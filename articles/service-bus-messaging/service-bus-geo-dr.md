---
title: "Azure Service Bus の geo ディザスター リカバリー | Microsoft Docs"
description: "フェールオーバーに地理上の地域を使用して、Azure Service Bus のディザスター リカバリーを実行する方法"
services: service-bus-messaging
documentationcenter: 
author: christianwolf42
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: sethm
ms.openlocfilehash: 49f2992245d694f85b7b1f1c34339f1445c9d699
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="azure-service-bus-geo-disaster-recovery-preview"></a>Azure Service Bus の geo ディザスター リカバリー (プレビュー)

地域のデータ センターにダウンタイムが発生した場合に、別の地域またはデータ センターでデータ処理が続行されることが重要です。 そのため、*geo ディザスター リカバリー*と *geo レプリケーション*は、どの企業にとっても重要な機能です。 Azure Service Bus は、名前空間のレベルで、 geo ディザスター リカバリーと geo レプリケーションの両方をサポートします。 

geo ディザスター リカバリーのプレビューは、現在 2 つの地域 (**米国中北部**と**米国中南部)** のみで利用可能です。

## <a name="outages-and-disasters"></a>故障と災害

[Service Bus の障害および災害に対するアプリケーション保護のベスト プラクティス](service-bus-outages-disasters.md)の資料では、以下の点を重要視して、「故障」と「災害」を区別しています。 *故障*とは、Azure Service Bus が一時的に使用不可能になっていることです。メッセージング ストアなどサービスの一部のコンポーネントや、場合によってはデータセンター全体に影響を与えることがあります。 しかし、問題が解決されると、Service Bus は再び使用可能になります。 通常、故障によってメッセージなどのデータが失われることはありません。 故障の例として、データセンターでの電源障害があります。

*災害*は、Service Bus [スケール ユニット](service-bus-architecture.md#service-bus-scale-units)またはデータ センターの永久または長期間損失として定義されています。 データセンターは、再び利用可能になる場合も、ならない場合もあります。また、数時間や数日間の停止になる場合もあります。 このような災害の例としては、火災、洪水、地震があります。 永続的な災害により、メッセージまたはその他のデータが失われる可能性があります。 ただし、ほとんどの場合、データ センターがバックアップされていればデータが失われることはなく、メッセージを復元できます。

Azure Service Bus の geo ディザスター リカバリー機能は、災害復旧ソリューションです。 この記事で説明する概念とワークフローは、一時的な故障ではなく、災害のシナリオに適用されます。  

## <a name="basic-concepts-and-terms"></a>基本的な概念と用語

ディザスター リカバリー機能は、メタデータの災害復旧を実装しており、一次および二次障害復旧の名前空間に依存しています。 geo ディザスター リカバリー機能は、[Premium 名前空間](service-bus-premium-messaging.md)でのみ使用可能です。 別名を使用して接続を確立するので、接続文字列に変更を加える必要はありません。

この記事では、次の用語を使用します。

-  *別名*: 主要な接続文字列です。

-  *プライマリ/セカンダリ名前空間*: 別名に対応する名前空間を示します。 プライマリは "アクティブ" であり、メッセージを受信します。セカンダリは "パッシブ" であり、メッセージを受信しません。 両者間のメタデータは同期しているため、どちらでもアプリケーション コードを変更せずにメッセージをシームレスに受信できます。

-  *メタデータ*: Azure Service Bus のオブジェクトの表現です。 現在は、メタデータのみがサポートされています。

-  *フェールオーバー*: セカンダリの名前空間をアクティブ化するプロセスです。 再び利用可能になったら、以前のプライマリ名前空間からメッセージをプルし、その後に名前空間を削除する必要があります。 別のフェールオーバーを作成するには、該当のペアリングに新しいセカンダリ名前空間を追加します。 フェールオーバー後、以前のプライマリ名前空間をもう一度使用する場合、最初に既存のエンティティを名前空間からすべて削除する必要があります。 削除前に、必ずすべてのメッセージを受信してください。

## <a name="failover-workflow"></a>フェールオーバーのワークフロー

次のセクションでは、最初のフェールオーバーを設定する全体プロセスと、その時点から前進する方法の概要を示します。

![1][]

まず、プライマリおよびセカンダリ名前空間を設定してから、ペアリングを作成します。 このペアリングによって、接続に使用できる別名が決定されます。 別名を使用するので、接続文字列を変更する必要はありません。 フェールオーバーのペアリングに追加できるのは、新しい名前空間だけです。 最後に、何らかのトリガー ロジックを追加する必要があります (たとえば、名前空間が使用できない場合を検出してフェールオーバーを開始するビジネス ロジックなど)。 Service Bus の[メッセージ参照](message-browsing.md)機能を使用して、名前空間の利用可否を確認できます。

監視とディザスター リカバリーの両方を設定すると、以降はフェールオーバー プロセスを確認できるようになります。 トリガーによってフェールオーバーを開始するか、またはフェールオーバーを手動で開始する場合、次の 2 つの手順が必要になります。

1. 別の故障が発生した場合は、もう一度フェールオーバーしたいと考えます。 そのために、セカンダリのパッシブな名前空間を設定して、ペアリングを更新します。 
2. 新しい名前空間が利用可能になったら、以前のプライマリ名前空間からメッセージをプルします。 その後、古いプライマリ名前空間を再利用または削除します。

![2][]

## <a name="set-up-disaster-recovery"></a>ディザスター リカバリーを設定する

このセクションでは、独自の Service Bus の geo ディザスター リカバリー コードをビルドする方法を説明します。 これを行うには、米国南部と米国中北部など、独立した場所にある 2 つの名前空間が必要です。 次の例では、Visual Studio 2017 を使用しています。

1.  Visual Studio で新しい**コンソール アプリ (.Net Framework)** プロジェクトを作成し、たとえば **SBGeoDR** のように名前を付けます。

2.  次の NuGet パッケージをインストールします。
    1.  Microsoft.IdentityModel.Clients.ActiveDirectory
    2.  Microsoft.Azure.Management.ServiceBus

3. 使用している Newtonsoft.Json NuGet パッケージのバージョンが 10.0.3 であることを確認します。

3.  次の `using` ステートメントをコードに追加します。

    ```csharp
    using System.Threading;
    using Microsoft.Azure.Management.ServiceBus;
    using Microsoft.Azure.Management.ServiceBus.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

4. `main()` メソッドを変更して、2 つのpremium 名前空間を追加します。

    ```csharp
    // 1. Create primary namespace (optional).

    var namespaceParams = new SBNamespace()
    {
        Location = "South Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    var namespace1 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, namespaceParams);

    // 2. Create secondary namespace (optional if you already have an empty namespace available).

    var namespaceParams2 = new SBNamespace()
    {
        Location = "North Central US",
        Sku = new SBSku()
        {
            Name = SkuName.Premium,
            Capacity = 1
        }

    };

    // If you re-run this program while namespaces are still paired this operation will fail with a bad request.
    // This is because we block all updates on secondary namespaces once it is paired.

    var namespace2 = client.Namespaces.CreateOrUpdate(resourceGroupName, geoDRSecondaryNS, namespaceParams2);
    ```

5. 2 つの名前空間のペアリングを有効にし、後でエンティティへの接続に使用する別名を取得します。

    ```csharp
    // 3. Pair the namespaces to enable DR.

    ArmDisasterRecovery drStatus = client.DisasterRecoveryConfigs.CreateOrUpdate(
        resourceGroupName,
        geoDRPrimaryNS,
        alias,
        new ArmDisasterRecovery { PartnerNamespace = geoDRSecondaryNS });

    // A similar loop can be used to check if other operations (Failover, BreakPairing, Delete) 
    // mentioned below have been successful.
    while (drStatus.ProvisioningState != ProvisioningStateDR.Succeeded)
    {
        Console.WriteLine("Waiting for DR to be set up. Current state: " +
        drStatus.ProvisioningState);
        drStatus = client.DisasterRecoveryConfigs.Get(
        resourceGroupName,
        geoDRPrimaryNS,
        alias);

        Thread.CurrentThread.Join(TimeSpan.FromSeconds(30));
    }
    ```

これで、ペアになった 2 つ名前空間を設定できました。 メタデータの同期を確認するためのエンティティを作成できます。 直後にフェールオーバーを実行する場合、メタデータが同期するまで一定の時間がかかります。 次の例のように、短いスリープ時間を追加できます。

```csharp
client.Topics.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", new SBTopic());
client.Subscriptions.CreateOrUpdate(resourceGroupName, geoDRPrimaryNS, "myTopic", "myTopic-Sub1", new SBSubscription());

// sleeping to allow metadata to sync across primary and secondary
Thread.Sleep(1000 * 60);
```

この時点で、ポータルまたは Azure Resource Manager 経由でエンティティを追加して、その同期方法を確認できます。 手動でフェールオーバーする計画ではないかぎり、プライマリ名前空間を監視して、この名前空間が使用不能になった場合にフェールオーバーを開始するアプリを作成する必要があります。 

## <a name="initiate-a-failover"></a>フェールオーバーの開始

次のコードは、フェールオーバーの開始方法を示しています。

```csharp
// Note that this failover operation is always run against the secondary namespace 
// (because primary might be down at time of failover).

client.DisasterRecoveryConfigs.FailOver(resourceGroupName, geoDRSecondaryNS, alias);
```

フェールオーバーをトリガーした後、新しいパッシブの名前空間を追加して、ペアリングを再確立します。 前のセクションに、新しいペアを作成するコードが示されています。 さらに、フェールオーバーが完了したら、古いプライマリ名前空間からメッセージを削除する必要があります。 キューからメッセージを受信する方法の例については、[キューの概要](service-bus-dotnet-get-started-with-queues.md)に関する記事をご覧ください。

## <a name="how-to-disable-geo-disaster-recovery"></a>geo ディザスター リカバリーを無効にする方法

次のコードは、名前空間のペアリングを無効にする方法を示しています。

```csharp
client.DisasterRecoveryConfigs.BreakPairing(resourceGroupName, geoDRPrimaryNS, alias);
```

次のコードは、作成した別名を削除します。

```csharp
// Delete the DR config (alias).
// Note that this operation must run against the namespace to which the alias is currently pointing.
// If you break the pairing and want to delete the namespaces afterwards, you must delete the alias first.

client.DisasterRecoveryConfigs.Delete(resourceGroupName, geoDRPrimaryNS, alias);
```

## <a name="steps-after-a-failover-failback"></a>フェールオーバー (フェールバック) 後に行う手順

フェールオーバー後は、次の 2 つの手順を実行します。

1.  新しいパッシブのセカンダリ名前空間を作成します。 前のセクションにコードが示されています。
2.  残りのメッセージをキューから削除します。

## <a name="alias-connection-string-and-test-code"></a>別名の接続文字列およびテスト コード

フェールオーバー プロセスをテストする場合は、別名を使用してプライマリ名前空間にメッセージをプッシュするサンプル アプリケーションを記述できます。 これを行うには、必ずアクティブな名前空間から別名の接続文字列を取得するようにします。 現在のプレビュー リリースでは、接続文字列を直接取得するための他のインターフェイスはありません。 次のコード例では、フェールオーバーの前後を接続しています。

```csharp
var accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRPrimaryNS, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;

if(aliasPrimaryConnectionString == null)
{
    accessKeys = client.Namespaces.ListKeys(resourceGroupName, geoDRSecondaryNS, "RootManageSharedAccessKey");
    aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
    aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
}
```

## <a name="next-steps"></a>次のステップ

- geo ディザスター リカバリーの [REST API リファレンス](/rest/api/servicebus/disasterrecoveryconfigs)を確認する
- geo ディザスター リカバリーの[ GitHub のサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)を実行する
- geo ディザスター リカバリーの[別名にメッセージを送信するサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)を確認する

Service Bus メッセージングの詳細については、次の記事をご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rest API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
