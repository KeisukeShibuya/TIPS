# H2データベースとMyBatisの導入

以下はH2とMyBatis3を利用するための設定例と、簡単な動作確認用サンプルコードです。

以下の変更・追加が必要です。

1.  【pom.xml】  
 MyBatisスタータ―依存関係を追加してください。

```xml
<!-- pom.xml (MyBatis依存関係追加部分) -->
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>3.0.1</version>
</dependency>
```

2.  【application.properties】  
 H2接続設定とMyBatisのmapper XML配置パスの指定を追加します。

```properties
# src/main/resources/application.properties
spring.datasource.url=jdbc:h2:mem:demo;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=
spring.h2.console.enabled=true

mybatis.mapper-locations=classpath:mapper/*.xml
```

3.  【DB初期化ファイル】  
 H2のテーブル作成とダミーデータ追加用にschema.sqlを作成してください。

```sql
-- src/main/resources/schema.sql
CREATE TABLE sample (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(50)
);
INSERT INTO sample(name) VALUES ('Sample Data');
```

4.  【エンティティクラス】  
 MyBatisで扱うデータを格納するシンプルなモデルクラスを作成します。

```java
// src/main/java/com/example/demo/model/Sample.java
package com.example.demo.model;

public class Sample {
    private int id;
    private String name;

    // getter / setter
    public int getId() {
        return id;
    }
    public void setId(int id) {
        this.id = id;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

5.  【Mapperインタフェース】  
 MyBatisのMapperインタフェースを作成し、DBからデータを取得するメソッドを定義します。

```java
// src/main/java/com/example/demo/mapper/DemoMapper.java
package com.example.demo.mapper;

import org.apache.ibatis.annotations.Mapper;
import com.example.demo.model.Sample;

@Mapper
public interface DemoMapper {
    Sample selectSampleById(int id);
}
```

6.  【Mapper XML】  
 MapperのSQL定義をXMLで設定します。

```xml
<!-- src/main/resources/mapper/DemoMapper.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.DemoMapper">
    <select id="selectSampleById" resultType="com.example.demo.model.Sample">
        SELECT id, name FROM sample WHERE id = #{id}
    </select>
</mapper>
```

7.  【動作確認用コントローラー】  
 MyBatisのMapperを利用してDBデータを取得し、画面に表示するエンドポイントを追加します。

```java
// src/main/java/com/example/demo/controller/DemoController.java
package com.example.demo.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import com.example.demo.mapper.DemoMapper;
import com.example.demo.model.Sample;

@Controller
public class DemoController {

    @Autowired
    private DemoMapper demoMapper;

    @GetMapping("test")
    public String test(Model model) {
        // DBからid=1のデータを取得
        Sample sample = demoMapper.selectSampleById(1);
        model.addAttribute("sample", sample);
        return "test";
    }
}
```

8.  【テンプレートファイルの更新】  
 取得した値を表示するために、テンプレートファイルも変更します。

```html
<!-- src/main/resources/templates/test.html -->
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>テストページ</title>
</head>
<body>
    <p>ID: <span th:text="${sample.id}"></span></p>
    <p>Name: <span th:text="${sample.name}"></span></p>
</body>
</html>
```

以上の設定により、H2データベースとMyBatis3が連携し、DBに登録したデータがブラウザ上で確認できるようになります。