<properties
   pageTitle="リソース ガバナンス設計パターン | Microsoft Azure"
   description="Service Fabric Reliable Actors を利用し、拡張するが制約のあるリソースを使用するアプリケーション ニーズをモデル化する設計パターン。"
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

# Reliable Actors の設計パターン: リソースのガバナンス

このパターンおよび関連するシナリオは、すぐに拡張できない制約されたリソースがオンプレミスまたはクラウドにある開発者あれば一見してわかります。大規模なアプリケーションとデータをクラウドに出荷する開発者 (エンタープライズまたはその他) にもよく知られています。

企業では、データベースなどのこのような制約されたリソースはスケールアップ ハードウェアで実行されます。企業に長くいる開発者は誰でもそれがオンプレミスで一般的な状況であることを知っています。クラウド スケールでも、クラウド サービスがアドレス/ポート タプル間で 64,000 TCP の接続上限を超えようとしたとき、この状況が発生することを開発者は知っています。同時接続数を制限するクラウドベースのデータベースに接続を試行したときにも発生します。

通常これまでは、メッセージ ベースのミドルウェアを介した調整によって、またはカスタム ビルドのプールとファサード メカニズムによって、これは解決されました。ただし、これらを正しく行うのは難しく、中間層を拡大しながら適切な接続数を保持する必要があるときは特にそうです。脆弱で複雑なソリューションです。

スマート キャッシュ パターンのように、このパターンは複数のシナリオと、リソースが制限されたシステムを既に持っている顧客で利用されています。そのシステムにおいて、サービスだけでなく、メモリ内の状態と安定したストレージ内の永続化状態もスケールアウトする必要があります。

次の図は、このシナリオを示しています。

![ステートレス アクター、パーティション分割、制約付きリソース][1]

## アクターによるキャッシュ シナリオのモデル化

リソースまたはリソース グループへのプロキシ (たとえば接続など) として機能する 1 つまたは複数のアクターとしてリソースへのアクセスをモデル化できます。その後、個別のアクターまたはリソース アクターを管理する調整アクターを使用して、リソースを直接管理できます。

この概念をさらに具体的に説明するため、パフォーマンスとスケーラビリティの理由でパーティション分割された (シャーディングされた) 記憶域階層に対する一般的ニーズを取り上げます。最初の選択肢は非常に基本的です。静的関数を使用してアクターを下流のリソースにマップして解決をします。このような関数は、たとえば、与えられた入力で接続文字列を返すことができます。その関数の実装方法は自由です。ただし、この方法には欠点があり、たとえば、静的アフィニティがリソースの再分割やリソースへのアクターの再マッピングを難しくします。

単純な例を次に示します。モジュラー演算を行い、**ユーザー ID** でデータベース名を決定し、**リージョン**でデータベース サーバーを特定します。

### リソース ガバナンスのコード サンプル: 静的解決

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

これは単純ですが、柔軟性に非常に欠けています。これより高度で役に立つ方法を見てみましょう。

最初に、物理リソースとアクターの間のアフィニティをモデル化します。これは「**リゾルバー**」と呼ばれるアクターで行います。これはユーザー、論理パターン、物理リソース間のマッピングを理解します。リゾルバーはそのデータを永続化ストアに保持しますが、キャッシュされるので簡単に検索できます。[スマート キャッシュ パターンの Exchange Rate サンプル](service-fabric-reliable-actors-pattern-smart-cache.md)で示したように、リゾルバーはタイマーを使用して最新の情報を事前にフェッチできます。User アクターは、使用する必要があるリソースを解決すると、「\_resolution」という名前のローカル変数にリソースをキャッシュし、リソースの有効期間中はこれを使用します。

操作での柔軟性のため、単純ハッシュや範囲ハッシュではなく、検索ベースの解決を選択しました (下図参照)。操作とは、スケールイン/スケールアウトやリソース間のユーザーの移動などです。

![ルックアップ リゾルバー ソリューション][2]

上の図では、アクター B23 が最初にリソース (resolution) **DB1** を解決してキャッシュしています。後続の操作は、キャッシュされている resolution を使用して制約付きリソースにアクセスできます。アクターはシングル スレッドの実行をサポートするため、開発者はリソースへの同時アクセスについて心配する必要がなくなります。次のコード サンプルでユーザーとリゾルバーのアクターを見つけてください。

### リソース ガバナンスのコード サンプル: リゾルバー

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

#### リソース ガバナンス: リゾルバーの例

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

## 機能が限定されたリソースにアクセスする

もう 1 つの例を見てみましょう。有限のスループット機能を備えた、データベース、ストレージ アカウント、ファイル システムなどの貴重なリソースへの排他アクセスです。このシナリオでは、EventProcessor と呼ばれるアクターを使用してイベントを処理します。このアクターは、イベントを処理し、永続化する作業を担当します。この例では、わかりやすくするために .csv ファイルに保存します。リソースをスケールアウトするために前に説明したパーティション分割を実行できますが、それでも同時実行の問題に対処する必要があります。この点を示すためにファイルに基づく例を選択しました。複数のファイルから 1 つのファイルに書き込むと、同時実行の問題が発生するためです。この問題に対処するため、制約付きリソースの排他的所有権を持つ EventWriter という名前の別のアクターを導入します。このシナリオを次に示します。

![EventWriter と EventProcessor を利用したイベントの書き込みと処理][3]

EventProcessor アクターを「ステートレス worker」として指定します。これにより、ランタイムは必要に応じてアクターをクラスター全体に拡張できます。上の図ではこれらのアクターに識別子を使いませんでした。ステートレス アクターはランタイムによって管理される worker のプールです。

次のサンプル コードでは、EventProcessor アクターは 2 つことを行います。最初に、使用する EventWriter (とリソース) を決定し、選択したアクターを呼び出し、処理されたイベントを書き込みます。わかりやすくするために、EventWriter アクターの識別子をイベント タイプとして選択しました。そのため、このイベント タイプには EventWriter が 1 つだけあり、リソースへのシングル スレッドの排他アクセスを提供します。

### リソース ガバナンスのコード サンプル: EventProcessor

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

それでは、EventWriter アクターを見てみましょう。これは制約付きリソースへの排他アクセスを制御します。この場合、ファイルとそれへのイベントの書き込みですが、それ以外、たいした制御はありません。

### リソース ガバナンスのコード サンプル: EventWriter

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

リソースを 1 つのアクターで担当することにより、バッファリングなどの機能を追加できます。受信イベントをバッファーに入れ、タイマーを使用して定期的に、またはバッファーがいっぱいになったときに、これらのイベントを書き込みます。次のコード サンプルは、タイマー ベースの単純な例です。

### リソース ガバナンスのコード サンプル: バッファーを使用する EventWriter

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

上記のコードは問題なく動作しますが、クライアントはイベントが基になるストアに到達したかどうかわかりません。バッファーを可能にし、その要求に起こっていることに関する情報をクライアントに提供するために、次の方法で、イベントが .CSV ファイルに書き込まれるまでクライアントが待機できるようにします。

### リソース ガバナンスのコード サンプル: 非同期バッチ処理

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

このクラスを利用し、(クライアントをブロックする) 不完全タスクの一覧を作成し、保持します。バッファーされたイベントをストレージに書き込んだら、1 回のパスで完了します。

EventWriter クラスでは、次の 3 つのことを行う必要があります。アクター クラスを再入可能としてマークし、**SubmitNext()** の結果を返し、タイマーをフラッシュします。次の変更後のコードを参照してください。

### リソース ガバナンスのコード サンプル: 非同期バッチ処理のあるバッファリング

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

この手法は簡単ですが、エンタープライズ機能を備えています。このアーキテクチャを使用すると、次の操作が可能になります。

* 場所から独立したリソースのアドレス指定。
* リソースの代わりに機能するアクターの数の変更だけに基づく調整可能なプール サイズ。
* クライアント側で調整するプールの使用 (示したとおりです) またはサーバー側の調整 (画像でこれらの各プールの前に 1 つのアクターがあることを想像してください)。
* スケーラブルなプールの追加 (新しいリソースを表すアクターを追加するだけ)。
* 必要に応じてバックエンド リソースからの結果をキャッシュできる、あるいはタイマーを利用して事前キャッシュできるアクター (前に説明済み)。これにより、バックエンド リソースを検索する必要性が軽減されます。
* 効率的な非同期ディスパッチ。
* ミドルウェアのスペシャリストだけでなくすべての開発者に馴染みのあるコーディング環境。

このパターンは、制約のあるリソースに対して開発する必要がある場合、非常に一般的なシナリオです。大規模なスケールアウト システムを構築する場合にも一般的です。


## 次のステップ

[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: 分散ネットワークとグラフ](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチパターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric Reliable Actors の概要](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->