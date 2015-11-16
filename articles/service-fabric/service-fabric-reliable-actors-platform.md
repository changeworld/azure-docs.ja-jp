<properties
   pageTitle="Service Fabric の高信頼アクター | Microsoft Azure"
   description="Service Fabric プラットフォームの機能を高信頼アクターが使用するしくみについて説明します。アクター開発者の視点から見た概念も取り上げます。"
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
   ms.date="08/05/2015"
   ms.author="abhisram"/>

# 高信頼アクターの Service Fabric プラットフォームの使用方法

アクターは、Service Fabric アプリケーション モデルを使用して、アプリケーションのライフサイクルを管理します。すべてのアクターの型は、Service Fabric の[サービスの種類](service-fabric-application-model.md#describe-a-service)にマップされます。アクター コードは Service Fabric アプリケーションとして[パッケージ化され](service-fabric-application-model.md#package-an-application)、クラスターに[展開](service-fabric-deploy-remove-applications.md#deploy-an-application)されます。

## アクターのアプリケーション モデルの概念の例

上記の概念の一部を説明するため、[Visual Studio を使用して作成された](service-fabric-reliable-actors-get-started.md)、アクター プロジェクトの例を見てみましょう。

ソリューションが Visual Studio で作成されると、アプリケーション マニフェスト、サービス マニフェスト、Settings.xml 構成ファイルがアクターのサービスのプロジェクトに含まれます。これを示したものが下記のスクリーンショットです。

![][1]

アプリケーションの種類およびアクターがパッケージ化されているアプリケーションのバージョンは、アクターのサービスのプロジェクトに含まれているアプリケーション マニフェストから参照できます。この例として、アプリケーション マニフェストの次のスニペットが挙げられます。

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

アクターの型をマップするサービスの種類は、アクターのサービスのプロジェクトに含まれているサービス マニフェストから参照できます。この例として、サービス マニフェストの次のスニペットが挙げられます。

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Visual Studio を使用してアプリケーション パッケージが作成されると、[ビルド出力] ウィンドウのログが、アプリケーション パッケージの場所を示します。次に例を示します。

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

上記の場所の一覧の一部を次に示します (省略は簡略化のため) 。

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───ja-JP
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───ja-JP
                    System.Fabric.Common.Internal.Strings.resources.dll

上記の一覧は、アプリケーション パッケージのサービス パッケージ内のコード パッケージに含まれている、VoicemailBox アクターを実装するアセンブリを示します。

Visual Studio のソリューションには、クラスターとの間のアプリケーションのデプロイおよび削除に使用される PowerShell スクリプトが含まれます。次のスクリーン ショットでは、スクリプトが丸で囲まれています。

![][2]

アプリケーションの後続の管理 (アップグレードや最終的な削除など) は、Service Fabric のアプリケーション管理メカニズムからも実行できます。詳細については、[アプリケーション モデル](service-fabric-application-model.md)、[アプリケーションのデプロイと削除](service-fabric-deploy-remove-applications.md)、および[アプリケーションのアップグレード](service-fabric-application-upgrade.md)に関する各トピックを参照してください。

## アクターのサービスの拡張性
クラスターの管理者は、クラスター内の各サービスの種類の 1 つ以上のアクターにサービスを作成できます。各アクターのサービスはそれぞれ、1 つまたは複数のパーティション (その他の Service Fabric サービスに似ています) を持つことができます。1 つのサービスの種類に複数のサービスを作成する (アクターの型にマップする) 機能や、サービスに複数のパーティションを作成する機能により、アクターのアプリケーションを拡張できるようになります。詳細については、「[拡張性](service-fabric-concepts-scalability.md)」の記事を参照してください。

> [AZURE.NOTE]ステートレス アクター サービスは、[インスタンス](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)数が 1 である必要があります。1 つのパーティション内に、複数インスタンスのステートレス アクター サービスを持つことはできません。そのため、ステートレス アクター サービスのスケーラビリティのためにインスタンス数を増やすことはできません。スケーラビリティ オプションを使用する必要があります ([スケーラビリティに関する記事](service-fabric-concepts-scalability.md)を参照してください)。

## アクターの Service Fabric のパーティションの概念
アクターのアクター ID は、アクターのサービスのパーティションにマップされます。そのアクター ID がマップされるパーティション内で、アクターが作成されます。アクターが作成されると、アクターのランタイムが、アクターが作成されるパーティションを示す [EventSource イベント](service-fabric-reliable-actors-diagnostics.md#eventsource-events)を記述します。下記はこのイベントの例で、ID `-5349766044453424161` のアクターが、サービス `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`、アプリケーション `fabric:/VoicemailBoxAdvancedApplication` のパーティション `0583c745-1bed-43b2-9545-29d7e3448156` 内で作成されたことを示します。

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

下記のイベントで示されているように、ID `-4952641569324299627` の別のアクターが同じサービスの別のパーティション `c146fe53-16d7-4d96-bac6-ef54613808ff` 内で作成されました。

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*注:* 簡略化のため、上記のイベントの一部のフィールドを省略しています。

パーティション ID はパーティションに関するその他の情報を取得するために使用できます。たとえば、[Service Fabric エクスプローラー](service-fabric-visualizing-your-cluster.md) ツールを使用して、パーティションおよびパーティションが属するサービスとアプリケーションに関する情報を表示することができます。次のスクリーン ショットはパーティション `c146fe53-16d7-4d96-bac6-ef54613808ff` に関する情報を表示しています。ここには上記の例の ID `-4952641569324299627` のアクターが含まれています。

![][3]

アクターは、アクターの型から派生する基本クラスの `Host.ActivationContext` および `Host.StatelessServiceInitialization` または `Host.StatefulServiceInitializationParameters` メンバーから、パーティション ID、サービス名、アプリケーション名、その他の Service Fabric プラットフォーム固有の情報をプログラムによって取得できます。次のコード スニペットで例を示します。

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### ステートレスなアクターの Service Fabric のパーティションの概念
ステートレスなアクターは、Service Fabric のステートレス サービスのパーティション内に作成されます。アクター ID によって、アクターが作成されるパーティションが決定されます。ステートレス アクター サービスの[インスタンス](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)数は 1 にする必要があります。インスタンス数は 1 以外の値に変更できません。そのため、アクターはパーティション内の 1 つのサービス インスタンス内に作成されます。

> [AZURE.TIP]Fabric アクターのランタイムは、[ステートレス アクターのインスタンスに関連するイベント](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances)をいくつか出力します。これらは、診断やパフォーマンスの監視に役立ちます。

ステートレスなアクターが作成されると、アクターのランタイムが、アクターが作成されるパーティションとインスタンスを示す [EventSource イベント](service-fabric-reliable-actors-diagnostics.md#eventsource-events)を記述します。下記はこのイベントの例で、ID `abc` のアクターが、パーティション `8c828833-ccf1-4e21-b99d-03b14d4face3`、サービス `fabric:/HelloWorldApplication/HelloWorldActorService`、アプリケーション `fabric:/HelloWorldApplication` のインスタンス `130745709600495974` で作成されたことを示します。

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

*注:* 簡略化のため、上記のイベントの一部のフィールドを省略しています。

### ステートフル アクターの Service Fabric パーティションの概念
ステートフル アクターは、Service Fabric ステートフル サービスのパーティション内に作成されます。アクター ID によって、アクターが作成されるパーティションが決定されます。サービスの各パーティションは、クラスターの異なるノードに配置される 1 つまたは複数の[レプリカ](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)を持つことができます。複数のレプリカを持つことで、アクターの状態に信頼性が得られます。リソース マネージャーは、クラスター内にある障害とアップグレード ドメインに基づいて配置を最適化します。同じノードに、同じパーティションの 2 つのレプリカが配置されることはありません。アクターは、そのアクター ID がマップされるパーティションのプライマリ レプリカに常に作成されます。

> [AZURE.TIP]Fabric アクターのランタイムは、[ステートフル アクターのレプリカに関連するイベント](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas)をいくつか出力します。これらは、診断やパフォーマンスの監視に役立ちます。

[先に説明したVoiceMailBoxActor の例](#service-fabric-partition-concepts-for-actors)で、ID `-4952641569324299627` のアクターがパーティション `c146fe53-16d7-4d96-bac6-ef54613808ff` に作成されたことを思い出してください。その例の EventSource イベントも、アクターがそのパーティションのレプリカ `130745418574851853` に作成されたことを示していました。これは、アクターが作成された時点で、そのパーティションのプライマリ レプリカでした。次の Service Fabric エクスプ ローラーのスクリーン ショットはこれを表しています。

![][4]

## アクターの状態プロバイダーの選択
アクター ランタイムに含まれる既定のアクター状態プロバイダーがいくつかあります。アクターのサービスに適切な状態プロバイダーを選択するには、状態プロバイダーが基になる Service Fabric プラットフォーム機能をどのように使用してアクターの状態の可用性を高めるかを理解する必要があります。

既定では、ステートフル アクターはキー値ストアのアクター状態プロバイダーを使用します。この状態プロバイダーは、Service Fabric プラットフォームで提供される分散キー値ストアに構築されます。状態はプライマリ [レプリカ](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services)をホストしているノードのローカル ディスクに永続的に保存されるだけでなく、セカンダリ レプリカをホストしているノードのローカル ディスクにも複製され、永続的に保存されます。レプリカのクォーラムがローカル ディスクに状態をコミットした場合にのみ、状態の保存が完了します。キー値ストアに、障害の状況などの不整合を検出し、これを自動的に修正する高度な機能が備わりました。

アクターのランタイムには `VolatileActorStateProvider` も含まれています。この状態プロバイダーは、状態をレプリカにレプリケートしますが、状態はレプリカのメモリ内に残ります。1 つのレプリカがダウンして復帰した場合、その状態は他のレプリカから再構築されます。ただし、すべてのレプリカ (状態のコピー) が同時にダウンした場合、状態のデータは失われます。そのため、この状態プロバイダーは、複数のレプリカで障害があってもデータを保護し、アップグレードなどの計画されたフェールオーバーを切り抜けることができるアプリケーションで適しています。すべてのレプリカ (コピー) が失われた場合は、Service Fabric の外部のメカニズムを使用してデータを再作成する必要があります。`VolatileActorStateProvider` 属性をアクターのクラスまたはアセンブリ レベルの属性に追加することで、ステートフル アクターが揮発性のあるアクターの状態プロバイダーを使用するように構成できます。

次のコード スニペットは、明示的な状態プロバイダーの属性を持たないアセンブリのすべてのアクターを `VolatileActorStateProvider` を使用するように変更する方法を示しています。

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

次のコード スニペットは、特定のアクターの型 (ここでは `VoicemailBox`) の状態プロバイダーを `VolatileActorStateProvider` に変更する方法を示しています。

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

状態プロバイダーを変更するには、アクターのサービスを再作成する必要があることに注意してください。状態プロバイダーはアプリケーションのアップグレードの一部として変更できません。

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=Nov15_HO2-->