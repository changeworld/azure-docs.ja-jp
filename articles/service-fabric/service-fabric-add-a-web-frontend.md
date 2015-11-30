<properties
   pageTitle="アプリケーション用の Web フロントエンドの作成 | Microsoft Azure"
   description="ASP.NET 5 Web API プロジェクトと ServiceProxy によるサービス間通信を使用して、Web に Service Fabric アプリケーションを公開します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/17/2015"
   ms.author="seanmck"/>

# アプリケーション用の Web サービス フロントエンドの構築

既定では、Service Fabric サービスは Web にパブリック インターフェイスを提供しません。HTTP クライアントにアプリケーションの機能を公開するには、エントリ ポイントとして機能する Web プロジェクトを作成し、そこから個々のサービスと通信する必要があります。

このチュートリアルでは、ステートフル サービスが既に含まれるアプリケーションに ASP.NET 5 Web API フロントエンドを追加する方法を説明します。「[Creating your first application in Visual Studio (Visual Studio での初めてのアプリケーションの作成)](service-fabric-create-your-first-application-in-visual-studio.md)」をまだ行っていない場合は、このチュートリアルを始める前に行うことをお勧めします。

## アプリケーションへの ASP.NET 5 サービスの追加

ASP.NET 5 は軽量のクロスプラットフォーム Web 開発フレームワークであり、最新の Web UI と Web API を作成できます。ASP.NET Web API プロジェクトを既存のアプリケーションに追加してみましょう。

1. ソリューション エクスプローラーで、アプリケーション プロジェクトを右クリックして、**[新しい Fabric サービス]** を選択します。

	![既存アプリケーションへの新しいサービスの追加][vs-add-new-service]

2. [新しいサービス] ダイアログ ボックスで、ASP.NET 5 Web サービスを選択し、名前を付けます。

	![新しいサービス ダイアログでの ASP.NET Web サービスの選択][vs-new-service-dialog]

3. 次のダイアログ ボックスでは、一連の ASP.NET 5 プロジェクト テンプレートが提供されます。Service Fabric アプリケーションの外部で ASP.NET 5 プロジェクトを作成した場合、これらは同じテンプレートであることに注意してください。このチュートリアルでは Web API を選択しますが、完全な Web アプリケーションの構築にも同じ概念を適用できます。

	![ASP.NET プロジェクトの種類の選択][vs-new-aspnet-project-dialog]

  作成した Web API プロジェクトでは、2 つのサービスがアプリケーションに含まれます。アプリケーションの作成を続けながら、まったく同じ方法でさらにサービスを追加し、個別にバージョン管理およびアップグレードできます。

## アプリケーションの実行

手順を理解してもらうため、新しいアプリケーションをデプロイし、ASP.NET 5 Web API テンプレートによって提供される既定の動作を見てみます。

1. Visual Studio で F5 キーを押してアプリをデバッグします。

2. デプロイメントが完了すると、Visual Studio はブラウザーを起動して ASP.NET Web API サービスのルートを開きます (例: http://localhost:33003、ポート番号はランダムに割り当てられるのでこれと異なる場合があります)。ASP.NET 5 Web API テンプレートではルートの既定の動作が提供されないので、ブラウザーでエラーが発生します。

3. ブラウザーの場所に `/api/values` を追加します。これにより、Web API テンプレートの ValuesController で `Get` メソッドが呼び出され、テンプレートによって提供される既定の応答である 2 つの文字列を含む JSON 配列が返されます。

  ![ASP.NET 5 Web API テンプレートから返される既定値][browser-aspnet-template-values]

  チュートリアルの最後では、これらの既定値をステートフル サービスからの最新のカウンター値で置き換えます。

## サービスへの接続

Service Fabric は、Reliable Services との通信方法において完全な柔軟性を提供します。1 つのアプリケーションに、TCP でアクセス可能なサービス、HTTP REST API でアクセス可能なサービス、Web ソケットでアクセス可能なサービスが含まれることがあります。使用可能なオプションおよびトレードオフについては、「[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)」を参照してください。このチュートリアルでは簡単な手法の 1 つに従い、SDK で提供される `ServiceProxy`/`ServiceCommunicationListener` クラスを使用します。

`ServiceProxy` の方法 (リモート プロシージャ呼び出しまたは RPC でモデル化) では、サービスに対するパブリック コントラクトとして機能するようにインターフェイスを定義し、そのインターフェイスを使用してサービスと対話するためのプロキシ クラスを生成します。

### インターフェイスの作成

最初に、ステートフル サービスとそのクライアントの間のコントラクトとして機能する、ASP.NET 5 プロジェクトを含むインターフェイスを作成します。

1. ソリューション エクスプローラーでソリューションを右クリックし、**[追加]、[新しいプロジェクト]** の順に選択します。

2. 左側のナビゲーション ウィンドウで Visual C# のエントリを選択し、**[クラス ライブラリ]** テンプレートを選択します。.NET Framework のバージョンが 4.5.1 に設定されていることを確認します。

  ![ステートフル サービス用のインターフェイス プロジェクトの作成][vs-add-class-library-project]

3. インターフェイスを `ServiceProxy` で使用できるようにするには、Service Fabric NuGet パッケージの 1 つに含まれる IService インターフェイスから派生する必要があります。パッケージを追加するには、新しいクラス ライブラリ プロジェクトを右クリックして、**[NuGet パッケージの管理]** を選択します。

4. **[プレリリースを含める]** チェック ボックスがオンになっていることを確認した後、**Microsoft.ServiceFabric.Services** パッケージを検索してインストールします。

  ![Services NuGet パッケージの追加][vs-services-nuget-package]

5. クラス ライブラリで、1 つのメソッド `GetCountAsync` を含むインターフェイスを作成し、IService からインターフェイスを拡張します。

  ```c# namespace MyStatefulService.Interfaces { using Microsoft.ServiceFabric.Services;

      public interface ICounter: IService
      {
          Task<long> GetCountAsync();
      }
  } ```

### ステートフル サービスでのインターフェイスの実装

インターフェイスを定義したので、次にステートフル サービスでそれを実装する必要があります。

1. ステートフル サービスで、インターフェイスを含むクラス ライブラリ プロジェクトへの参照を追加します。

  ![ステートフル サービスのクラス ライブラリ プロジェクトへの参照の追加][vs-add-class-library-reference]

2. `StatefulService` を継承するクラスを探し (`MyStatefulService` など)、それを拡張して `ICounter` インターフェイスを実装します。

  ```c#
  public class MyStatefulService : StatefulService, ICounter
  {        
        // ...
  }
  ``` 3.次に、`ICounter` インターフェイスで定義されている単一のメソッド `GetCountAsync` を実装します。

  ```c# public async Task<long> GetCountAsync() { var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string  long>>("myDictionary");

      using (var tx = this.StateManager.CreateTransaction())
      {          
          var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");
          return result.HasValue ? result.Value : 0;
      }
  } ```

### ServiceCommunicationListener を使用したステートフル サービスの公開

`ICounter` インターフェイスを実装した後、ステートフル サービスを他のサービスから呼び出すことができるようにする最後の手順は、通信チャネルを開くことです。ステートフル サービスの場合、Service Fabric が提供するオーバーライド可能な `CreateServiceReplicaListeners` という名前のメソッドでは、サービスで可能にする通信の種類に基づいて、1 つ以上の通信リスナーを指定できます。

>[AZURE.NOTE]ステートレス サービスへの通信チャネルを開く同等のメソッドは、`CreateServiceInstanceListeners` という名前です。

ここで提供する `ServiceCommunicationListener` は、`ServiceProxy` を使用してクライアントから呼び出すことができる RPC エンドポイントを作成します。

```c#
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (initParams) =>
                new ServiceCommunicationListener<ICounter>(initParams, this))
    };
}
```


### ServiceProxy を使用したサービスとの対話

ステートフル サービスが他のサービスからトラフィックを受信できるようになったので、残っているのは、ASP.NET Web サービスからそれと通信するためのコードを追加することだけです。

1. ASP.NET プロジェクトで、`ICounter` インターフェイスを含むクラス ライブラリへの参照を追加します。

2. 前にクラス ライブラリ プロジェクトで行ったのと同様に、ASP.NET プロジェクトに Microsoft.ServiceFabric.Services パッケージを追加します。これにより、`ServiceProxy` クラスが提供されます。

3. Controllers フォルダーで `ValuesController` クラスを開きます。現状では、`Get` メソッドはハード コーディングされた文字配列 "value1" と "value2" を返すだけであることに注意してください。これらは、先にブラウザーで見たものと一致します。この実装を次のコードに置き換えます。

  ```c# public async Task<IEnumerable<string>> Get() { ICounter counter = ServiceProxy.Create<ICounter>(0, new Uri("fabric:/MyApp/MyStatefulService"));

      long count = await counter.GetCountAsync();

      return new string[] { count.ToString() };
  } ```

  コードの最初の行が重要です。ステートフル サービスへの ICounter プロキシを作成するには、パーティション ID とサービス名の 2 つの情報を提供する必要があります。

  パーティション分割を使用すると、顧客 ID や郵便番号などの定義したキーに基づいて異なるバケットにステートを分割することにより、ステートフル サービスを拡張できます。この単純なアプリケーションではステートフル サービスはパーティションを 1 つしか持たないので、キーは重要ではありません。どのようなキーを提供しても同じパーティションになります。

  サービス名は、fabric:/&lt;アプリケーション名&gt;/&lt;サービス名&gt; の形式の URI です。

  これら 2 つの情報により、Service Fabric は要求送信先のコンピューターを一意に識別できます。また、`ServiceProxy` は、ステートフル サービス パーティションをホストしているコンピューターで障害が発生し、別のコンピューターを昇格させて引き継ぐ必要がある状況を、シームレスに処理します。この抽象化により、他のサービスを処理するクライアント コードの作成が大幅に簡略化されます。

  プロキシを作成した後は、`GetCountAsync` メソッドを呼び出して結果を返すだけです。

4. 再び F5 キーを押して、変更したアプリケーションを実行します。前と同じように、Visual Studio はブラウザーを自動的に起動して Web プロジェクトのルートを表示します。"api/values" パスを追加すると、返される現在のカウンター値がわかります。

  ![ブラウザーに表示されたステートフル カウンター値][browser-aspnet-counter-value]

  定期的にブラウザーを更新して、カウンターの値が更新されるのを確認します。


## アクターについて

このチュートリアルではステートフル サービスと通信する Web フロントエンドの追加に注目しましたが、非常によく似たモデルに従ってアクターと対話できます。実際にはもう少し簡単です。

アクター プロジェクトを作成すると、Visual Studio によってインターフェイス プロジェクトが自動的に生成されます。そのインターフェイスを使用して Web プロジェクトでアクター プロキシを生成し、アクターと通信できます。通信チャネルは自動的に提供されるので、このチュートリアルでステートフル サービスに関して行ったような `ServiceCommunicationListener` の確立に相当するようなことは何も行う必要がありません。

## ローカル クラスターでの Web サービスの実行

一般に、ローカル クラスターは 5 ノードの構成を 1 つのコンピューターにまとめただけなので、ローカル クラスターにデプロイした複数コンピューター クラスターにまったく同じ Service Fabric アプリケーションをデプロイでき、意図したとおりに動作することを信頼できます。

ただし、Web サービスに関しては 1 つの重要な違いがあります。クラスターが Azure でのようにロード バランサーの背後に配置されている場合、ロード バランサーはコンピューター間にトラフィックをラウンドロビンするだけなので、Web サービスをすべてのコンピューターにデプロイする必要があります。これは、サービスの `InstanceCount` に特別な値 -1 を設定することによって実現できます。一方、ローカルで実行するときは、実行するサービスのインスタンスを 1 つだけにする必要があります。そうしないと、同じパスとポートでリッスンしている複数のプロセスが競合します。そのため、ローカルな deployments.git の場合は Web サービスのインスタンス数を 1 に設定する必要があります。

異なる環境で異なる値を構成する方法については、「[複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」を参照してください。

## 次のステップ

- [Azure にクラスターを作成してクラウドにアプリケーションをデプロイします](service-fabric-cluster-creation-via-portal.md)
- [サービスとの通信について学習します](service-fabric-connect-and-communicate-with-services.md)
- [ステートフル サービスのパーティション分割について学習します](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png

<!---HONumber=Nov15_HO4-->