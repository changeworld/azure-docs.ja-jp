<properties
   pageTitle="Azure Service Fabric アクターの分散計算パターン"
   description="Azure Service Fabric は並列非同期メッセージングに最適であり、分散状態の管理や並列計算が容易になります。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/01/2015"
   ms.author="claudioc"/>

# Service Fabric アクターの設計パターン: 分散計算
これは、Azure Service Fabric アクターで実際の顧客が驚くほど短時間で安易に金融計算を行う状況を監視する (つまり、リスク計算のためにモンテカルロ シミュレーションを行う) ためにも必要です。

最初は、特にドメインの特定の知識のないユーザーには、Map/Reduce や MPI などのより一般的な手法と比べて、Azure Service Fabric のこの種のワークロード処理はわかりにくいかもしれません。

しかし、次の図に示すように、Azure Service Fabric は並列非同期メッセージングに最適であり、分散状態の管理や並列計算が簡単にできることがわかります。

![][1]

次の例では、モンテカルロ シミュレーションを使用して、単に Pi を計算します。使用するアクターは以下のとおりです。

* PoolTask アクターを使用して Pi を計算する必要があるプロセッサ。

* モンテカルロ シミュレーションを行い、結果をアグリゲーターに送信する必要がある PoolTask。

* 適切に結果を集計し、ファイナライザーに送信する必要があるアグリゲーター。

* 最終結果を計算して、画面に表示する必要があるファイナライザー。

## 分散計算のコード サンプル – モンテカルロ シミュレーション

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(0); // stateless
            tasks.Add(task.CalculateAsync(tries, seed));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed)
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

        var agg = ActorProxy.Create<IAggregator>(0);
        return agg.AggregateAsync(pi);
    }
}
```

Azure Service Fabric での結果集計の一般的な方法は、タイマーを使用することです。ここでは、2 つの主な理由によりステートレス アクターを使用しています。1 つは、ランタイムが動的に必要なアグリゲーターの数を決定するため、必要に応じてスケールが示されるためです。もう 1 つは、これらのアクターが「ローカルで」(つまり、呼び出し元アクターの同じサイロで) インスタンス化され、ネットワーク ホップが減るためです。アグリゲーターとファイナライザーは次のようになります。

## 分散計算のコード サンプル – アグリゲーター

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

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
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

public class Finaliser : Actor<FinalizerState>, IFinaliser
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

        return TaskDone.Done;
    }
}
```

この時点で、スケールやパフォーマンスに応じて、アグリゲーターでランキング例がどのように拡張される可能性があるかがわかるはずです。

決して、Azure Service Fabric がビッグ データ フレームワークやハイ パフォーマンス コンピューティングの他の分散計算に一時的に代用されることを断言しているわけではありません。ただ処理において他のものより優れている点がいくつかあるというだけです。ただし、Azure Service Fabric ではワークフローと分散並列計算をモデル化できるため、やはり操作が簡単になるという利点があります。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png
 

<!---HONumber=July15_HO4-->