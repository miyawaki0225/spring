## SpringBoot入門
https://masahiroharada.github.io/spring-boot-primer/#%E7%9B%AE%E7%9A%84%E3%83%BB%E5%AF%BE%E8%B1%A1

## Tymeleafチートシート
https://qiita.com/NagaokaKenichi/items/c6d1b76090ef5ef39482

# Postgresql
[説明書](https://www.postgresql.jp/document/13/html/index.html)

### 開発環境構築
この章では、開発に必要なソフトウェアのインストール方法を紹介します。

あらかじめ Java および PostgreSQL はインストールしておいてください。
- STS（Spring Tool Suite）
- Eclipseプラグイン
- データベースクライアント
- [Postgresql](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)

[pgAdmin4](https://works.forward-soft.co.jp/blog/detail/10118)

- [Emmet](https://design.nagomi-reha.com/emmet/)
https://zenn.dev/miz_dev/articles/6cac5f2e32398d

## WAFの仕組み
コードを書く前に WAF（Web Application Framework）について説明します。  

### フレームワークとは
EC サイトでも SNS でもニュースサイトでも求人サイトでも、Web アプリケーションに共通して必要な処理があります。
例えば…  
1. リクエスト URL に基づいて特定の処理をよびだす
2. ユーザーの認証を行う
3. 入力値のバリデーションを行う
4. データベースに接続する
5. テンプレートにデータを流し込みHTMLを生成する
6. レスポンスメッセージを作成する
などです。

これらの共通処理をまとめたプログラム群が Web アプリケーションフレームワークと呼ばれます。フレームワークが提供する共通処理部分を自前で書く必要がなくなるので生産性が高くなります。  

### WAF のパターン
WAF はプロダクトによってそれぞれ書き方やクラスの呼び名などが少しづつ違いますが、基本となるパターンは似ています。

1. アプリケーションが HTTP リクエストを受け取る。
2. リクエスト URL ごとの処理が実行される前に認証チェックなどの共通処理が実行される。この処理はたいてい「ミドルウェア」と呼ばれる。
3. リクエスト URL およびリクエストメソッドに従って、あらかじめ紐づけしておいたメソッドが実行される。
  1. 紐づけというのは例えば「GET で /profile がリクエストされた時は ProfileController の show メソッドを呼ぶ」という設定のこと。この紐づけ設定を「ルーティング」と呼ぶ。設定ファイルもしくはアノテーションで定義することが多い。また、ルーティング先のクラスのことを「コントローラー」と呼ぶ。
  2. 記で呼ばれたメソッド内の処理は要件次第だが、データベースを操作する際はたいてい「Object-Relational マッパー（後述）」が用いられる。
4. レスポンスを返却する際、コンテンツが HTML であれば HTML 生成のために「テンプレート（エンジン）」が用いられる。
このパターンを習得してしまえば今まで使ったことがなかったフレームワークもある程度スムーズに理解することができます。
  
### 様々な WAF
言語によっていくつかの WAF が開発されています。

機能は様々で、上記のパターンの機能すべてを装備したフレームワークもあれば、ルーティングの機能だけを備えたミニマルなフレームワークも存在します。

### 初期設定
![spring tool](spring tool初期設定.PNG)

- SQL
  - JDBC：データベースを操作する
  - MyBatis：OR マッパー（後述）
  - PostgreSQL：PostgreSQL に接続するため
  - Flyway：マイグレーション（後述）
- Template Engines
  - Thymeleaf：テンプレートエンジン（後述）
- Web
  - Web：ルーティングなど Web アプリの基本機能

### コントローラークラス
1. クラスに「@Controller」を付ける。
2. メソッドに「@GetMapping」「@PostMapping」を付けて リクエスト URL + HTTP メソッドを紐付ける。
というのが Spring におけるルーティングのパターンです。


### URL設計
|URL	|Method	|ContentType	|目的|
|---  |---    |---          |---|
|/	                  |GET	|HTML	|TOP 画面               |
|/create	            |GET	|HTML	|メンバー追加 画面      |
|/create	            |POST	|HTML	|メンバー追加 実行      |
|/api/members	        |GET	|JSON	|すべてのメンバーを返す  |
|/api/members/{words}	|GET	|JSON	|wordsでメンバーを検索  |

下の2つの`/api/〜`は、Ajax用でJSONをレスポンスします。

### DB マイグレーションとは
DBマイグレーションとは、テーブル定義を管理する仕組みのことです。  

アプリケーションを開発するにあたって、テーブル定義はしばしば変更されるものです。実装着手前の設計段階で変更のない完璧なテーブル定義を作成することは不可能です。初期フェーズの開発中においてさえ設計の変更（テーブルやカラムの追加）は発生しますし、リリースした後もアプリケーションの成長（＝機能追加、仕様変更）に合わせてテーブル定義も当然変化します。  
そこで、変遷するテーブル定義を管理する必要が出てきます。DB マイグレーションツールはたいてい、どのような SQL をどの順番で実行したかを管理します。そして実行される SQL 文[^1]はアプリケーションのプロジェクトディレクトリに含まれます。つまりアプリケーションコードと同様にバージョン管理下に置かれ共有されるということです。  
  
ある環境にアプリケーションをリリースするときやチームに新しいメンバーを迎えるとき、このようにテーブル定義の管理がされていると、データベースをあるべき状態に再現することが容易になります。  
[^1]: マイグレーションツールによっては SQL 文ではなく、独自のクラスや設定ファイルでテーブルの状態や変更を表現する場合もあります。  

### Flyway
DB マイグレーションを実現するためのライブラリはいくつかあるようですが、今回はその中で Flyway というライブラリを使用します。

### 設定
#### application.properties
最後の行の`spring.flyway.enabled`に`true`を指定します。

※データベース名、ユーザー名、パスワードを要確認
```java
spring.datasource.url=jdbc:postgresql://localhost:5432/spring-demo
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.datasource.driverClassName=org.postgresql.Driver

spring.flyway.enabled=true
```

### SQL
`src/main/resources`の下に`db/migration`というディレクトリを作成してください。
そしてそのなかに`V1__Create_members.sql`というファイルを作成してください。Flyway の仕様としてこのファイル名が重要な意味を持つので注意しましょう。

```java
V{バージョン番号}__{任意の名前}.sql
```

[初期テーブル](初期設定テーブル.PNG)

### Object-Relational マッピングとは
Object-Relational マッピング（ORM）とは、アプリケーションからデータベースの操作をしやすくするためのプログラミング手法です。  
オブジェクト指向モデルであるアプリケーション（例：Java）とリレーショナルデータベース（例：PostgreSQL）では、そもそもデータの持ち方が違います。  
オブジェクト指向では、クラスがあってプロパティにデータを持ちます。例えば以下のように。

```java
public class User{
  private String email;
  private String password;
}
```

```console
//一方リレーショナルモデルでは行と列、つまりテーブルでデータを持ちます。
users テーブル
| email           | password |
|-----------------|----------|
| sample@mail.com | test1234 |
```

例えば`users`テーブルから取得したデータを、毎回`User`クラスに詰めなおすのは面倒ですね。`User`クラスのデータを`user`テーブルに挿入したい場合も、いちいちプロパティからデータを引っ張ってきて`INSERT`文を作るのは面倒です。そこであらかじめアプリ側のデータとテーブル側のデータの紐づけを定義しておこうというのが`ORM`です。

### ドメインクラス
まず Java 側でデータを入れる箱になるクラスを用意しましょう。このような役割を持つクラスをドメインクラスやエンティティクラス、モデルクラスなどと呼びます。  
`com.example.search`配下に`domains`パッケージを新たに作成してください。そしてそこに`Member.java`クラスファイルを作成しましょう。

```java
package com.example.search.domains;

public class Member {
    private int id;
    private String name;
    public Member(int id, String name) {
        this.id = id;
        this.name = name;
    }
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

ドメインクラスひとつがテーブルひとつに対応するように作ればいいのか、と考えた方もいるでしょうか。基本的にはその通りです。  
複雑なアプリケーションになるともちろん例外も出てきますが、まずはテーブルに対応するドメインクラス（またはエンティティまたはモデル）を作成する認識で OK です。

### MyBatis
ORM ライブラリも言語やフレームワークによっていろいろ存在します。Java の世界でもいくつか選択肢がありますが、今回は MyBatis というライブラリを使用します。
ここからは MyBatis での実装方法です。ライブラリが変われば実装方法も変わります（ドメインクラスはどのような ORM ライブラリでも大体必要になる）。
また、まずは member テーブルの一覧を取得する機能から実装していく前提で進めます。

#### Mapper インターフェース
JavaからSQLを発行するための仲介役となるインターフェースおよびメソッドを定義します。
どういうことかというと、
```java
db.exec("SELECT * FROM members");
```
  
ORM の考え方では上記のような書き方はしないのです。リレーショナルの世界の言葉（SQL）がオブジェクト指向の世界（Java）に入り込んでしまっていて良くないと考えられます。
その代わり Mapper を介して、
  
```java
memberMapper.all();
```

インターフェースを作成


### XMLでのマッピング
MyBatis では「アプリ側のデータとテーブル側のデータの紐づけを定義」を XML ファイルで行います。  
src/main/resources の下にさらに com/example/search/mappers というディレクトリ[^1]を作成します。ディレクトリができたらその中に MemberMapper.xml というファイルを作成し、以下の内容を記述してください。  
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.search.mappers.MemberMapper">
    <!-- 結果と結果を受け取るクラスの紐づけ -->
    <resultMap id="memberResultMap" type="com.example.search.domains.Member">
      <result property="id" column="id" />
      <result property="name" column="name"/>
    </resultMap>
    <!-- SQL -->
    <select id="all" resultMap="memberResultMap">
        SELECT * FROM members
    </select>
</mapper>
```

[^1]: ディレクトリが重要なので間違わないように注意してください。つまり Mapper インターフェースのパッケージ名「com.example.search.mappers」に合わせたディレクトリ階層にするということです。このルールを守ることで自動的に XML ファイルを検出して読み込んでくれるらしいです。設定で変更することもできるようですが今回は MyBatis の詳細な設定方法までは追いません。  


`<mapper>`
まずルート要素は`<mapper>`です。`namespace`属性に対応するインターフェースの名前空間を記述しましょう。

`<mapper>`の子要素が2つありますね。`<resultMap>`と`<select>`です。

`<resultMap>`
`<resultMap>`は「クエリ結果のどのカラムとクラスのどのプロパティが紐づくか」を定義します。

id属性は後で他の要素から参照するときに使用するIDです。  
type 属性はアプリ側のデータの入れ物クラス＝ドメインクラスの型名です。com からの完全修飾名でなくてはいけませんので注意してください。
`<resultMap>`の中には`<result>`が並びます。`property`と`column`という2つの属性を持っています。

- property 属性にはクラスのプロパティ名を記述します。
- column 属性にはクエリ結果のカラム名を記述します。

`<select>`
`<select>`は「MapperのメソッドとSQLの紐づき」を定義します。

`id`と`resultMap`という2つの属性が定義されていますね。

`id`属性には`Mapper`側のメソッド名を記述します。  
`resultMap`属性には`SELECT`文の結果をどのクラスにどのように紐づけるかを定義します。  
ここで`<resultMap>`の`id`を参照するわけです。
`<select>`の中には実行したいSQL文（`<select>`なので`SELECT`文）を記述します。

[my batis](https://mybatis.org/mybatis-3/ja/sqlmap-xml.html#Result_Maps)
  
### 処理の流れ
1. memberMapper.all() を実行する。
2. XML で定義された通り、\<select id="all"> の中の SQL（SELECT * FROM members）が発行される。
3. クエリの結果が返ってくる。クエリの結果をどのようにマッピングするかは、\<select> の resultMap 属性に書いてある。ここでは memberResultMap と記述されているので、id が memberResultMap である \<resultMap> 要素を参照する。
4. \<resultMap id="memberResultMap"> を参照すると、クエリ結果の`id`を`Member`クラスの`id`プロパティに、クエリ結果の`name`を`name`プロパティに紐づけるよう定義されている。
5. 定義の通りにクエリの結果データが格納されたクラスのインスタンスが生成される。クエリの結果行ごとにインスタンスは生成される。
6. `Mapper`では`all`の戻り値は`List<Member>`と定義しているため、`Member`インスタンスが結果行数ぶん入った`List`が`all`メソッドの呼び出し元に返却される。

文字にすると一見ややこしいようですがロジックやアルゴリズムの問題ではなく、ライブラリのルールとしてどことどこが紐づくかというパズルでしかないので落ち着いて取り組んでみてください。  

### コントローラーを作成する
このページではコントローラーを作成します。
`com.example.search.controllers`に`MemberController.java`を追加してください。
まずは TOP 画面から作成します。

### @Autowired
まず最初のポイントは @Autowired アノテーションです。  

前のページで作成した`MemberMapper`を使用したいわけですが、直接`new`でインスタンス化はしません（そもそも`MemberMapper`はインターフェースですので`new`できません）。代わりにDI（Dependency Injection：依存性の注入）という方法を使います。

`DI`はかなり難しいオブジェクト指向のプログラミング手法なので、今すぐに覚えなくて構いません。ただ自分で`new`しない方法もある[^1]と知っておいてください。
さて @Autowired をコンストラクタに付与すると、コンストラクタを実行するときに自動的に引数の型のクラスのインスタンスがコンストラクタに渡されます。

今回の例では、`MemberController`のインスタンスが生成されるとき、コンストラクタに`MemberMapper`のインスタンスが渡されるということです。これを`MemberController`のプロパティである`memberMapper`に代入しています。


### テンプレートへのデータの受け渡し
次のポイントは、テンプレートへのデータの受け渡しです。   
Hello world! の例ではただシンプルな画面を返すだけでしたが、今回はコントローラーで取得したデータをテンプレートに渡す必要があります。
まずは前のページで定義した all メソッドでメンバーの一覧を取得します。  

### テンプレートを追加する
このページでは TOP ページのテンプレートファイルを追加します。  
ちなみに Bootstrap という CSS フレームワークを使用しています。  

### テンプレートエンジンとは
さてここまでは普通の HTML ですが、ここからテンプレートエンジン Thymeleaf（タイムリーフ）を使っていきます。  
テンプレートとはアプリケーションがレスポンスする HTML の雛形で、制御構文や変数の展開を記述することができます。雛形が同じでもデータを変えることで別のページを作り出せる仕組みですね。  
テンプレートエンジンとはテンプレートを HTML に変換するライブラリです。テンプレートエンジンが変わればテンプレートの書き方も変わってきます。例えば JSP もテンプレートエンジンの一種でしょう。今回は Spring と一緒に使われることが多い Thymeleaf というライブラリを使用します。  

### 属性の宣言
Thymeleaf を使うために、まずは \<html> タグに記述を追加します。
```html
<html lang="ja" xmlns:th="http://www.thymeleaf.org">
```

`th:each`と`th:text`に注目してください。`th:xxx`というのが`Thymeleaf`の属性です。

### 変数の例

```java
String[] numbers  = {"one","two","three"};
model.addAttribute("data",numbers);
```

```html
<p th:each="item : ${data}>
   <span th:text="item"></span>
</p>
```

練習用コントローラー
```java:Controller
@Controller
public class CodePracticeController {
	@GetMapping("/code_practice")
	public String index(Model model) {
		String[] array = {"one","two","three"};
		model.addAttribute("array",array);
		return "code_practice";
	}
}
```

上記を表示する
```html:CodePractice.html
<body>
	<p th:each="moji:${array}">
		<span th:text="${moji}"></span>
	</p>
</body>
```
※$を付けないと`moji`が繰り返し記載される。
