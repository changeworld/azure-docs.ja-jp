<properties
   pageTitle="Reliable Actors の再入 | Microsoft Azure"
   description="Service Fabric Reliable Actors の再入の概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/19/2016"
   ms.author="amanbha"/>


# Reliable Actors の再入
既定では、Fabric Actors ランタイムは、論理呼び出しコンテキスト ベースの再入を許可します。これにより、アクターは、同じ呼び出しコンテキストのチェーンにある場合は再入可能になります。たとえば、アクター A がアクター C にメッセージを送信するアクター B にメッセージを送信するとします。メッセージ処理の一環として、アクター C がアクター A を呼び出した場合、メッセージは再入可能であるため、再入が許可されます。処理が完了するまで、アクター A では別の呼び出しコンテキストの一部である他のメッセージがブロックされます。

論理呼び出しコンテキスト ベースの再入を許可しないようにするアクターは、アクター クラスを `ReentrantAttribute(ReentrancyMode.Disallowed)` で装飾することで無効にできます。

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

次のコードは、再入モードを `ReentrancyMode.Disallowed` に設定したアクター クラスを示しています。この場合、アクターが別のアクターに再入メッセージを送信すると、`FabricException` 型の例外がスローされます。

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```

<!---HONumber=AcomDC_0224_2016-->