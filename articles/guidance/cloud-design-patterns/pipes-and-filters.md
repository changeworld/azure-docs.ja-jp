---
title: "パイプとフィルター |Azure | Microsoft ドキュメント"
description: "複雑な処理を実行するタスクを、再利用できる一連の独立した要素に分解します。"
categories:
- design-implementation
- messaging
keywords: "設計パターン"
author: dragon119
manager: bennage
ms.service: guidance
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.author: mwasson
ms.date: 11/14/2016
translationtype: Human Translation
ms.sourcegitcommit: c4963489ca53a90cab480116ec0a044501ccb5e9
ms.openlocfilehash: b4d71ed1c080d63c5873656ac688c9fea788cf17

---
   
# <a name="pipes-and-filters"></a>パイプとフィルター

複雑な処理を実行するタスクを、再利用できる一連の独立した要素に分解します。 この設計パターンを使用すると、処理を実行する複数のタスク要素を別々に展開およびスケール変更することにより、パフォーマンス、スケーラビリティ、および再利用性を向上させることができます。

## <a name="context-and-problem"></a>コンテキストと問題

アプリケーションは、その処理の対象となる情報に対し、複雑度の異なる多様なタスクを実行する必要があります。 その処理をモノシリック モジュールとして実行するのが、柔軟性は低いものの実装方法としては簡単です。 しかしそのアプリケーション内のどこか他の箇所で同じ処理の一部分が必要になったとき、この方法では、コードのリファクタリングや最適化、再利用の可能性が制限されます。 

図は、モノリシック アプローチを使用してデータを処理する場合の問題を示しています。 アプリケーションは、2 つのソースからデータを受け取って処理します。 各ソースからのデータは、これらのデータを変換する一連のタスクを実行する個別のモジュールによって処理されてから、アプリケーションのビジネス ロジックに渡されます。 

![図 1 - モノリシック モジュールを使用して実装したソリューション](images/pipes-and-filters-modules.png) 

これらのモノリシック モジュールが実行するタスクの一部は機能的によく似ていますが、モジュールはそれぞれ独立した設計となっています。 タスクを実装するコードはモジュール内で密接に結び付けられており、再利用性やスケーラビリティをほとんどあるいはまったく考慮しないで開発されています。 

ただし、各モジュールによって実行される処理タスク、またはタスクごとのデプロイ要件は、ビジネス要件が更新されると変わる可能性があります。 タスクの中には、コンピューティング集中型のため高性能のハードウェアで実行することで利点が得られるタスクもあれば、そのような高価なリソースを必要としないタスクもあります。 また、将来的に追加の処理が必要になることもあれば、処理におけるタスクの実行順序が変更される可能性もあります。 これらの問題に対処し、コード再利用の可能性を高める解決策が必要です。 

## <a name="solution"></a>解決策

各ストリームで必要な処理を、一連の独立したコンポーネント (またはフィルター) に分解し、各コンポーネントでそれぞれ 1 つのタスクを実行します。 各コンポーネントが受信および送信するデータの形式を標準化することにより、このようなフィルターを 1 つのパイプラインにまとめることができます。 この方法は、コードの重複を回避するのに役立ちます。また、処理の要件が変化した場合に、コンポーネントの削除や置換、追加のコンポーネントの統合を容易に行うことができます。 次の図に、パイプとフィルターを使用して実装されたソリューションを示します。

![図 2 - パイプとフィルターを使用して実装したソリューション](images/pipes-and-filters-solution.png) 


1 つの要求を処理するのにかかる時間は、パイプライン内で最も低速なフィルターの速度によって決まります。 特定のデータ ソースからのストリーム内に多数の要求が含まれている場合は特に、1 つまたは複数のフィルターがボトルネックになる可能性があります。 パイプラインの構造の主な利点は、低速フィルターのインスタンスを並列に実行する機会が与えられていることです。これにより、システムの負荷を分散し、スループットを高めることができます。 

パイプラインを構成するフィルターは別々のコンピューターで実行できます。このため、フィルターを個別にスケール変更したり、多くのクラウド環境で提供されている弾力性を活用したりできます。 計算負荷の高いフィルターは高性能のハードウェアで実行し、その他の必要条件が低いフィルターは低価格の汎用的なハードウェアで実行することができます。 フィルターは同じデータ センターまたは地理的な場所に配置される必要はないので、パイプライン内の各要素は、必要とするリソースに近い環境で実行することができます。  次の図に、ソース 1 からのデータに対するパイプラインの適用例を示します。 

![図 3 に、ソース 1 からのデータに対するパイプラインの適用例を示します。](images/pipes-and-filters-load-balancing.png)

フィルターの入出力をストリームとして構成した場合は、各フィルターの処理を並列に実行することができます。 パイプライン内の最初のフィルターが作業を開始し、結果を出力すると、最初のフィルターの作業が完了する前に、シーケンス内の次のフィルターに結果が直接渡されます。

もう 1 つの利点は、このモデルで実現される回復性です。 フィルターで障害が発生したり、フィルターを実行していたコンピューターが使用できなくなったりした場合、パイプラインは、フィルターで実行されていた作業のスケジュールを設定し直して、その作業をコンポーネントの別のインスタンスに割り当てることができます。 1 つのフィルターでエラーが発生した場合、必ずしもそれが原因でパイプライン全体がエラーになるとは限りません。

分散トランザクションを実装するための別のアプローチとして、パイプとフィルターのパターンを [Compensating Transaction パターン](compensating-transaction.md) と組み合わせて使用するという方法があります。 この場合、分散トランザクションを補正可能な個別のタスクに分けることができ、各タスクは Compensating Transaction パターンも実装するフィルターを使用して実装することができます。 パイプライン内のフィルターは、管理しているデータの近くで個別のホストされたタスクが実行されるように実装することができます。

## <a name="issues-and-considerations"></a>問題と注意事項

このパターンの実装方法を決めるときには、以下の点に注意してください。
- **複雑さ**。 パイプライン内のフィルターをさまざまなサーバー間で分散する場合は特に、このパターンによって柔軟性が向上する一方で、複雑性が増大します。 

- **信頼性**。 パイプライン内のフィルター間を流れるデータが失われないことを保証したインフラストラクチャを使用します。 

- **べき等性**。 メッセージを受信した後にパイプライン内のフィルターが失敗し、作業のスケジュールがフィルターの別のインスタンスで実行されるように変更された場合、作業の一部が既に完了している場合があります。 このような作業によって、グローバル状態の一部 (データベースに格納されている情報など) が更新されている場合、同一の更新が繰り返される可能性があります。 フィルターにおいて、パイプライン内の次のフィルターに結果を送信してから自身の作業が正常に完了したことを表明するまでの間に、障害が発生した場合にも、同様の問題が発生することがあります。 このような場合は、フィルターの別のインスタンスでも同じ作業が繰り返され、同じ作業を 2 回ポストすることになる可能性があります。 この結果、パイプライン内の後続のフィルターでも同じデータが繰り返し処理される可能性があります。 したがって、パイプライン内のフィルターは、べき等になるように設計する必要があります。 詳細については、Jonathan Oliver のブログ「[Idempotency パターン](http://blog.jonathanoliver.com/idempotency-patterns/)」を参照してください。

- **メッセージの繰り返し**。 パイプライン内のフィルターが、パイプラインの次のステージにメッセージをポストした後でエラーになった場合は、フィルターの別のインスタンスが実行される可能性があります。そのようにして実行されたインスタンスはパイプラインに同じメッセージのコピーをポストします。 これにより、同一メッセージの 2 つのインスタンスが次のフィルターに渡される可能性があります。 この問題を回避するには、パイプラインが重複するメッセージを検出し削除する必要があります。

    >  メッセージ キュー (Microsoft Azure Service Bus キューなど) を使用してパイプラインを実装した場合、メッセージ キュー インフラストラクチャでは重複するメッセージの自動検出および削除を実現することができます。  

- **コンテキストと状態**。 パイプライン内の各フィルターは、基本的に分離して実行され、自身がどのように呼び出されたかについて推測する必要はありません。 すなわち、各フィルターには作業を実行するためのコンテキストが十分に提供されるということです。 このコンテキストには、大量の状態情報を含めることが可能です。 

## <a name="when-to-use-this-pattern"></a>このパターンを使用する状況

このパターンは次の状況で使用します。
- アプリケーションで必要な処理を一連の独立した手順に容易に分割することができる。

- アプリケーションによって実行される処理手順に、さまざまなスケーラビリティ要件がある。

    >  スケールの変更が必要なフィルターを同じプロセスにグループ化することができます。 詳細については、「[Compute Resource Consolidation pattern](compute-resource-consolidation.md)」 (Compute Resource Consolidation パターン) を参照してください。 

- アプリケーションによって実行される処理手順の並び替えを可能にする柔軟性、または手順を追加および削除する機能が必要である。

- 手順の処理をさまざまなサーバーに分散することでシステムに利点がある。

- データの処理中に手順において発生したエラーの影響を最小限に抑えることができる信頼性の高いソリューションが必要である。 

このパターンが適さない状況
- アプリケーションによって実行される処理手順が独立していない。すなわち、それらの手順を同じトランザクションの一部としてまとめて実行する必要がある。

- 手順で必要とされるコンテキストまたは状態情報の量が、このアプローチを使用するには不十分である。 状態情報をデータベースに保存しておくことができる場合があります。しかし、データベース上での負荷の増加が過剰な競合を引き起こす場合、この手法は使用しないでください。

## <a name="example"></a>例

メッセージ キューのシーケンスを使用して、パイプラインの実装に必要なインフラストラクチャを実現することができます。 最初のメッセージ キューは、未処理のメッセージを受信します。 フィルター タスクとして実装されたコンポーネントは、このキューでメッセージをリッスンし、作業を実行し、変換したメッセージをシーケンス内の次のキューにポストします。 別のフィルター タスクも、このキューでメッセージをリッスンし、そのメッセージを処理し、結果を別のキューにポストするという具合に進められ、キュー内の最後のメッセージに完全に変換されたデータが現れるまで継続されます。 次の図に、メッセージ キューを使用したパイプラインの実装を示します。

![図 4 - メッセージ キューを使用したパイプラインの実装](images/pipes-and-filters-message-queues.png)


Azure 上でソリューションを構築する場合は、Service Bus キューを使用して信頼性が高くスケーラブルなキュー メカニズムを実現できます。 次の C# で作成された `ServiceBusPipeFilter` クラスは、キューからの入力メッセージを受信し、これらのメッセージを処理し、結果を別のキューにポストするフィルターを実装する方法を示しています。

>  `ServiceBusPipeFilter` クラスは、[GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/pipes-and-filters) から入手可能な PipesAndFilters.Shared プロジェクト内に定義されています。

```csharp
public class ServiceBusPipeFilter
{
  ...
  private readonly string inQueuePath;
  private readonly string outQueuePath;
  ...
  private QueueClient inQueue;
  private QueueClient outQueue;
  ...

  public ServiceBusPipeFilter(..., string inQueuePath, string outQueuePath = null)
  {
     ...
     this.inQueuePath = inQueuePath;
     this.outQueuePath = outQueuePath;
  }

  public void Start()
  {
    ...
    // Create the outbound filter queue if it doesn't exist.
    ...
    this.outQueue = QueueClient.CreateFromConnectionString(...);
    
    ...
    // Create the inbound and outbound queue clients.
    this.inQueue = QueueClient.CreateFromConnectionString(...);
  }

  public void OnPipeFilterMessageAsync(
    Func<BrokeredMessage, Task<BrokeredMessage>> asyncFilterTask, ...) 
  {
    ...

    this.inQueue.OnMessageAsync(
      async (msg) =>
    {
      ...
      // Process the filter and send the output to the 
      // next queue in the pipeline.
      var outMessage = await asyncFilterTask(msg);

      // Send the message from the filter processor 
      // to the next queue in the pipeline.
      if (outQueue != null)
      {
        await outQueue.SendAsync(outMessage);
      }

      // Note: There's a chance that the same message could be sent twice 
      // or that a message gets processed by an upstream or downstream 
      // filter at the same time.
      // This would happen in a situation where processing of a message was
      // completed, it was sent to the next pipe/queue, and then failed 
      // to complete when using the PeekLock method.
      // Idempotent message processing and concurrency should be considered 
      // in a real-world implementation.
    },
    options);
  }

  public async Task Close(TimeSpan timespan)
  {
    // Pause the processing threads.
    this.pauseProcessingEvent.Reset();

    // There's no clean approach for waiting for the threads to complete
    // the processing. This example simply stops any new processing, waits
    // for the existing thread to complete, then closes the message pump 
    // and finally returns.
    Thread.Sleep(timespan);

    this.inQueue.Close();
    ...
  }

  ...
}
```

`ServiceBusPipeFilter` クラス内の `Start` メソッドは、入力キューと出力キューのペアに接続し、`Close` メソッドは入力キューとの接続を解除します。 `OnPipeFilterMessageAsync` メソッドは、メッセージの実際の処理を実行します。実行する処理は、このメソッドの `asyncFilterTask` パラメーターで指定されます。 `OnPipeFilterMessageAsync` メソッドは、入力キューで着信メッセージを待機し、メッセージが着信すると `asyncFilterTask` パラメーターで指定されたコードを各メッセージに対して実行し、結果を出力キューにポストします。 キュー自体はコンストラクターで指定されます。 

サンプル ソリューションでは、一連のworker ロールでフィルターを実装しています。 各 worker ロールは、実行するビジネス プロセスの複雑さや処理に必要なリソースに応じて、個別にスケールを変更することができます。 さらに、各 worker ロールの複数のインスタンスを並列に実行して、スループットを向上させることができます。 

次のコードは、サンプル ソリューションの PipeFilterA プロジェクトに `PipeFilterARoleEntry` という名前で定義された Azure worker ロールを示しています。 

```csharp
public class PipeFilterARoleEntry : RoleEntryPoint
{
  ...
  private ServiceBusPipeFilter pipeFilterA;

  public override bool OnStart()
  {
    ...
    this.pipeFilterA = new ServiceBusPipeFilter(
      ...,
      Constants.QueueAPath,
      Constants.QueueBPath);

    this.pipeFilterA.Start();
    ...
  }

  public override void Run()
  {
    this.pipeFilterA.OnPipeFilterMessageAsync(async (msg) =>
    {
      // Clone the message and update it.
      // Properties set by the broker (Deliver count, enqueue time, ...) 
      // aren't cloned and must be copied over if required.
      var newMsg = msg.Clone();
      
      await Task.Delay(500); // DOING WORK

      Trace.TraceInformation("Filter A processed message:{0} at {1}", 
        msg.MessageId, DateTime.UtcNow);

      newMsg.Properties.Add(Constants.FilterAMessageKey, "Complete");

      return newMsg;
    });

    ...
  }

  ...
}
```

このロールには、`ServiceBusPipeFilter` オブジェクトが含まれています。 ロール内の `OnStart` メソッドは、入力メッセージを受信し、出力メッセージを送信するためのキューに接続します (キューの名前は `Constants` クラスに定義されています)。 `Run` メソッドは、`OnPipeFilterMessagesAsync` メソッドを呼び出して、受信された各メッセージに対して何らかの処理を実行します (この例では、短い時間待ってから処理がシミュレーションされます)。 処理が完了すると、結果を含む新しいメッセージが作成され (この場合は、入力メッセージにカスタム プロパティが追加される)、そのメッセージが出力キューにポストされます。

サンプル コードでは、PipeFilterB プロジェクト内に `PipeFilterBRoleEntry` という名前の別の worker ロールが含まれています。 このロールは、`Run` メソッドでさまざまな処理を実行する点を除いて、`PipeFilterARoleEntry` メソッドによく似ています。 ソリューションの例では、この 2 つのロールを組み合わせてパイプラインを作成します。`PipeFilterARoleEntry` ロールの出力キューは、`PipeFilterBRoleEntry` ロールの入力キューです。

サンプル ソリューションでは、`InitialSenderRoleEntry` という名前のロール (InitialSender プロジェクト) と `FinalReceiverRoleEntry` という名前のロール (FinalReceiver プロジェクト) も追加しています。 `InitialSenderRoleEntry` ロールは、パイプライン内に最初のメッセージを提供します。 `OnStart` メソッドは 1 つのキューに接続し、`Run` メソッドはこのキューにメソッドをポストします。 このキューは `PipeFilterARoleEntry` ロールで使用される入力キューです。メッセージをそのキューにポストすると、`PipeFilterARoleEntry` ロールがそのメッセージを受信して処理します。 処理されたメッセージは、`PipeFilterBRoleEntry` ロールを通して渡されます。

`FinalReceiveRoleEntry` ロールの入力キューは、`PipeFilterBRoleEntry` ロールの出力キューです。 以下に示す `FinalReceiveRoleEntry`ロール内の `Run` メソッドは、メッセージを受信し、何らかの最終処理を実行します。 パイプライン内のフィルターによって追加されたカスタム プロパティの値がトレース出力に書き込まれます。

```csharp
public class FinalReceiverRoleEntry : RoleEntryPoint
{
  ...
  // Final queue/pipe in the pipeline to process data from.
  private ServiceBusPipeFilter queueFinal;

  public override bool OnStart()
  {
    ...
    // Set up the queue.
    this.queueFinal = new ServiceBusPipeFilter(...,Constants.QueueFinalPath);
    this.queueFinal.Start();
    ...
  }

  public override void Run()
  {
    this.queueFinal.OnPipeFilterMessageAsync(
      async (msg) =>
      {
        await Task.Delay(500); // DOING WORK

        // The pipeline message was received.
        Trace.TraceInformation(
          "Pipeline Message Complete - FilterA:{0} FilterB:{1}",
          msg.Properties[Constants.FilterAMessageKey],
          msg.Properties[Constants.FilterBMessageKey]);

        return null;
      });
    ...
  }

  ...
}
```

##<a name="related-patterns-and-guidance"></a>関連のあるパターンとガイダンス

このパターンを実装する場合は、次のパターンとガイダンスも関連している可能性があります。
- このパターンを示すサンプルは [GitHub](https://github.com/mspnp/cloud-design-patterns/tree/master/samples/pipes-and-filters) から入手できます。
- [競合コンシューマー パターン](competing-consumers.md)。 パイプラインには、1 つまたは複数のフィルターの複数のインスタンスを含めることができます。 このアプローチは、低速フィルターのインスタンスを並列実行することで、システムの負荷を分散しスループットを向上させる場合に便利です。 フィルターの各インスタンスは、入力に対して互いに競合するので、フィルターの 2 つのインスタンスで同じデータを処理すべきではありません。 このアプローチの説明を入力します。
- [Compute Resource Consolidation パターン](compute-resource-consolidation.md)。 スケールの変更が必要なフィルターを同じプロセスにグループ化することができる場合があります。 この方法の利点とトレードオフの詳細について説明します。
- [Compensating Transaction パターン](compensating-transaction.md)。 フィルターは、取り消し可能な操作として、または、障害発生時に前の状態に復元するための補正操作を備えた操作として実装できます。 最終的な一貫性を維持または実現するためには、この方法をどのように実装すればよいかを説明します。 
- Jonathan Oliver のブログの [Idempotency パターン](http://blog.jonathanoliver.com/idempotency-patterns/)。 



<!--HONumber=Nov16_HO3-->


