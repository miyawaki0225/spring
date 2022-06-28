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
