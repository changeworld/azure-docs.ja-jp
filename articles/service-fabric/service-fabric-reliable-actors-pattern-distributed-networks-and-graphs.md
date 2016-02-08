<properties
   pageTitle="分散ネットワークとグラフのパターン | Microsoft Azure"
   description="Service Fabric Reliable Actors を使用して、分散ネットワークやグラフとしてアプリケーションをモデル化する方法に関する設計パターン"
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
   ms.date="09/29/2015"
   ms.author="vturecek"/>

# Reliable Actors の設計パターン: 分散ネットワークおよびグラフ
Azure Service Fabric Reliable Actors プログラミング モデルは、関係に関する複雑なソリューションのモデル化とそれらの関係のオブジェクトとしてのモデル化に自然に適合します。

![Azure Service Fabric Reliable Actors のモデリング][1]

上図のように、アクター インスタンス (ネットワーク内のノード) としてユーザーをモデル化するのが簡単です。たとえば、"友人フィード" ("フォロワー" の問題ともいう) では、Facebook や Twitter の動作と同じように、ユーザーは接続されている別のユーザーからの状態の更新を表示することができます。

Reliable Actors モデルには、実体化の問題に対処できる柔軟性があります。下図のように、イベント時に友人フィードを設定して、更新の投稿時に自分のすべての友人の友人フィードを更新できます。

![Reliable Actors モデルと友人フィードの設定][2]


## スマート キャッシュのコード サンプル -- ソーシャル ネットワークの友人フィード (イベント時間)

友人フィードを設定するためのサンプル コード:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : StatefulActor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

アクターをモデル化して、クエリ タイマーで (ユーザーが友人フィードを要求したときに) 友人フィードを展開およびコンパイルすることもできます。また、タイマーに基づいて (たとえば、5 分ごとに) 友人フィードを実体化することもできます。または、モデルを最適化し、イベント時間とクエリ時間の両方を組み合わせて、ユーザーの習慣 (ログインや更新投稿の頻度など) に応じてタイマー ベースのモデルで処理することができます。

ソーシャル ネットワークでアクターをモデル化する場合は、何百万ものフォロワーを持つ "スーパー ユーザー" というユーザーを考慮する必要もあります。開発者は、高まるニーズに合わせて異なる方法でそのようなユーザーの状態と動作をモデル化する必要があります。

同様に、多くのユーザー アクターを単一のアクティビティ アクターに接続するアクティビティ (ハブとスポーク) をモデル化する必要がある場合も同じように実行できます。たとえば、グループ チャットとゲームのホストという 2 つの例があります。グループ チャットの例を見てみましょう。ある参加者セットは、1 人の参加者からのメッセージをグループに分散できるグループ チャット アクターを作成します。これを次の例で示します。

## スマート キャッシュのコード サンプル -- GroupChat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : StatefulActor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : StatefulActor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

このアプローチは、Reliable Actors モデルが、クラスター内の他のアクターの ID でアドレスを指定できる機能を利用しています。配置、アドレス指定、キャッシュ、メッセージング、シリアル化、またはルーティングを心配することなく、通信できます。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチパターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=AcomDC_0128_2016-->