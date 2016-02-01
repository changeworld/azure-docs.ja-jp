<properties
   pageTitle="分散計算パターン | Microsoft Azure"
   description="Service Fabric Reliable Actors は並列非同期メッセージングに最適であり、分散状態の管理や並列計算が容易になります。"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Reliable Actors の設計パターン: 分散計算
実際のユーザーが Azure Service Fabric Reliable Actors で会計計算を短時間で作成する場合に、監視処理の一部で、この計算を利用しています。これは、リスク計算向けのモンテカルロ シミュレーションでした。

ドメイン固有の情報がない場合、従来のアプローチ (MapReduce や Message Passing Interface など) ではなく Service Fabric でこのようなワークロードを処理する利点は、すぐにはわからない可能性があります。

ただし次の図に示すように、Service Fabric は並列非同期メッセージングに最適であり、分散状態の管理や並列計算が簡単にできることがわかります。

![Service Fabric の並列非同期メッセージング、分散状態、並列計算][1]

次の例では、モンテカルロ シミュレーションを使用して、単に円周率を計算します。ここでは、次のアクターを採用します。

* プール済みタスク アクターを使用して円周率を計算する必要があるプロセッサ

* モンテカルロ シミュレーションを行い、結果をアグリゲーターに送信する必要があるプール済みタスク。

* 適切に結果を集計し、ファイナライザーに送信する必要があるアグリゲーター。

* 最終結果を計算して、画面に表示する必要があるファイナライザー。

## 分散計算のコード サンプル -- モンテカルロ シミュレーション

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : StatelessActor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : StatelessActor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Service Fabric での結果集計の一般的な方法は、タイマーを使用することです。ステートレス アクターを使用する 2 つの主な理由は、動的に必要なアグリゲーター数をランタイムで決定して必要に応じて拡大/縮小できるようにするためと、ステートレス アクターを "ローカル" でインスタンス化するためです。 つまり、呼び出し元アクターと同じサイロで発生するので、ネットワーク ホップ数を抑えられます。

アグリゲーターとファイナライザーは次のようになります。

## 分散計算のコード サンプル -- アグリゲーター

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : StatefulActor<AggregatorState>, IAggregator
{
    protected override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : StatefulActor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return Task.FromResult(true);
    }
}
```

この時点で、スケールやパフォーマンスに応じて、アグリゲーターでランキング例がどのように拡張できるかがわかるはずです。

Azure Service Fabric がビッグ データ フレームワークやハイ パフォーマンス コンピューティングの他の分散計算に一時的に代用されることを断言しているわけではありませんが、Azure Service Fabric は他の方法よりも一部の処理に適切に対応できるように構築されています。ただし、Service Fabric ではワークフローと分散並列計算をモデル化できるため、やはり操作が簡単になるという利点があります。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[いくつかのアンチパターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=AcomDC_0121_2016-->