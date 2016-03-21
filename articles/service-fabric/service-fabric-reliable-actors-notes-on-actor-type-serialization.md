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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Service Fabric Reliable Actors 型のシリアル化に関する留意事項

アクターのインターフェイスと状態を定義する場合、いくつかの重要な側面に留意する必要があります。型は、シリアル化可能なデータ コンストラクトである必要があります。データ コントラクトの詳細については、「[データ コントラクト シリアライザーでサポートされる型](https://msdn.microsoft.com/library/ms731923.aspx)」を参照してください。

## アクター インターフェイス型。

[アクター インターフェイス](service-fabric-reliable-actors-introduction.md#actors)で定義されているすべてのメソッドの引数と各メソッドによって返されるタスクの結果の型は、データ コントラクト シリアル化可能でなければなりません。これは、[アクター イベント インターフェイス](service-fabric-reliable-actors-events.md#actor-events)で定義されているメソッドの引数にも当てはまります(アクター イベント インターフェイス メソッドは常に void を返します)。 たとえば、`IVoiceMail` インターフェイスで以下のようにメソッドを定義するとします。

```csharp

Task<List<Voicemail>> GetMessagesAsync();

```

この場合、`List<T>` は既にデータ コントラクト シリアル化可能な標準の .NET 型です。また、`Voicemail` 型もデータ コントラクト シリアル化可能にする必要があります。

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

## アクター状態のクラス

アクター状態をデータ コントラクト シリアル化可能にする必要があります。たとえば、アクター クラス定義は次のようになります。

```csharp

public class VoiceMailActor : StatefulActor<VoicemailBox>, IVoiceMail
{
...

```

この場合、状態クラスはクラスとそのメンバーで定義され、注釈として **DataContract** および **DataMember** という属性がそれぞれ付けられます。

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

<!---HONumber=AcomDC_0121_2016-->