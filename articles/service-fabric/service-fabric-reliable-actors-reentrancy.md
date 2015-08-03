<properties
   pageTitle="Azure Service Fabric のアクターの再入"
   description="Azure Service Fabric のアクターの再入の概要"
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
   ms.date="07/09/2015"
   ms.author="amanbha"/>


# アクターの再入
Fabric アクターは、既定で、論理呼び出しコンテキスト ベースの再入を使用できます。これにより、アクターは、同じ呼び出しコンテキストのチェーンにある場合は再入可能になります。たとえば、アクター A がアクター C にメッセージを送信するアクター B にメッセージを送信する場合です。アクター C がアクター A を呼び出す場合は、メッセージ処理の一環として、メッセージが許可されるように再入可能です。処理が完了するまで、アクター A では異なる呼び出しコンテキストの一部である他のメッセージがブロックされます。

論理呼び出しコンテキスト ベースの再入を許可しないようにするアクターは、アクター クラスを `ReentrantAttribute(ReentrancyMode.Disallowed)` で装飾することで無効にできます。

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

次のコードは再入モードを `ReentrancyMode.Disallowed` に設定するアクター クラスを示します。ここでは、アクターが別のアクターに再入メッセージを送信する場合、`FabricException` の型の例外がスローされます。

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```
 

<!---HONumber=July15_HO4-->