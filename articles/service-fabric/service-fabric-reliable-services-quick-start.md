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

# Microsoft Azure Service Fabric の Reliable Service の概要

Service Fabric アプリケーションには、コードを実行する 1 つ以上のサービスが含まれています。ここでは、[Reliable Services](service-fabric-reliable-services-introduction.md) を使用してステートレスとステートフル両方の Service Fabric アプリケーションを作成する方法を説明します。

## ステートレス サービスの作成

ステートレス サービスは、現在ほとんどのクラウド アプリケーションに含まれるサービスの種類です。確実に格納しなければならいデータや可用性を高めなければならないデータがサービス自体に含まれない場合、サービスはステートレスだと考えられます。つまり、ステートレス サービスのインスタンスがシャットダウンした場合、その内部状態はすべて失われてしまうということです。これらの種類のサービスで、状態の高可用性と高い信頼性を実現するには、Azure テーブルや SQL データベースなどの外部ストアに状態を格納する必要があります。

Visual Studio 2015 RC を**管理者**として起動し、*HelloWorld* という名前の新しい **Service Fabric アプリケーション** プロジェクトを作成します。

![[新しいプロジェクト] ダイアログを使用して、新しい Service Fabric アプリケーションを作成します。](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

次に、*HelloWorldStateless* という名前の**ステートレス サービス** プロジェクトを作成します。

![2 番目のダイアログ ボックスでは、ステートレス サービスを作成します。](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

これで、ソリューションには、次の 2 つのプロジェクトが含まれています。

 + **HelloWorld**: これは*サービス*を含む*アプリケーション* プロジェクトです。また、アプリケーションを説明するアプリケーション マニフェストと、アプリケーションをデプロイするのに役立つ多くの PowerShell スクリプトも含まれます。
 + **HelloWorldStateless**: これはサービス プロジェクトで、ステートレス サービスの実装が含まれています。


## サービスの実装

サービス プロジェクト内にある **HelloWorldStateless.cs** ファイルを開きます。Service Fabric では、どのようなビジネス ロジックもサービスで実行できます。サービス API には、コードのエントリ ポイントが 2 つあります。

 - *RunAsync* と呼ばれる変更可能なエントリ ポイント メソッドでは、実行時間の長いコンピューティング ワークロードなどの任意のワークロードを実行開始できます。

```C#
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

 - Web API などの任意の通信スタックをプラグインできる通信エントリ ポイントでは、ユーザーまたはその他のサービスからの要求の受信を開始できます。

```C#
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

このチュートリアルでは、コードをすぐに実行開始できる `RunAsync()` エントリ ポイント メソッドを取り上げます。プロジェクト テンプレートには、ローリング カウントをインクリメントする `RunAsync()` の実装例が含まれます。

> [AZURE.NOTE]通信スタックの操作に関する詳細については、「[OWIN 自己ホストによる Microsoft Azure Service Fabric の Web API サービスの概要](service-fabric-reliable-services-communication-webapi.md)」を参照してください。


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

プラットフォームでは、サービスのインスタンスが配置され実行準備ができたときに、このメソッドが呼び出されます。ステートレス サービスの場合、これは単にサービス インスタンスが開始したことになります。サービス インスタンスを終了する必要がある場合のために、キャンセル トークンが提供されています。Service Fabric でサービス インスタンスの開始から終了のサイクルは、次のようなさまざまな理由で、サービスのライフタイムで何度も発生する可能性があります。

- リソース分散のために、サービス インスタンスが切り替わっている場合。
- コード内でエラーが発生した場合。
- アプリケーションまたはシステムのアップグレード中。
- 基礎となるハードウェアで障害が発生した場合。

このオーケストレーションは、サービスの可用性を高めて適切なバランスを取るために、システムによって管理されます。

`RunAsync()` は独自**タスク**内で実行されます。ここで使用しているコード スニペットでは、**while** ループをすぐに開始しているため、ワークロードのために別のタスクをスケジュール設定する必要はありません。ワークロードの取り消しは、提供されたキャンセル トークンを使用して協調的に調整されます。システムはタスクが完了 (正常に完了、キャンセル、または失敗) するまで待機してから、次に進みます。**重要**: システムからキャンセルが要求された場合、キャンセル トークンを利用し、すべての作業を完了してから、できるだけ早く `RunAsync()` を終了します。

このステートレス サービスの例では、カウントはローカル変数に格納されます。これはステートレス サービスであるため、保存される値は、その値を含むサービス インスタンスの現在のライフサイクルのみで保持されます。このサービスを移動または再起動すると、値は失われます。

## ステートフル サービスの作成

Service Fabric では、新しい種類のステートフル サービスが導入されました。それは、サービス内で状態を確実に維持でき、サービスで使用されているコードと併置されているサービスです。Service Fabric によって状態の可用性が高まるため、外部ストアに状態を維持する必要がなくなります。

サービスを切り替えたり、再起動したりした場合でも、カウンター値をステートレスから高可用と永続性に変換するには、ステートフル サービスが必要です。

先ほどと同じ **HelloWorld** アプリケーションで、アプリケーション プロジェクトを右クリックして **[新しい Fabric Service]** を選択し、新しいサービスを追加します。

![Service Fabric アプリケーションにサービスを追加します。](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

[**Service Fabric のステートフル サービス**] を選択し、「HelloWorldStateful」という名前を付けます。**[追加]** をクリックします。

![[新しいプロジェクト] ダイアログを使用して、新しい Service Fabric ステートフル サービスを作成します。](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

アプリケーションには、ステートレス サービス* HelloWorld* とステートフル サービス *HelloWorldStateful* という 2 つのサービスが表示されています。

*HelloWorldStateful* で、次の `RunAsync` メソッドを含む **HelloWorldStateful.cs** を開きます。

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

        // Pause for 1 second before continue processing.
        await Task.Delay(TimeSpan.FromSeconds(1), cancelServicePartitionReplica);
    }
}
```

### RunAsync

ステートフル サービスのエントリ ポイントは、ステートレス サービスと同じです。主な違いは、*Reliable Collection* と *State Manager* の可用性です。ステートフル サービスの `RunAsync()` はステートレス サービスにおいてと同様に機能しますが、ステートフル サービスでは `RunAsync()` の実行前に、*State Manager* と *Reliable Collection* の使用準備ができているかを確認するなどの追加作業を、ユーザーではなくプラットフォームが行う点が異なります。

### Reliable Collection および Reliable State Manager

```C#
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

**IReliableDictionary** は、サービスに状態を信頼性の高い方法で格納できるディクショナリの実装です。これは、Service Fabric の組み込みの [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) に含まれています。Service Fabric と Reliable Collection を使用すると、データをサービスに直接格納することができます。外部の永続ストアは不要なので、データを高可用にする必要はありません。これを達成するために、Service Fabric では、サービスの複数*レプリカ*が作成および管理される一方で、これらのレプリカとその状態遷移の管理上の複雑さを抽象化する API が提供されています。

Reliable Collection にはカスタム型を含むすべての .NET 型を格納できます。ただし次の点にご注意ください。

 1. Service Fabric では、ノード全体で状態を*レプリケート*してローカル ディスクに格納することで、状態が高可用性になります。これはつまり、Reliable Collection に格納されているすべてのデータは*シリアル化*する必要があるということです。既定では、Reliable Collection は [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx) を使用してシリアル化します。そのため、既定のシリアライザーを使用する場合は、使用する型が[データ コントラクト シリアライザーでサポートされている](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx)ことを確認することは重要です 。

 2. Reliable Collection でトランザクションをコミットすると、オブジェクトはレプリケートされて可用性が高まります。Reliable Collection に格納されているオブジェクトはサービスのローカル メモリに保持されるため、オブジェクトへのローカル参照が存在することになります。

    トランザクションの Reliable Collection を更新せずに、これらのオブジェクトのローカル インスタンスを変更しないようにしてください。これらの変更は自動的にレプリケートされないためです。

Reliable Collection は *StateManager* によって管理されます。サービス内のどこからでも Reliable Collection の名前を指定して StateManager に要求すれば、いつでも必ず参照を取得できます。参照を Reliable Collection インスタンスのクラス メンバーの変数やプロパティに保存することはお勧めしません。サービスのライフサイクルでは常に参照がインスタンスに設定されているように特に注意が必要だからです。この作業は StateManager によって実行され、繰り返されるアクセスに最適化されています。

### トランザクションおよび非同期

```C#
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

Reliable Collection には、LINQ など、`System.Collections.Generic` および `System.Collections.Concurrent` の操作と同じ操作が多くあります。ただし、Reliable Collection に対する操作は非同期です。これは Reliable Collection での書き込み操作が*レプリケート*されるためです (操作を別のノード上にあるサービスのレプリカに送信することで高可用性を実現しているということです)。

*トランザクション*操作もサポートされているため、複数の Reliable Collection 間で状態の整合性を保つことができます。たとえば、1 つのトランザクション内で Reliable Queue から作業項目をデキューし、その項目の操作を実行してから、結果を Reliable Dictionary に保存するとします。これはアトミック操作として処理されるため、すべての操作が成功するかしないかのいずれかになることが決まっています。そのため、項目をデキューした後で、結果を保存する前にエラーが発生した場合は、トランザクション全体がロールバックされ、その項目はキューに残って処理を待ちます。

## アプリケーションの実行

*HelloWorld* アプリケーションに戻ります。ここからサービスを構築してデプロイできます。**F5** キーを押すと、アプリケーションが構築され、ローカル クラスターにデプロイされます。

サービスが実行されると、生成された ETW イベントが [**診断イベント**] ウィンドウに表示されます。アプリケーションには、ステートレス サービスとステートフル サービスの両方のイベントが表示されるのでご注意ください。[*一時停止*] ボタンをクリックするとストリームが一時停止するので、メッセージを展開して詳細内容を確認できます。

>[AZURE.NOTE]アプリケーションを実行する前に、ローカルの開発クラスターが実行されていることを確認します。ローカル環境を設定するには、「[ファースト ステップ ガイド](service-fabric-get-started.md)」を参照してください。

![Visual Studio での診断イベントの表示](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)


## 次のステップ

[Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)

[OWIN 自己ホストによる Microsoft Azure Service Fabric Web API サービスの概要](service-fabric-reliable-services-communication-webapi.md)

[Reliable Collection の詳細](service-fabric-reliable-services-reliable-collections.md)

[アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[Reliable Service の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_1217_2015-->