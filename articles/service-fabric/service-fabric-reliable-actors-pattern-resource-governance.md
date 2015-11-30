<properties
   pageTitle="リソース ガバナンス設計パターン | Microsoft Azure"
   description="Service Fabric 高信頼アクターを利用し、拡張するが制約のあるリソースを使用するアプリケーション ニーズをモデル化する設計パターン。"
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# 高信頼アクターの設計パターン: リソースのガバナンス

このパターンおよび関連するシナリオは、すぐに拡張できない制約されたリソースがオンプレミスまたはクラウドにある開発者または大規模なアプリケーションとデータをクラウドに出荷する開発者 (エンタープライズまたはその他) であれば簡単に認識できます。

企業では、データベースなどのこのような制約されたリソースはスケールアップ ハードウェアで実行されます。企業に長くいる開発者は誰でもそれがオンプレミスで一般的な状況であることを知っています。クラウド スケールであっても、クラウド サービスがアドレス/ポート タプルの間で 64 K TCP の接続制限を超えようとしたとき、または同時接続数が制限されているクラウド ベースのデータベースへの接続を試みたときに、この状況が発生することを見てきました。

通常これまでは、メッセージ ベースのミドルウェアを介した調整によって、またはカスタム ビルドのプールとファサード メカニズムによって、これは解決されました。これらを正しく行うのは難しく、中間層を拡大しながら適切な接続数を保持する必要があるときは特にそうです。これは脆弱であり複雑なです。

実際、スマート キャッシュ パターンのように、このパターンは複数のシナリオと、リソースが制限されたシステムを既に持っている顧客にまたがっています。彼らは、サービスのスケールアウトだけでなく、メモリ内の状態および安定したストレージ内の永続化状態のスケールアウトを必要とするシステムを作成しています。

次の図は、このシナリオを示しています。

![][1]

## アクターによるキャッシュ シナリオのモデル化

基本的に、リソースまたはリソース グループへのプロキシ (たとえば接続など) として機能する 1 つまたは複数のアクターとしてリソースへのアクセスをモデル化します。その後、個別のアクターまたはリソース アクターを管理する調整アクターを使用して、リソースを直接管理できます。さらに具体的にするため、パフォーマンスとスケーラビリティの理由でパーティション分割された (シャーディングも呼ばれる) 記憶域階層に対して必要な一般的ニーズに対応します。最初のオプションは非常に基本的です。静的関数を使用してアクターを下流のリソースにマップして解決をします。たとえば、このような関数は指定された入力で接続文字列を返すことができます。その関数の実装方法は完全にお任せです。もちろん、このアプローチには、リソースの再分割またはリソースへのアクターの再マップを非常に困難にする静的アフィニティのような固有の欠点があります。非常に簡単な例を次に示します。モジュロ演算を行いユーザー ID とユーザー リージョンを使用してデータベース名を特定してデータベース サーバーを識別します。

## リソース ガバナンスのコード サンプル – 静的解決

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

簡単ですがあまり柔軟ではありません。これより高度で役に立つアプローチを見てみましょう。最初に、物理リソースとアクターの間のアフィニティをモデル化します。これは、ユーザー、論理パーティション、および物理リソースの間のマッピングを理解している Resolver という名前のアクターによって行われます。Resolver は、永続化ストアでデータを保持しますが、容易に検索するためにキャッシュに格納されます。前のスマート キャッシュ パターンの Exchange Rate サンプルで示したように、Resolver はタイマーを使用して最新の情報を事前にフェッチできます。User アクターは、使用する必要があるリソースを解決すると、\_resolution という名前のローカル変数にキャッシュして、それを有効期間中使用します。スケールイン/スケールアウトやリソース間のユーザーの移動などの操作での柔軟性のため、単純ハッシュや範囲ハッシュではなく、検索ベースの解決を選択しました (下図参照)。

![][2]

上の図では、アクター B23 が最初にリソース (resolution とも呼ばれる) DB1 を解決してキャッシュしています。後続の操作は、キャッシュされている resolution を使用して制約付きリソースにアクセスできます。アクターはシングル スレッドの実行をサポートするため、開発者はリソースへの同時アクセスについて心配する必要がなくなります。User アクターと Resolver アクターは次のようになります。

## リソース ガバナンスのコード サンプル – Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : StatefulActor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

リソース ガバナンス – Resolver の例

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : StatefulActor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## 有限の機能を持つリソースへのアクセス

もう 1 つの例を見てみましょう。有限のスループット機能を備えた DB、ストレージ アカウント、ファイル システムなどの貴重なリソースへの排他アクセスです。このシナリオは次のようなものです。EventProcessor と呼ばれるアクターを使用してイベントを処理します。このアクターは、イベントを処理して保存する役割を持ちます。ここでは、わかりやすくするために .CSV ファイルに保存します。リソースをスケールアウトするために前に説明したパーティション分割を実行できますが、それでも同時実行の問題に対処する必要があります。これが、この特定のポイントを説明するためにファイルに基づく例を選択した理由です。複数のアクターから 1 つのファイルに書き込むと、同時実行の問題が発生します。問題に対処するため、制約付きリソースの排他的所有権を持つ EventWriter という名前の別のアクターを導入します。このシナリオを次に示します。

![][3]

EventProcessor アクターを "ステートレス worker" として指定します。これにより、ランタイムは必要に応じてアクターをクラスター全体に拡張できます。そのため、上の図ではこれらのアクターに識別子を使いませんでした。つまり、ステートレス アクターはランタイムによって管理される worker のプールです。次のサンプル コードでは、EventProcessor アクターは 2 つの処理を行っています。最初に使用する EventWriter (したがってリソース) を決定し、選択したアクターを呼び出して処理されたイベントを書き込みます。わかりやすくするために、EventWriter アクターの識別子を Event Type にしています。つまり、この Event Type にはただ 1 つだけの EventWriter があり、リソースへのシングル スレッドの排他アクセスを提供します。

## リソース ガバナンスのコード サンプル – イベント プロセッサ

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

それでは、EventWriter アクターを見てみましょう。実際、制約付きリソースへの排他アクセスの制御以外にはたいしたことを行っていません。この場合は、ファイルとそれへのイベントの書き込みです。
## リソース ガバナンスのコード サンプル – イベント ライター

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

リソースを 1 つのアクターで担当することにより、バッファリングなどの機能を追加できます。受信イベントをバッファーに入れ、タイマーを使用して定期的に、またはバッファーがいっぱいになったときに、これらのイベントを書き込みます。次に示すのは単純なタイマーの例です。
## リソース ガバナンスのコード サンプル – バッファーを使用するイベント ライター

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

上記のコードは問題なく動作しますが、クライアントはイベントが基になるストアに到達したかどうかわかりません。バッファーを可能にし、クライアントがその要求に何が起こっているのかを認識できるようにするため、次のアプローチを導入して、イベントが .CSV ファイルに書き込まれるまでクライアントが待機できるようにします。

## リソース ガバナンスのコード サンプル – 非同期バッチ処理

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

このクラスを使用して、(クライアントをブロックするため) 完了していないタスクのリストを作成して保持し、バッファーのイベントをストレージに書き込んだら一度に完了します。EventWriter クラスでは、次の 3 つのことを行う必要があります。アクター クラスを再入可能としてマークし、SubmitNext() の結果を返し、タイマーをフラッシュします。変更したコードは、次のとおりです。

## リソース ガバナンスのコード サンプル – 非同期バッチ処理のあるバッファリング

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

簡単に見えるでしょうか。 そのとおりです。しかし、簡単ですがエンタープライズ機能を備えています。このアーキテクチャでは次のことを実現します。

* 場所から独立したリソースのアドレス指定。
* リソースの代わりに機能するアクターの数の変更だけに基づく調整可能なプール サイズ。
* クライアント側で調整するプールの使用 (示したとおりです) またはサーバー側の調整 (図でこれらの各プールの前に 1 つのアクターがあることを想像してください)。
* スケーラブルなプールの追加 (新しいリソースを表すアクターを追加するだけ)。
* アクターは (前に示したように) オンデマンドでバックエンド リソースからの結果をキャッシュするかタイマーを使用して事前キャッシュし、バックエンド リソースをヒットする必要性を少なくできます。
* 効率的な非同期ディスパッチ。
* ミドルウェアのスペシャリストだけでなくすべての開発者に馴染みのあるコーディング環境。

このパターンは、制約のあるリソースに対して開発する必要がある場合、または大規模なスケールアウト システムを構築する場合に、非常に一般的なシナリオです。


## 次のステップ

[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=Nov15_HO4-->