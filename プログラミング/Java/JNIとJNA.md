## JNI と JNA（＋Panama）まとめ：未定段階でも判断できる具体化ガイド

Java から C/C++ などのネイティブ（DLL/.so/.dylib）を呼ぶ代表的な手段は **JNI** と **JNA**、そして近年の選択肢として **Panama（Foreign Function & Memory API）** です。  
「何を優先するか（速さ・作り込み・手軽さ・将来性）」で選び方が変わります。

---

# 1. まず結論（使い分け地図）

- **まず動かしたい／既存DLLを呼ぶだけ／試作が目的** → **JNA**
- **性能が重要／複雑な相互運用（構造体・コールバック・スレッド）／長期運用で堅牢に** → **JNI（薄いJNI層）**
- **将来のJDK標準寄りでやりたい（JDK縛りOK）** → **Panama**
- **高頻度呼び出し（オーバーヘッドが支配的）** → **JNI or Panama** ＋「呼び出し粒度を大きくする」設計

---

# 2. 比較表（概要）

| 観点 | JNI | JNA | Panama |
|---|---|---|---|
| 導入 | 重い（C/C++側の実装・ビルド必須） | 軽い（Java宣言中心） | JDK機能（ただしJDK版の影響を受ける） |
| 速度 | 速くしやすい | 遅くなりがち（マーシャリング等） | 良好が期待される（設計次第） |
| 自由度 | 非常に高い | 呼び出し中心（複雑化で辛いことも） | 呼び出し中心（メモリモデルあり） |
| 保守 | 設計次第（薄い層が定石） | 簡単だがハマると原因特定が難しい | JDKバージョン管理が鍵 |
| 配布 | ネイティブ同梱が必要 | 同左 | 同左 |

---

# 3. パターン別：どれを選ぶか

## パターンA：既存のDLL/.soを“とりあえず呼びたい”
- 推奨：**JNA**
- 理由：Java側に宣言を書くだけで呼べる。ネイティブの橋渡しコード不要。

## パターンB：自分でC/C++も書ける、プロダクトとして堅牢にしたい
- 推奨：**JNI**
- 理由：型変換、例外、スレッド、リソース管理を「自分で正確に制御」できる。

## パターンC：OS APIを叩く（Windows/macOS/Linux）
- 軽く叩く／検証：**JNA**
- 重要機能として長期運用：**JNI**（または OS向けの別手段）

## パターンD：コールバック（ネイティブ→Java）がある
- 推奨：**JNIが堅い**（JNAでも可能だが条件が増える）
- 理由：ネイティブスレッドからJavaへ入るときの制御（スレッドアタッチ等）が重要。

## パターンE：高頻度・低レイテンシ（呼び出し回数が多い）
- 推奨：**JNI / Panama**
- 定石：細かく何度も呼ばず、処理をまとめて粒度を大きくする。

## パターンF：将来性（JDK標準寄り）
- 推奨：**Panama**（採用JDKを固定できるなら）
- 注意：JDKバージョン差分の影響を受けやすい。

---

# 4. 具体例（最小イメージ）

## 4-1. JNA：既存ライブラリの関数を呼ぶ（単純関数）
C側に `int add(int a, int b)` がある想定。

```java
import com.sun.jna.Library;
import com.sun.jna.Native;

public interface MyLib extends Library {
    MyLib INSTANCE = Native.load("mylib", MyLib.class);
    int add(int a, int b);
}

// 使う側
int r = MyLib.INSTANCE.add(2, 3);
```


### JNA：構造体（struct）例
C側（想定）：`Point { int x; int y; }` を `Point*` で渡す。

```java
import com.sun.jna.Structure;
import java.util.List;

public interface MyLib extends com.sun.jna.Library {
    MyLib INSTANCE = com.sun.jna.Native.load("mylib", MyLib.class);

    class Point extends Structure {
        public int x;
        public int y;

        @Override
        protected List<String> getFieldOrder() {
            return List.of("x", "y");
        }

        public static class ByReference extends Point implements Structure.ByReference {}
    }

    int move(Point.ByReference p, int dx, int dy);
}
```


### JNA：コールバック例（native→Java）
```java
import com.sun.jna.Callback;

public interface MyLib extends com.sun.jna.Library {
    MyLib INSTANCE = com.sun.jna.Native.load("mylib", MyLib.class);

    interface OnEvent extends Callback {
        void invoke(int code);
    }

    void register_callback(OnEvent cb);
}
```


注意：コールバックは **GCされないよう参照を保持**する（フィールドに持つ等）。

---

## 4-2. JNI：最小構成（Java側→C実装→共有ライブラリ）

### Java側
```java
public class NativeAdder {
    static {
        System.loadLibrary("nativeadder");
    }

    public static native int add(int a, int b);

    static void main(String[] args) {
        System.out.println(add(2, 3));
    }
}
```


### ヘッダ生成
```shell script
javac -h native src/NativeAdder.java
```


### C側（ヘッダに従って実装）
```textmate
#include <jni.h>
#include "NativeAdder.h"

JNIEXPORT jint JNICALL Java_NativeAdder_add(JNIEnv *env, jclass cls, jint a, jint b) {
    return a + b;
}
```


#### 保守の定石：「薄いJNI層」
JNI関数では変換・境界チェック・例外変換程度に留め、重いロジックは純C/C++側へ寄せると安全・保守的です。

---

## 4-3. Panama：関数シンボルを見つけて呼ぶ（概念イメージ）
`add(int,int)` を呼ぶ方向性の例（APIはJDK版で差分が出る点に注意）。

```java
import java.lang.foreign.*;
import java.lang.invoke.MethodHandle;
import static java.lang.foreign.ValueLayout.JAVA_INT;

public class PanamaAdder {
    static void main(String[] args) throws Throwable {
        Linker linker = Linker.nativeLinker();
        SymbolLookup lookup = SymbolLookup.loaderLookup();

        MemorySegment addSym = lookup.find("add")
                .orElseThrow(() -> new UnsatisfiedLinkError("add not found"));

        FunctionDescriptor fd = FunctionDescriptor.of(JAVA_INT, JAVA_INT, JAVA_INT);
        MethodHandle add = linker.downcallHandle(addSym, fd);

        int r = (int) add.invokeExact(2, 3);
        System.out.println(r);
    }
}
```


Panamaの現実ポイント：
- 「どのJDKで使うか」を決めてから具体コードを固めるのが安全
- ネイティブ配布（OS/CPU別）は結局必要

---

# 5. 落とし穴（方式を問わず重要）

## 5-1. 文字列（encoding / wchar_t）
- `char*` が UTF-8とは限らない（Windowsの古い資産は特に注意）
- `wchar_t*` はOSでサイズ差がある（Windows=2byte、Linuxで4byteが多い）
- **返却文字列の所有権**（誰がfreeするか）が曖昧だと事故る

## 5-2. 構造体レイアウト（pack/alignment/longサイズ差）
- `#pragma pack`、アラインメント、`long` のサイズ差（Windows vs Linux）で破綻しやすい
- bitfield、可変長要素は難易度高

## 5-3. 呼び出し規約・シンボル名（特にWindows）
- cdecl/stdcall 不一致でクラッシュ
- エクスポート名が装飾されている場合がある

## 5-4. スレッド（ネイティブスレッド→Javaコールバック）
- JNI はスレッドアタッチが必要
- JNA でもコールバックが別スレッドで来ると設計上の注意が必要
- スレッドセーフでない DLL を並列に叩くと壊れる

## 5-5. エラー処理（戻り値/errno/GetLastError）
- Cはエラーコード中心、Javaは例外中心
- 境界層で「例外に変換するルール」を作ると保守性が上がる

## 5-6. リソース管理（メモリ・ハンドル・参照）
- “誰が確保し誰が解放するか”を契約として固定しないと破綻
- Java側は `AutoCloseable` などでライフサイクルを明示すると良い

## 5-7. 配布（OS/CPU別、署名、ロードパス）
- 多OS/多CPU対応はネイティブが最大のコスト要因
- macOSの署名/公証、WindowsのDLL探索パス、Linuxのrpath等も絡む

---

# 6. まとめ（未定段階の“現実的な順序”）
1. **試作・検証**：まず **JNA** で「呼べるか／構造体が地獄か」を見極める
2. **本番判断**：性能・複雑さ・長期運用が重いなら **JNI（薄い層）**へ寄せる
3. **JDK戦略が固いなら**：**Panama** を検討（将来の標準寄り）
4. どれでも最重要は「文字列」「構造体レイアウト」「所有権」「スレッド」「配布」を最初に設計する
