---
title: "Service Fabric の .NET アプリケーションを作成する | Microsoft Docs"
description: "ASP.NET Core のフロントエンドとリライアブル サービスのステートフルなバックエンドを含むアプリケーションを作成し、クラスターにアプリケーションをデプロイする方法を説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8f824e451abd2ca87875c416184e4d7265e8c72b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>ASP.NET Core Web API フロントエンド サービスとステートフルなバックエンド サービスを含むアプリケーションを作成およびデプロイする
このチュートリアルでは、ASP.NET Core Web API フロント エンドとステートフルなバックエンド サービスを含む Azure Service Fabric アプリケーションを作成し、データを格納する方法を説明します。

![アプリケーション ダイアグラム](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core Web API サービスをリライアブル サービスとして作成する
> * ステートフル リライアブル サービスの作成
> * サービス リモート処理とサービス プロキシの使用の実装

チュートリアルは 3 つの記事に分かれています。この記事は 1 つ目です。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の条件を満たす必要があります。
- Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- [Visual Studio 2017 をインストール](https://www.visualstudio.com/)し、**Azure 開発**ワークロードと **ASP.NET および Web 開発**ワークロードをインストールします。
- [Service Fabric SDK をインストール](service-fabric-get-started.md)します。

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API サービスをリライアブル サービスとして作成する
ASP.NET Core は軽量のクロスプラットフォーム Web 開発フレームワークであり、これを使用すると、最新の Web UI と Web API を作成できます。 Service Fabric と ASP.NET Core を統合する方法を十分に理解するには、「[Service Fabric リライアブル サービスでの ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)」の記事全体を読むことを強くお勧めします。 ここでは、このチュートリアルに従って、すぐに開始することができます。 ASP.NET Core の詳細については、[ASP.NET Core のドキュメント](https://docs.microsoft.com/aspnet/core/)を参照してください。

> [!NOTE]
> このチュートリアルは、[Visual Studio 2017 用の ASP.NET Core ツール](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)に基づいています。 Visual Studio 2015 用の .NET Core ツールは、更新されなくなりました。

1. Visual Studio を**管理者**として起動します。

2. **[ファイル]**->**[新規]**->**[プロジェクト]** の順に選択してプロジェクトを作成します。

3. **[新しいプロジェクト]** ダイアログで、**[クラウド]、[Service Fabric アプリケーション]** の順に選択します。

4. アプリケーションに "**MyApplication**" という名前を付けて、**[OK]** をクリックします。

   ![Visual Studio の [新しいプロジェクト] ダイアログ](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. **[新しい Service Fabric サービス]** ページで、**[ステートレス ASP.NET Core]** を選択し、サービスに "**MyWebAPIFrontEnd**" という名前を付けます。
   
   ![Choosing ASP.NET web service in the new service dialog](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 次のページには、一連の ASP.NET Core プロジェクト テンプレートが表示されます。 このチュートリアルでは、**[Web API]** を選択します。 ただし、完全な Web アプリケーションの作成にも同じ概念を適用できます。
   
   ![ASP.NET プロジェクトの種類の選択](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio によって、アプリケーションとサービス プロジェクトが作成され、ソリューション エクスプローラーに表示されます。

   ![アプリケーションと ASP.NET Core Web API サービスの作成後に表示されるソリューション エクスプローラー]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>アプリケーションをローカルでデプロイしデバッグする
次に、アプリケーションをデバッグし、ASP.NET Core Web API テンプレートによって提供される既定の動作を見てみましょう。

Visual Studio で `F5` キーを押して、デバッグ用にアプリケーションをデプロイします。 Visual Studio を**管理者**として開いていない場合、`F5` キーを押すとエラーが生じます。

> [!NOTE]
> 初めてアプリケーションをローカルで実行してデプロイすると、Visual Studio によってデバッグ用にローカル クラスターが作成されます。これには少し時間がかかる場合があります。 Visual Studio の出力ウィンドウにクラスターの作成状態が表示されます。

クラスターの準備が整うと、システム トレイの Service Fabric ローカル クラスター マネージャー アプリケーションから通知が表示されます。

1. アプリケーションをデバッグするには、Visual Studio で F5 キーを押します。
2. デプロイが完了すると、Visual Studio からブラウザーが起動し、ASP.NET Web API サービスのルートにアクセスされます。 ASP.NET Core Web API テンプレートではルートの既定の動作が指定されないため、ブラウザーでエラーが発生します。
3. ブラウザーに表示されている URL に `/api/values` を追加します。 この要求により、Web API テンプレートの ValuesController で `Get` メソッドが呼び出されます。 このメソッドは、テンプレートで指定される既定の応答として、2 つの文字列を格納する JSON 配列を返します。
   
![Default values returned from ASP.NET Core Web API template](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> アプリケーションのデバッグ時に Visual Studio 2017 の既定の動作を変更するには、Service Fabric アプリケーション プロジェクト **MyApplication** のプロパティを変更します。
> このチュートリアルでは、アプリケーション デバッグ モードを **[アプリケーションの更新]** に設定し、アプリケーションの URL プロパティに "**/api/values**" を追加することで、デバッグ エクスペリエンスが最適化されます。
> 

アプリケーションのデバッグを停止するには、Visual Studio に戻って **Shift + F5** キーを押します。

### <a name="understanding-the-service-fabric-application-and-service"></a>Service Fabric のアプリケーションとサービスを理解する
これで、Visual Studio ソリューションには、次の 2 つのプロジェクトが含まれています。
1. Service Fabric アプリケーション プロジェクト - **MyApplication**
    - このプロジェクトでは、コードは直接含まれません。 代わりに、一連のサービス プロジェクトが参照されます。 また、アプリケーションの構成およびデプロイ方法を指定する、別の種類の内容が含まれています。
2. サービス プロジェクト - **MyWebAPIFrontEnd**
    - このプロジェクトは、コードとサービスの構成を含む、ASP.NET Core Web API プロジェクトです。 Controllers フォルダーの ValuesController.cs コード ファイルを見ると、ASP.NET Core Web API の標準コントローラーであることがわかります。 ASP.NET Core Web API をリライアブル サービスとして Service Fabric で実行する場合、コントローラーの一部として記述されるコードに特定の要件はありません。

Service Fabric のアプリケーション モデルの詳細については、「[Service Fabric でのアプリケーションのモデル化](service-fabric-application-model.md)」を参照してください。

サービス プロジェクトの内容の詳細については、「[Reliable Services 使用](service-fabric-reliable-services-quick-start.md)」を参照してください。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>アプリケーションにステートフルなバックエンド サービスを追加する
ASP.NET Web API サービスがアプリケーションで実行されるようになりました。次に、ステートフル リライアブル サービスを追加して、アプリケーションにデータを格納しましょう。

Service Fabric では、Reliable Collection によってデータがサービス内に一貫して確実に格納されます。 Reliable Collection は、C# コレクションを使用したことがあるユーザーには馴染みのある可用性が高く信頼できる一連のコレクション クラスです。

このチュートリアルでは、Reliable Collection にカウンター値を格納するサービスを作成します。

1. ソリューション エクスプローラーで、アプリケーション プロジェクトの **[サービス]** を右クリックし、**[追加]、[Service Fabric サービスの新規作成]** の順に選択します。
   
    ![既存アプリケーションへの新しいサービスの追加](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. **[新しい Service Fabric サービス]** ダイアログ ボックスで、**[ステートフル サービス]** を選択し、サービスに "**MyStatefulService**" という名前を付けて **[OK]** をクリックします。

    ![Visual Studio の [新しいサービス] ダイアログ](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    作成したサービス プロジェクトでは、2 つのサービスがアプリケーションに含まれます。 アプリケーションのビルドを続けながら、同じ方法でさらにサービスを追加することができます。 それぞれを個別にバージョン管理およびアップグレードできます。

### <a name="deploy-and-debug-the-application-locally"></a>アプリケーションをローカルでデプロイしデバッグする
アプリケーションに新しいサービスが追加されたので、アプリケーション全体をデバッグし、新しいサービスの既定の動作を見てみましょう。

アプリケーションをデバッグするには、Visual Studio で F5 キーを押します。

> [!NOTE]
> アプリケーション デバッグ モードに **[アプリケーションの更新]** の使用を選択すると、Service Fabric のローカル クラスターに、アプリケーションのビルド出力フォルダーへのアクセスを許可するよう求められます。
> 

アプリケーションに含まれる両方のサービスが、Service Fabric のローカル クラスターにビルド、デプロイ、および表示されます。 アプリケーションが起動すると、Visual Studio によってブラウザーが起動されるとともに、**診断イベント ビューアー**が自動的に呼び出されます。ここでは、サービスからのトレース出力を確認することができます。
   
![診断イベント ビューアー](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

デバッグ中の Visual Studio ソリューションの一部であるすべてのサービスから送信されたトレース メッセージが、診断イベント ビューアーに表示されます。 診断イベント ビューアーを一時停止して、サービスのメッセージの 1 つを展開し、プロパティを確認することができます。 これにより、クラスターのどのサービスからメッセージが生成されているか、またサービス インスタンスがどのノードで実行されているかなどの情報がわかります。

![診断イベント ビューアー](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

**serviceTypeName** が **MyStatefulServiceType** であることから、このチュートリアルで作成したステートフル サービスからメッセージが送信されていることがわかります。 また、このサービスが "Current counter is... (現在のカウンターは...)" というテキストのメッセージを送信していることも確認できます。 次のスクリーンショットに示すように、このメッセージは **MyStatefulService.cs** の `RunAsync` メソッドのコードの、強調表示された行によって生成されています。

![サービス メッセージのコード](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

サービスとアプリケーションから診断情報を生成する方法の詳細については、「[Azure Service Fabric での監視と診断](service-fabric-diagnostics-overview.md)」を参照してください。

アプリケーションのデバッグを停止するには、Visual Studio に戻って **Shift + F5** キーを押します。

### <a name="understanding-the-mystatefulservice-code"></a>MyStatefulService のコードを理解する
Reliable Dictionary を使用してデータがクラスターに格納されるしくみを理解するには、ここで **MyStatefulService** サービスのコードを見ていきましょう。

サービスのコードには、Reliable Dictionary の作成、更新、および読み取りに関連する 5 行があります。

![Reliable Dictionary のコード](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. サービスの起動時に `RunAsync` メソッドが実行されるたびに、コードのこちらの行は `myDictionary` の名前の Reliable Dictionary を取得したり、サービスに追加したりします。
2. Reliable Dictionary 内の値とのすべての相互作用にはトランザクションが必要です。using ステートメントがコードのこちらの行に入力されることによって、トランザクションが作成されます。
3. コードのこちらの行は、メソッドの呼び出しで指定されたキー (`Counter` など) に関連付けられている値を取得します。
4. コードのこちらの行は、値を増分することで、`Counter` キーに関連付けられている値を更新します。
5. このメソッド呼び出しはトランザクションをコミットし、更新された値がクラスター内のクォーラムのノード全体に格納されると、値を返します。

Reliable Dictionary と Reliable Collection について詳しくは、「[Azure Service Fabric ステートフル サービスの Reliable Collection の概要](service-fabric-reliable-services-reliable-collections.md)」を参照してください。

## <a name="connect-the-services"></a>サービスへの接続
次の手順では、2 つのサービスを接続し、フロントエンドの Web API がバックエンド サービスの Reliable Dictionary から現在の値を返すようにします。

Service Fabric は、Reliable Services との通信方法において完全な柔軟性を提供します。 1 つのアプリケーション内に、TCP 経由でアクセス可能なサービスが含まれる場合があります。 そのほかにも、HTTP REST API を使用してアクセスできるサービスや、Web ソケットを介してアクセスできるサービスが含まれる場合があります。 使用可能なオプションとトレードオフについては、[サービスとの通信](service-fabric-connect-and-communicate-with-services.md)に関する記事を参照してください。

このチュートリアルでは、[Reliable Services によるサービスのリモート処理](service-fabric-reliable-services-communication-remoting.md)を使用します。

(リモート プロシージャ コール (RPC) でモデル化された) Service Remoting アプローチで、サービスのパブリック コントラクトとして機能するインターフェイスを定義します。 次に、そのインターフェイスを使用して、サービスと対話するためのプロキシ クラスを生成します。

### <a name="create-the-remoting-interface"></a>リモート処理インターフェイスを作成する
まず、2 つのサービス間のコントラクトとして機能するインターフェイスを作成します。 インターフェイスは、これを使用するすべてのサービスによって参照される必要があるため、別のクラス ライブラリ プロジェクトで作成します。

1. ソリューション エクスプローラーで、ソリューションを右クリックし、**[追加]** > **[新しいプロジェクト]** の順に選択します。
2. 左側のナビゲーション ウィンドウで **[Visual C#]** エントリを選択し、**[クラス ライブラリ (.NET Framework)]** テンプレートを選択します。 .NET Framework のバージョンが **4.5.2** 以降に設定されていることを確認します。
   
    ![ステートフル サービス用のインターフェイス プロジェクトの作成](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. クラス ライブラリに "**MyStatefulService.Interface**" の名前を付けて **[OK]** をクリックします。

4. ソリューション エクスプローラーでソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** をクリックします。

5. **[参照]** を選択して **Microsoft.ServiceFabric.Services.Remoting** を検索します。 ソリューションの 3 つのサービス プロジェクトにこれをインストールすることを選択します。 
   
    ![Services NuGet パッケージの追加](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. クラス ライブラリで、1 つのメソッド `GetCountAsync`を含むインターフェイスを作成し、`Microsoft.ServiceFabric.Services.Remoting.IService` からインターフェイスを拡張します。 リモート処理インターフェイスは、Service Remoting インターフェイスであることを示すために、このインターフェイスから派生している必要があります。 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. ソリューション エクスプローラーで **MyStatefulService.Interface** プロジェクトを右クリックし、**[プロパティ]** を選択します。 **[ビルド]** タブを選択し、**[プラットフォーム ターゲット]** ドロップダウンで **x64** の値を選択します。 

8. すべての変更を保存します。

### <a name="implement-the-interface-in-your-stateful-service"></a>ステートフル サービスでのインターフェイスの実装
インターフェイスを定義したので、次にステートフル サービスでそれを実装する必要があります。

1. **MyStatefulService** プロジェクトから、インターフェイスを含むクラス ライブラリ プロジェクトへの参照を追加します。
   
    ![ステートフル サービスのクラス ライブラリ プロジェクトへの参照の追加](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![ステートフル サービスのクラス ライブラリ プロジェクトへの参照の追加](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. `MyStatefulService.cs` ファイルを開いて拡張し、作成した `ICounter` インターフェイスを実装します。
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 次に、`ICounter` インターフェイスで定義されている単一のメソッド (`GetCountAsync`) を実装します。
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - このメソッドは、`myDictionary` という名前の Reliable Dictionary 内にある `Counter` キーの格納された値を返します。 

### <a name="expose-the-stateful-service-using-service-remoting"></a>サービス リモート処理を使用したステートフル サービスの公開
`ICounter` インターフェイスを実装したら、最後の手順としてサービス リモート処理のエンドポイントを開きます。 ステートフル サービスのために、Service Fabric には、 `CreateServiceReplicaListeners`というオーバーライド可能なメソッドが用意されています。 このメソッドを使用すると、サービスに対して有効にする通信の種類に基づき、1 つ以上の通信リスナーを指定できます。

ここでは、既存の `CreateServiceReplicaListeners` メソッドを置き換え、`ServiceRemotingListener` のインスタンスを指定します。これにより、`ServiceProxy` を介してクライアントから呼び出すことができる RPC エンドポイントが作成されます。  

**MyStatefulService.cs** ファイルの **CreateServiceReplicaListeners** メソッドを変更し、`Microsoft.ServiceFabric.Services.Remoting.Runtime` 名前空間に using ステートメントを追加します。

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

ステートフル サービスでは、サービス リモート処理を使用し、RPC を介して他のサービスからトラフィックを受信する準備ができました。

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>フロントエンド サービスからステートフルなバックエンド サービスを呼び出す
バックエンド サービスによってインターフェイスが公開されたので、後はコードを追加して ASP.NET Web API サービスから通信するだけです。 サービス リモート処理を使用して通信するには、Values コントローラーからサービス プロキシを使用します。

1. ソリューション エクスプローラーで、**MyWebAPIFrontEnd** を展開し、**[依存関係]** を右クリックして **[参照の追加]** を選択します。  **MyStatefulService.Interface** を選択して **[OK]** をクリックします。
   
2. **Controllers** フォルダーで `ValuesController.cs` ファイルを開きます。 次の using ステートメントをファイルに追加します。

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. 現時点では、`Get` メソッドはハードコーディングされた文字列配列 "value1" と "value2" を返すだけです。これらは、先にブラウザーに表示されていたものと一致します。 この実装を次のコードに置き換えます。
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    このメソッドのコードの最初の行は、ICounter インターフェイスを使用して、ステートフル サービスに ServiceProxy オブジェクトを作成します。 ServiceProxy を作成する場合は、パーティション ID とサービス名の 2 つの情報を提供する必要があります。
   
    顧客 ID や郵便番号などのキーに基づいてデータを異なるバケットに分類することで、ステートフル サービスをパーティション分割してスケーリングできます。 この単純なアプリケーションではステートフル サービスはパーティションを 1 つしか持たないので、キーは重要ではありません。 どのキーを指定しても同じパーティションになります。 サービスのパーティション分割の詳細については、「 [Service Fabric Reliable Services をパーティション分割する方法](service-fabric-concepts-partitioning.md)」を参照してください。
   
    サービス名は、fabric:/&lt;アプリケーション名&gt;/&lt;サービス名&gt; の形式の URI です。
   
    これら 2 つの情報により、Service Fabric は要求送信先のコンピューターを一意に識別できます。 また、 `ServiceProxy` クラスは、ステートフル サービス パーティションをホストするコンピューターで障害が発生したため、別のコンピューターを昇格させてその代わりにする必要がある状況をシームレスに処理します。 この抽象化により、他のサービスを処理するクライアント コードの作成が簡略化されます。
   
    プロキシを作成した後は、`GetCountAsync` メソッドを呼び出して結果を返します。

4. もう一度 F5 キーを押して、変更したアプリケーションを実行します。 前と同じように、Visual Studio はブラウザーを自動的に起動して Web プロジェクトのルートを表示します。 "api/values" パスを追加すると、現在のカウンター値が返されることがわかります。
   
    ![ブラウザーに表示されたステートフル カウンター値](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    定期的にブラウザーを更新して、カウンターの値が更新されるのを確認します。

アプリケーションのデバッグを停止するには、Visual Studio に戻って **Shift + F5** キーを押します。

## <a name="next-steps"></a>次のステップ
チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ASP.NET Core Web API サービスをリライアブル サービスとして作成する
> * ステートフル リライアブル サービスの作成
> * サービス リモート処理とサービス プロキシの使用の実装

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [Azure にアプリケーションをデプロイする](service-fabric-tutorial-deploy-app-to-party-cluster.md)
