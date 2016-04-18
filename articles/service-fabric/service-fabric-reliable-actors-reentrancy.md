<properties
   pageTitle="Reliable Actors の再入 | Microsoft Azure"
   description="Service Fabric Reliable Actors の再入の概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>


# Reliable Actors の再入
Reliable Actors ランタイムは、論理呼び出しコンテキスト ベースの再入を既定で許可します。これにより、アクターは、同じ呼び出しコンテキストのチェーンにある場合は再入可能になります。たとえば、アクター A がアクター C にメッセージを送信するアクター B にメッセージを送信するとします。メッセージ処理の一環として、アクター C がアクター A を呼び出した場合、メッセージは再入可能であるため、再入が許可されます。処理が完了するまで、アクター A では別の呼び出しコンテキストの一部である他のメッセージがブロックされます。

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
class MyActor : Actor, IMyActor
{
    ...
}
```

## 次のステップ
 - [アクターの診断とパフォーマンスの監視](service-fabric-reliable-actors-diagnostics.md)
 - [Actor API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [コード サンプル](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0406_2016-->