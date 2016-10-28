<properties
   pageTitle="Reliable Actors のアクター型のシリアル化に関する留意事項 | Microsoft Azure"
   description="Service Fabric Reliable Actors の状態とインターフェイスを定義する場合に使用できるシリアル化可能なクラスを定義するための基本的な要件について説明します。"
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
   ms.date="07/06/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors 型のシリアル化に関する留意事項


すべてのメソッドの引数、アクター インターフェイスの各メソッドによって返されるタスクの結果の型、アクターの状態マネージャーに保存されるオブジェクトは、[シリアル化可能なデータ コントラクト](https://msdn.microsoft.com/library/ms731923.aspx)である必要があります。これは、[アクター イベント インターフェイス](service-fabric-reliable-actors-events.md#actor-events)で定義されているメソッドの引数にも当てはまります (アクター イベント インターフェイス メソッドは常に void を返します)。

## カスタム データ型

次の例では、アクター インターフェイスで `VoicemailBox` というカスタム データ型を返すメソッドを定義しています。

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

このインターフェイスは、状態マネージャーを使用して `VoicemailBox` オブジェクトを保存するアクターによって実装されます。

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

この例では、`VoicemailBox` オブジェクトは次の状況でシリアル化されます。
 - オブジェクトがアクター インスタンスと呼び出し元の間で送信されるとき。
 - オブジェクトが状態マネージャーに保存されるとき (状態マネージャーでオブジェクトがディスクに保存され、他のノードにレプリケートされます)。
 
Reliable Actors フレームワークでは、DataContract シリアル化を使用します。そのため、カスタム データ オブジェクトとそのメンバーに、それぞれ **DataContract** 属性と **DataMember** 属性を注釈として付ける必要があります。

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## 次のステップ
 - [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
 - [アクターのタイマーとアラーム](service-fabric-reliable-actors-timers-reminders.md)
 - [アクター イベント](service-fabric-reliable-actors-events.md)
 - [アクターの再入](service-fabric-reliable-actors-reentrancy.md)
 - [アクターのポリモーフィズムとオブジェクト指向設計パターン](service-fabric-reliable-actors-polymorphism.md)
 - [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)

<!---HONumber=AcomDC_0713_2016-->