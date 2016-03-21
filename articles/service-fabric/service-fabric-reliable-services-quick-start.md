<properties
   pageTitle="Reliable Services の概要 | Microsoft Azure"
   description="ステートレス サービスとステートフル サービスを使用して Microsoft Azure Service Fabric アプリケーションを作成する方法。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/15/2015"
   ms.author="vturecek"/>

# Service Fabric の Reliable Services の概要

Azure Service Fabric アプリケーションには、コードを実行する 1 つ以上のサービスが含まれています。このガイドでは、[Reliable Services](service-fabric-reliable-services-introduction.md) を使用してステートレスとステートフル両方の Service Fabric アプリケーションを作成する方法を説明します。

## ステートレス サービスの作成

ステートレス サービスは、クラウド アプリケーションで現在基準となっている種類のサービスです。ステートレスと見なされるのは、確実に格納する必要があるデータや高可用性を実現する必要があるデータが、サービス自体には含まれていないためです。ステートレス サービスのインスタンスが終了すると、すべての内部状態が失われます。この種類のサービスで、状態の高可用性と高い信頼性を実現するには、Azure テーブルや SQL データベースなどの外部ストアに状態を格納する必要があります。

Visual Studio 2015 RC を管理者として起動し、HelloWorld という名前の新しい Service Fabric アプリケーション プロジェクトを作成します。

![[新しいプロジェクト] ダイアログを使用して新しい Service Fabric アプリケーションを作成する](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

次に、HelloWorldStateless という名前のステートレス サービス プロジェクトを作成します。

![2 番目のダイアログ ボックスでステートレス サービス プロジェクトを作成する](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

これで、ソリューションには、次の 2 つのプロジェクトが含まれています。

 - HelloWorld。これはサービスを含むアプリケーション プロジェクトです。また、アプリケーションを説明するアプリケーション マニフェストと、アプリケーションをデプロイするのに役立つ多くの PowerShell スクリプトも含まれます。
 - HelloWorldStateless。これはサービス プロジェクトです。ステートレス サービスの実装が含まれています。


## サービスの実装

サービス プロジェクト内にある **HelloWorldStateless.cs** ファイルを開きます。Service Fabric では、どのようなビジネス ロジックもサービスで実行できます。サービス API には、コードのエントリ ポイントが 2 つあります。

 - ワークロードの実行を開始できる、RunAsync と呼ばれる変更可能なエントリ ポイント メソッド。ワークロードには、実行時間の長いコンピューティング ワークロードも含まれます。

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - 選択した通信スタック (ASP.NET Web API など) をプラグインできる通信エントリ ポイント。これは、ユーザーおよびその他のサービスからの要求の受信を開始できる場所です。

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

このチュートリアルでは、`RunAsync()` エントリ ポイント メソッドを取り上げます。これは、コードの実行をすぐに開始できる場所です。プロジェクト テンプレートには、ローリング カウントをインクリメントする `RunAsync()` の実装例が含まれます。

> [AZURE.NOTE]通信スタックを使用する方法の詳細については、「[OWIN 自己ホストによる Microsoft Azure Service Fabric Web API の概要](service-fabric-reliable-services-communication-webapi.md)」を参照してください。


### RunAsync

```C#
protected override async Task RunAsync(CancellationToken cancelServiceInstance)
{
    // TODO: Replace the following sample code with your own logic.

    int iterations = 0;
    // This service instance continues processing until the instance is terminated.
    while (!cancelServiceInstance.IsCancellationRequested)
    {

        // Log what the service is doing
        ServiceEventSource.Current.ServiceMessage(this, "Working-{0}", iterations++);

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServiceInstance);
    }
}
```

プラットフォームは、サービスのインスタンスが配置され実行準備ができたときに、このメソッドを呼び出します。ステートレス サービスの場合、これは単にサービス インスタンスが開いたことを意味します。サービス インスタンスを終了する必要がある場合のために、キャンセル トークンが提供されています。Service Fabric では、サービス インスタンスの開始から終了のサイクルは、サービスのライフタイムで何度も発生する可能性があります。これは、さまざまな理由で発生する可能性があります。

- システムがリソース分散のためにサービス インスタンスを移動している。
- コードでエラーが発生している。
- アプリケーションまたはシステムがアップグレードされている。
- 基礎となるハードウェアで障害が発生している。

この調整は、サービスの可用性を高めて適切なバランスを取るために、システムによって管理されます。

`RunAsync()` は独自タスク内で実行されます。上記のコード スニペットでは、while ループがすぐに開始されることに注意してください。ワークロード用の別のタスクをスケジュール設定する必要はありません。ワークロードの取り消しは、提供されたキャンセル トークンを使用して協調的に調整されます。システムはタスクが完了 (正常に完了、キャンセル、または失敗) するまで待機してから、次に進みます。システムからキャンセルが要求された場合は、キャンセル トークンを利用して作業を完了し、できるだけ早く `RunAsync()` を終了することが重要です。

このステートレス サービスの例では、カウントはローカル変数に格納されます。これはステートレス サービスであるため、保存される値は、サービス インスタンスの現在のライフサイクルのみで保持されます。このサービスを移動または再起動すると、値は失われます。

## ステートフル サービスの作成

Service Fabric には、新しい種類のステートフルなサービスが導入されています。ステートフル サービスは、サービス内に状態を確実に維持でき、それを使用するコードと同じ場所に配置できます。Service Fabric によって状態の可用性が高まるため、外部ストアに状態を維持する必要がなくなります。

サービスが移動または再起動した場合でも、カウンター値をステートレスから高可用と永続性に変換するには、ステートフル サービスが必要です。

先ほどと同じ HelloWorld アプリケーションで、アプリケーション プロジェクトを右クリックして **[新しい Fabric Service]** を選択することで、新しいサービスを追加できます。

![Service Fabric アプリケーションにサービスを追加します。](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

**[ステートフル サービス]** を選択し、HelloWorldStateful という名前を付けます。**[OK]** をクリックします。

![[新しいプロジェクト] ダイアログを使用して新しい Service Fabric のステートフル サービスを作成する](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

アプリケーションには、ステートレス サービス HelloWorld とステートフル サービス HelloWorldStateful という 2 つのサービスが表示されています。

HelloWorldStateful で、次の RunAsync メソッドを含む **HelloWorldStateful.cs** を開きます。

```C#
protected override async Task RunAsync(CancellationToken cancelServicePartitionReplica)
{
    // TODO: Replace the following sample code with your own logic.

    // Gets (or creates) a replicated dictionary called "myDictionary" in this partition.
    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    // This partition's replica continues processing until the replica is terminated.
    while (!cancelServicePartitionReplica.IsCancellationRequested)
    {

        // Create a transaction to perform operations on data within this partition's replica.
        using (var tx = this.StateManager.CreateTransaction())
        {

            // Try to read a value from the dictionary whose key is "Counter-1".
            var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

            // Log whether the value existed or not.
            ServiceEventSource.Current.ServiceMessage(this, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            // If the "Counter-1" key doesn't exist, set its value to 0
            // else add 1 to its current value.
            await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

            // Committing the transaction serializes the changes and writes them to this partition's secondary replicas.
            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is sent to this partition's secondary replicas.
            await tx.CommitAsync();
        }

        // Pause for one second before continuing processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

ステートフル サービスのエントリ ポイントは、ステートレス サービスと同じです。主な違いは、Reliable Collection と状態マネージャーの可用性です。`RunAsync()` は、ステートフル サービスとステートレス サービスで同じように動作します。ただし、ステートフル サービスでは、プラットフォームは、`RunAsync()` を実行する前に、ユーザーのために追加の作業を行います。この作業には、状態マネージャーと Reliable Collection が使用できる状態にあることの確認が含まれる可能性があります。

### Reliable Collection と状態マネージャー

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

IReliableDictionary は、サービスに状態を確実に格納するために使用できるディクショナリの実装です。これは、Service Fabric に組み込まれている [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) の一部です。Service Fabric と Reliable Collection を使用すると、データをサービスに直接格納できるため、外部の永続ストアが必要ありません。この方法によって、データの可用性が高まります。Service Fabric では、サービスの複数のレプリカを作成して管理することでこれを実現します。また、これらのレプリカとその状態遷移の管理の複雑さを取り除く API も提供します。

Reliable Collection にはカスタム型を含むすべての .NET 型を格納できます。ただし次の点にご注意ください。

 - Service Fabric では、ノード全体で状態をレプリケートしてローカル ディスクに格納することで、状態の可用性を高めます。これは、Reliable Collection に格納されるすべてのデータはシリアル化可能である必要があることを意味します。既定では、Reliable Collection は [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) を使用してシリアル化します。そのため、既定のシリアライザーを使用する場合は、使用する型が[データ コントラクト シリアライザーでサポートされている](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx)ことを確認することが重要です。

 - Reliable Collection でトランザクションをコミットすると、可用性が高めるためにオブジェクトがレプリケートされます。Reliable Collection に格納されるオブジェクトは、サービスのローカル メモリに保持されます。これは、オブジェクトへのローカルな参照があることを意味します。

    トランザクションの Reliable Collection を更新せずに、これらのオブジェクトのローカル インスタンスを変更しないようにしてください。これらの変更は自動的にレプリケートされないためです。

Reliable Collection の管理は状態マネージャーが行います。サービス内のどの時点のどの場所でも、名前によって Reliable Collection の情報を状態マネージャーに問い合わせることができます。状態マネージャーは参照が取得されることを保証します。Reliable Collection インスタンスへの参照をクラス メンバー変数やプロパティに保存することはお勧めしません。サービスのライフサイクル中、参照が常にインスタンスに設定されていることを保証するために特に注意を払う必要があります。この作業は状態マネージャーによって処理され、繰り返されるアクセスのために最適化されます。

### トランザクション処理と非同期処理

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collection には、LINQ を含めて、`System.Collections.Generic` と `System.Collections.Concurrent` が実行する操作と同じ操作が多数あります。ただし、Reliable Collection に対する操作は非同期です。これは Reliable Collection での書き込み操作はレプリケートされるためです。高可用性のため、これらの操作は、別のノード上の他のサービスのレプリカに送信されます。

トランザクション操作もサポートされているため、複数の Reliable Collection 間で状態の整合性を保つことができます。たとえば、1 つのトランザクション内で Reliable Queue から作業項目をデキューし、その項目の操作を実行してから、結果を Reliable Dictionary に保存するとします。これはアトミック操作として扱われ、操作全体が成功するかまったく成功しないことが保証されます。項目をデキューしたが、結果を保存する前にエラーが発生した場合は、トランザクション全体がロールバックされ、項目は処理のためにキューに残ります。

## アプリケーションの実行

HelloWorld アプリケーションに戻ります。ここからサービスを構築してデプロイできます。**F5** キーを押すと、アプリケーションが構築され、ローカル クラスターにデプロイされます。

サービスが実行を開始した後は、生成された Event Tracing for Windows (ETW) イベントを **[診断イベント]** ウィンドウで確認できます。アプリケーションのステートレス サービスとステートフル サービスの両方のイベントが表示されるのでご注意ください。**[一時停止]** ボタンをクリックして、ストリームを一時停止できます。その後、メッセージを展開して、メッセージの詳細を調べることができます。

>[AZURE.NOTE]アプリケーションを実行する前に、ローカル開発クラスターが実行されていることを確認します。ローカル環境の設定に関する情報は、「[ファースト ステップ ガイド](service-fabric-get-started.md)」を参照してください。

![Visual Studio で診断イベントを表示する](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 次のステップ

[Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

[はじめに: OWIN 自己ホストによる Service Fabric Web API サービス](service-fabric-reliable-services-communication-webapi.md)

[Reliable Collection の詳細](service-fabric-reliable-services-reliable-collections.md)

[アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Reliable Services の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0121_2016-->