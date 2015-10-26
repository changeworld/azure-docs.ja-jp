<properties
   pageTitle="Azure Service Fabric アクターの分散ネットワークおよびグラフの設計パターン"
   description="Service Fabric アクターを使用して、分散ネットワークやグラフとしてアプリケーションをモデル化する方法に関する設計パターン"
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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# 高信頼アクターの設計パターン: 分散ネットワークおよびグラフ
Service Fabric 高信頼アクターは、関係に関する複雑なソリューションのモデル化とそれらの関係のオブジェクトとしてのモデル化に自然に適合します。

![][1]

図に示されているように、アクター インスタンス (ネットワーク内のノード) としてユーザーをモデル化するのは簡単です。たとえば、「友人フィード」(「フォロワー」の問題ともいう) では、Facebook や Twitter の動作と同じように、ユーザーは接続されている別のユーザーからの状態の更新を表示することができます。アクター モデルは、実体化の問題に対処する柔軟性を提供します。下図のように、イベント時に友人フィードを設定して、更新の投稿時に自分のすべての友人の友人フィードを更新できます。

![][2]


## スマート キャッシュのコード サンプル – ソーシャル ネットワークの友人フィード (イベント時間)

友人フィードを設定するサンプル コード:

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

public class SocialPerson : Actor, ISocialPerson
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

アクターをモデル化して、クエリ タイマーで (つまり、ユーザーが友人フィードを要求したときに) 友人フィードを展開およびコンパイルすることもできます。使用可能な別の方法では、タイマーで (たとえば、5 分ごとに) 友人フィードを実体化します。または、モデルを最適化し、イベント時間とクエリ時間の両方を組み合わせて、ユーザーの習慣 (ログインや更新投稿の頻度など) に応じてタイマー ベースのモデルで処理することができます。ソーシャル ネットワークでアクターをモデル化する場合は、何百万ものフォロワーを持つ「スーパー ユーザー」というユーザーを考慮する必要もあります。開発者は、ニーズに合わせて異なる方法でそのようなユーザーの状態と動作をモデル化する必要があります。同様に、多くのユーザー アクターを単一のアクティビティ アクターに接続するアクティビティ (ハブとスポーク) をモデル化する必要がある場合も同じように実行できます。2 つの例として、グループ チャットとゲームのホスティング シナリオがあります。ここでは、グループ チャットの例を見てみましょう。以下の例のように、一連の参加者は 1 人の参加者からのメッセージをグループに配信できるグループ チャット アクターを作成します。

## スマート キャッシュのコード サンプル – GroupChat

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

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
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


public class GroupChat : Actor<GroupChatState>, IGroupChat
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

実際には、信頼性の高いアクターの機能を利用するだけです。この機能により、アクターは ID でクラスター内の別のアクターのアドレスを指定し、通信できるようになります。配置、アドレス指定、キャッシュ、メッセージング、シリアル化、またはルーティングについて心配する必要はありません。

## 次のステップ
[パターン: スマート キャッシュ](service-fabric-reliable-actors-pattern-smart-cache.md)

[パターン: リソースのガバナンス](service-fabric-reliable-actors-pattern-resource-governance.md)

[パターン: ステートフル サービスの構成](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[パターン: モノのインターネット](service-fabric-reliable-actors-pattern-internet-of-things.md)

[パターン: 分散計算](service-fabric-reliable-actors-pattern-distributed-computation.md)

[いくつかのアンチ パターン](service-fabric-reliable-actors-anti-patterns.md)

[Service Fabric アクターの概要](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=Oct15_HO3-->