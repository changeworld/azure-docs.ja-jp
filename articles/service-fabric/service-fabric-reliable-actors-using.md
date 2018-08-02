---
title: Azure Service Fabric アクターでの機能の実装 | Microsoft Docs
description: StatefulService を継承したときと同様にサービス レベルの機能を実装する独自のアクター サービスを記述する方法について説明します。
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 6aff9e9599d31942f994f3cb4e5e9219f33dc7e1
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205522"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>アクター サービスでのサービス レベルの機能の実装
[サービスのレイヤー](service-fabric-reliable-actors-platform.md#service-layering)で説明したように、アクター サービス自体は信頼できるサービスです。  StatefulService を継承したときと同様に、`ActorService` から派生した独自のサービスを記述し、サービス レベルの機能を実装できます。次にその例を示します。

- サービスのバックアップと復元。
- すべてのアクターで共有される機能 (サーキット ブレーカーなど)。
- アクター サービス自体と個別アクターでのリモート プロシージャ コール。

## <a name="using-the-actor-service"></a>アクター サービスの使用
アクター インスタンスは、それが実行されるアクター サービスにアクセスできます。 アクター インスタンスは、アクター サービスを使用して、サービス コンテキストをプログラムによって取得できます。 サービス コンテキストには、パーティション ID、サービス名、アプリケーション名、他の Service Fabric プラットフォーム固有の情報が含まれます。

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

アクター サービスは、すべての Reliable Services と同様に、Service Fabric ランタイムのサービスの種類に登録する必要があります。 アクター サービスでアクター インスタンスを実行するためには、アクター型もアクター サービスに登録する必要があります。 `ActorRuntime` 登録メソッドは、アクターに対してこの処理を実行します。 最も単純なケースでは、単にアクターの種類を登録するだけでかまいません。そうすれば、既定の設定のアクター サービスが暗黙的に使用されます。

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

また、登録メソッドによって提供されるラムダを使ってアクター サービスを自分で構築することもできます。 これでアクター サービスを構成し、アクター インスタンスを明示的に構築して、コンストラクターを通じてアクターに依存関係を挿入できます。

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>アクター サービスのメソッド
アクター サービスは `IActorService` (C#) または `ActorService` (Java) を実装し、それが `IService` (C#) または `Service` (Java) を実装します。 これは、Reliable Services リモート処理によって使用されるインターフェイスであり、サービス メソッドでのリモート プロシージャ呼び出しを可能にします。 これには、サービスのリモート処理によってリモートで呼び出すことのできるサービスレベルのメソッドが含まれており、それにより、アクターの[列挙](service-fabric-reliable-actors-enumerate.md)と[削除](service-fabric-reliable-actors-delete-actors.md)が可能になっています。


## <a name="custom-actor-service"></a>カスタム アクター サービス
アクター登録ラムダを使用することで、`ActorService` (C#) および `FabricActorService` (Java) から派生する独自のカスタム アクター サービスを登録できます。 このカスタム アクター サービスでは、`ActorService` (C#) または `FabricActorService` (Java) を継承するサービス クラスを記述することで、独自のサービス レベル機能を実装できます。 カスタム アクター サービスは、`ActorService` (C#) または `FabricActorService` (Java) からすべてのアクター ランタイム機能を継承します。カスタム アクター サービスを使えば、独自のサービス メソッドを実装できます。

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>アクターのバックアップと復元の実装
カスタム アクター サービスは、既に `ActorService` に存在するリモート処理リスナーを活用して、アクター データをバックアップするメソッドを公開することができます。  例については、[アクターのバックアップおよび復元](service-fabric-reliable-actors-backup-and-restore.md)を参照してください。

## <a name="actor-using-remoting-v2interfacecompatible-stack"></a>リモート処理 V2(InterfaceCompatible) スタックを使用するアクター
リモート処理 V2(InterfaceCompatible、別名 V2_1) スタックは、V2 リモート処理スタックのすべての機能を備えています。リモート処理 V1 スタックに対するインターフェイス互換スタックですが、V2 および V1 との後方互換性はありません。 サービスの可用性に影響を与えずに V1 から V2_1 へのアップグレードを行うには、[こちら](#actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability)に従ってください。

リモート処理 V2_1 スタックを使用するには、次の変更が必要です。
 1. アクター インターフェイスで次の Assembly 属性を追加します。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. ActorService とアクター クライアント プロジェクトをビルドしてアップグレードし、V2 スタックの使用を開始します。

#### <a name="actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability"></a>サービスの可用性に影響を与えずにアクター サービスをリモート処理 V2(InterfaceCompatible) スタックにアップグレードする
この変更は、2 段階に分かれたアップグレードです。 次に示す順序で手順に従います。

1.  アクター インターフェイスで次の Assembly 属性を追加します。 この属性は、ActorService 用の 2 つのリスナーである、V1 (既存) および V2_1 リスナーを開始します。 この変更で ActorService をアップグレードします。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. 上記アップグレードの完了後、ActorClients をアップグレードします。
この手順により、アクター プロキシは確実にリモート処理 V2_1 スタックを使用します。

3. この手順は省略可能です。 上記の属性を変更して V1 リスナーを削除します。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-using-remoting-v2-stack"></a>リモート処理 V2 スタックを使用したアクター
2.8 Nuget パッケージによって、ユーザーは、カスタムのシリアル化などの機能を提供する高性能なリモート処理 V2 スタックを使用できるようになりました。 リモート処理 V2 には、既存のリモート処理スタック (これからは V1 リモート処理スタックと呼びます) との下位互換性がありません。

リモート処理 V2 スタックを使用するには、次の変更が必要です。
 1. アクター インターフェイスで次の Assembly 属性を追加します。
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. ActorService とアクター クライアント プロジェクトをビルドしてアップグレードし、V2 スタックの使用を開始します。

#### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>サービスの可用性に影響を与えずにアクター サービスをリモート処理 V2 スタックにアップグレードする
この変更は、2 段階に分かれたアップグレードです。 次に示す順序で手順に従います。

1.  アクター インターフェイスで次の Assembly 属性を追加します。 この属性は、ActorService 用の 2 つのリスナーである、V1 (既存) および V2 リスナーを開始します。 この変更で ActorService をアップグレードします。

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. 上記アップグレードの完了後、ActorClients をアップグレードします。
この手順により、アクター プロキシは確実にリモート処理 V2 スタックを使用します。

3. この手順は省略可能です。 上記の属性を変更して V1 リスナーを削除します。

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>次の手順
* [アクターの状態管理](service-fabric-reliable-actors-state-management.md)
* [アクターのライフサイクルとガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)
* [アクターの API リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET サンプル コード](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java サンプル コード](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
