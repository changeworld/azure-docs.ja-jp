<properties
   pageTitle="Azure Service Fabric アクターのスマート キャッシュ設計パターン"
   description="Web ベースのアプリケーションでキャッシュ インフラストラクチャとして Service Fabric アクターを使用する方法の設計パターン。"
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
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Service Fabric アクターの設計パターン: スマート キャッシュ
Web 層、キャッシュ層、ストレージ層、および場合によってはワーカー層の組み合わせは、現在のアプリケーションの標準的な部分とよく一致します。通常、キャッシュ層はパフォーマンスにとって非常に重要であり、実際には、それ自体が複数の階層で構成される場合があります。多くのキャッシュは単純なキーと値のペアですが、キャッシュとして使用される [Redis](http://redis.io) のような他のシステムはさらにリッチなセマンティクスを提供します。それでも、特別なキャッシュ層はセマンティクスの制限があり、さらに重要なことはまだ管理すべきもう 1 つの層であるということです。代わりにオブジェクトがローカル変数に状態を保持し、これらのオブジェクトを自動的に永続的ストアにスナップショットまたは永続化できるとしたらどうでしょうか。 さらに、リスト、並べ替えられたセット、キュー、およびその他のカスタム種類などのリッチなコレクションは、単にメンバー変数およびメソッドとしてモデル化されます。

![][1]

## スコアボードのサンプル
例としてスコアボードを考えます。Leaderboard オブジェクトは、クエリできるようにプレーヤーとスコアのソートされたリストを維持する必要があります。たとえば、"上位 100 プレーヤー" や、上下 N プレーヤーを基準にしたスコアボード内でのプレーヤーの位置を検索する場合などです。従来のツールによる標準的なソリューションでは、Leaderboard オブジェクトを ‘GET’ し (Score という名前の新しいタプル<Player  Points>の挿入をサポートするコレクション)、それをソートして、最後にキャッシュに ‘PUT’ して戻します。おそらく、整合性のために Leaderboard オブジェクトをロック (GETLOCK、PUTLOCK) します。状態と動作が一緒になったアクター ベースのソリューションを考えます。2 つのオプションがあります。

* アクターの一部として Leaderboard コレクションを実装します。
* または、メンバー変数に保持できるコレクションへのインターフェイスとしてアクターを使用します。最初に、インターフェイスがどのようなものになるか見てみます。

## スマート キャッシュのコード サンプル – Leaderboard インターフェイス

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

次に、このインターフェイスを実装し、後者のオプションを使用して、このコレクションの動作をアクターにカプセル化します。

## スマート キャッシュのコード サンプル – Leaderboard アクター

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

クラスの state メンバーは、アクターの状態を提供します。上のサンプル コードでは、データの読み取り/書き込みのメソッドも提供します。

## スマート キャッシュのコード サンプル – LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

データ シッピングはなく、ロックはなく、分散ランタイム内のリモート オブジェクトを操作するだけであり、ただ 1 つのクライアントにサービスを提供する単一のアプリケーションの 1 つのオブジェクトであるかのように複数のクライアントにサービスを提供します。クライアントのサンプルを次に示します。

## スマート キャッシュのコード サンプル – Leaderboard アクターの呼び出し

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

出力は次のようになります。

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## アーキテクチャの拡張
上の例では Leaderboard インスタンスにボトルネックが作成されると思うかもしれません。たとえば、何百、何千ものプレーヤーのサポートを計画していたとするとどうでしょう。 1 つの方法は、バッファーのように機能するステートレスなアグリゲーターを導入することです。部分的なスコアを保持し (たとえば小計)、定期的にそれを Leaderboard アクターに送信します。アクターは、最終的な Leaderboard を維持できます。この「集計」手法の詳細については、後で説明します。また、正しく動作する同時実行プログラムによって従来必要であったミューテックス、セマフォ、またはその他の同時実行構造を考慮する必要はありません。次に示すのは、アクターで実装可能なリッチなセマンティクスを示すもう 1 つのキャッシュの例です。今度は、アクターの実装の一部として、優先順位キューのロジックを実装しています (値が小さいほど、優先順位が高い)。IJobQueue のインターフェイスは次のようになります。

## スマート キャッシュのコード サンプル – Job Queue インターフェイス

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

また、Job アイテムを定義する必要があります。

## スマート キャッシュのコード サンプル – Job

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

最後に、IJobQueue インターフェイスを実装します。わかりやすくするためにここでは優先順位キューの実装の詳細は省略されていることに注意してください。実装のサンプルは付属のサンプルにあります。

## スマート キャッシュのコード サンプル – Job Queue

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

出力は次のようになります。

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## アクターの柔軟性
Leaderboard と JobQueue のサンプルでは、2 つの方法を使用しました。

* Leaderboard サンプルでは、Leaderboard オブジェクトをアクターのプライベート メンバー変数としてカプセル化し、このオブジェクトに対する状態と機能の両方のインターフェイスだけを提供しました。

* 一方、JobQueue サンプルでは、他で定義されている別のオブジェクトを参照するのではなく、優先順位キュー自体としてアクターを実装しました。

アクターが提供する柔軟性により、開発者は、アクターの一部としてリッチなオブジェクト構造を定義することも、アクターの外部にあるオブジェクト グラフを参照することもできます。キャッシュの用語では、アクターはライト ビハインドまたはライト スルーを実行でき、メンバー変数のレベルで異なる手法を使用することもできます。つまり、何をいつ永続化するかを完全に制御できます。一時的な状態または保存済みの状態から構築できる状態を永続化する必要はありません。それでは、これらのアクター キャッシュの設定はどうでしょうか。 複数の方法でこれを実現できます。アクターが提供する OnActivateAsync() および OnDectivateAsync() という名前の仮想メソッドを使用すると、アクターのインスタンスがアクティブ化および非アクティブ化されたことを知ることができます。アクターは最初の要求が送信されたときにオンデマンドでアクティブ化されることに注意してください。OnActivateAsync() を使用して、おそらく外部の安定したストアから、オンデマンドで状態をリード スルー中として設定できます。または、タイマーで状態を設定できます。最新の為替相場に基づく変換関数を提供する Exchange Rate アクターがあるものとします。このアクターは、外部サービスから状態を定期的に設定でき (たとえば 5 秒ごと)、変換関数にその状態を使用できます。次の例を見てください。

## スマート キャッシュのコード サンプル – Rate Converter

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

基本的にスマート キャッシュは以下のことを提供します。

* メモリからのサービス要求による高スループット/低遅延。
* 永続的ストアでの競合なしで、アイテムに対する要求の単一インスタンス ルーティングおよび単一スレッド シリアル化。
* セマンティック操作 (エンキュー (ジョブ項目) など)。
* 簡単に実装できるライト スルーまたはライト ビハインド。
* LRU (最近最も使用されていない) 項目の自動削除 (リソース管理)。
* 自動的な弾力性と信頼性。


## 次のステップ
[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png
 

<!---HONumber=July15_HO2-->