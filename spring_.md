[spring リファレンス](https://spring.pleiades.io/spring-boot/docs/current/reference/html)

## Beans  
 
>Bean は、Spring によって管理されるクラスのオブジェクトです。従来、オブジェクトは独自の依存関係を作成するために使用されていましたが、
>Springはオブジェクトのすべての依存関係を管理し、必要な依存関係を挿入した後にオブジェクトをインスタンス化します。
>@Componentアノテーションは、Bean を定義する最も一般的な方法です。

```java
@Component
public class Vehicle {
}
```

## Autowiring
Operator
>依存関係を識別し、一致するものを探し、依存関係を設定するプロセスは、自動配線と呼ばれます。@Autowired注釈は、Spring に、コラボレーションする Bean を見つけて別の Bean に注入するように指示します。同じタイプの Bean が複数ある場合、Spring はエラーをスローします。次のシナリオでは、2 つのタイプの Bean が Spring によって検出されます。

Arithmetic
>Spring は、開発者が明示的に指定しない限り、どの Bean を Bean に注入すべきかを知りません。

```java
@Component
class Arithmetic(){
  @Autowired
  private Operator operator;
  //...
}

@Component
class Addition implements Operator{}

@Component
class Subtraction implements Operator{}
```

## Dependency injection（依存性の注入）
>依存性注入は、Spring が特定の Bean が機能するために必要な Bean を検索し、それらを依存関係として注入するプロセスです。Spring は、コンストラクターまたはセッターメソッドを使用して依存関係の挿入を実行できます。


## Inversion of Control
Engine Vehicle
>伝統的に、依存関係を必要とするクラスは依存関係のインスタンスを作成しました。クラスは、依存関係を作成するタイミングと作成方法を決定しました。たとえば、class は class の依存関係であり、そのオブジェクトが作成されます。
```java
//旧来のjavaの方法　Vehicle →　Engine
class Vehicle{
  private Engine engine = new Engine();
  //...
}
```
>Spring はこの責任をクラスから引き受け、オブジェクト自体を作成します。開発者は単に依存関係に言及し、フレームワークが残りの面倒を見ます。
```java
//spring版　Vehicle　←　Engine
class Vehicle{
  private Engine engine;
  //...
}
```
>したがって、制御は依存関係を必要とするコンポーネントからフレームワークに移動します。フレームワークは、コンポーネントの依存関係を見つけ出し、その可用性を確保し、コンポーネントに挿入する責任を負います。このプロセスは、制御の反転と呼ばれます。

## IoC container
>IoC コンテナーは、制御の反転機能を提供するフレームワークです。
>IoC コンテナーは Bean を管理します。上記の例では、クラスのインスタンスを作成し、クラスのインスタンスを作成してから、オブジェクトを依存関係としてオブジェクトに挿入します。EngineVehicleEngineVehicle
>IoC コンテナーは一般的な用語です。フレームワーク固有ではありません。Spring には、IoC コンテナーの 2 つの実装が用意されています。

1. Beans Factory（メモリが少ない場合利用する）
2. Application Context

## Bean factory
>Spring IoC コンテナーの基本バージョンは Bean ファクトリです。
>これはレガシー IoC コンテナーであり、Bean の基本的な管理と依存関係の配線を提供します。
>Springでは、下位互換性を提供するためにBeanファクトリーがまだ存在します。


## Application context
>アプリケーション・コンテキストは、エンタープライズ・アプリケーションで通常必要とされるより多くの機能を Bean ファクトリーに追加します。これは、Springフレームワークの最も重要な部分です。Springのすべてのコアロジックはここで起こります。これには、Bean ファクトリーによって提供される Bean の基本的な管理と依存関係の配線が含まれます。アプリケーションコンテキストの追加機能には、Spring AOP機能、国際化、Webアプリケーションコンテキストなどが含まれます。


【依存関係で指定したモジュールについて】  
- Spring Boot DevTools・・・Spring Bootの開発ツールです。Javaコード、HTMLファイル、プロパティファイルなどの修正を検知して実行中のアプリに即時反映（ホットデプロイ）してくれるなど、開発に便利なツールが含まれています。
- Thymeleaf（タイムリーフ）・・・Spring Bootで標準的に使われるHTMLのテンプレートエンジンです。※Spring BootではJSPの使用は非推奨となっています。
- Spring Web・・・Spring MVCを使ったWebアプリケーションが作成可能となります。
- Lombok（ロンボック）・・・定型コード記述の省力化に役立つJavaアノテーションライブラリです。
 
【作成したプロジェクト内のファイルについて（下記イメージ参照）】  
- SpringMvc1Application.java・・・mainメソッドが宣言されているクラスです。
- SpringBootの起動はこのクラスから行います。@SpringBootApplicationアノテーションが定義されています。
- application.properties・・・Spring Bootのプロパティファイルです。
- pom.xml・・・Mavenの設定ファイルです。Mavenは、Javaプログラムをビルドするためのツールです。様々な機能がありますが、特に便利な機能は、アプリケーションが利用するライブラリの自動取得です。

```java
@Controller・・・このクラスをコントローラとして機能させる場合に指定します。
@GetMapping・・・アノテーション付与によりHTTPのGETリクエストを受け付けます。ここでは"http://localhost:8080/form"のGETリクエストを受け付けます。
@PostMapping・・・アノテーション付与によりHTTPのPOSTリクエストを受け付けます。ここでは"http://localhost:8080/form"のPOSTリクエストを受け付けます。
@ModelAttribute・・・モデル属性にバインドします。バインドとは、日本語で「結び付ける」「関連付ける」などの意味です。ここでは、入力画面の「氏名」が<input type="text" name="name">の場合、リクエストを受け付けたタイミングでSpringが自動でUserクラスのnameプロパティに画面入力値を設定してくれます。これは、データバインディングと呼ばれ、パラメータ取得コードの記述が不要となります。
 ```
 
 ```java
//<html xmlns:th="http://www.thymeleaf.org">・・・この宣言により、Thymeleafのタグが利用可能となる。
//th:action・・・フォームのPOST先を、@{パス名} で指定します。@{...}はリンクURL式、${...}は変数式といいます。
//th:object・・・フォームにバインドするオブジェクトを設定します。今回はコントローラ側で用意した"user"オブジェクトを設定しています。*{フィールド名}は選択変数式で、th:objectが付いたタグ内では、オブジェクト名の記述を省略できます。th:objectを使用しない場合、<input type="number" th:field="${user.age}">のように&{オブジェクト名.フィールド名}を指定する必要があります。
//th:field・・・フィールドを設定します。これは、<input>タグのid・name・value属性をHTMLに出力する機能です。
//<input type="text" th:field="*{name}">の記述は、<input type="text" id="name" name="name" value="">と出力されます。
//th:inline="text"・・・タグ内のテキストを展開します。[[ ]]で囲むと、その値を表示できます。
 ```
