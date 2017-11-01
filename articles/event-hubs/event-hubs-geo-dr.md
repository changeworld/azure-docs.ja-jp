---
title: "Azure Event Hubs geo ディザスター リカバリー | Microsoft Docs"
description: "Azure Event Hubs でリージョンを使用してフェールオーバーとディザスター リカバリーを実行する方法"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Azure Event Hubs geo ディザスター リカバリー (プレビュー)

地域のデータ センターにダウンタイムが発生した場合に、別の地域またはデータ センターでデータ処理が続行されることが重要です。 そのため、*geo ディザスター リカバリー*と *geo レプリケーション*は、どの企業にとっても重要な機能です。 Azure Event Hubs では、geo ディザスター リカバリーと geo レプリケーションの両方が名前空間レベルでサポートされています。 

Azure Event Hubs の geo ディザスター リカバリー機能はディザスター リカバリー ソリューションです。 この記事で説明する概念とワークフローは、一時的な故障ではなく、災害のシナリオに適用されます。

Microsoft Azure でのディザスター リカバリーの詳細については、[こちらの記事](/azure/architecture/resiliency/disaster-recovery-azure-applications)をご覧ください。 

## <a name="terminology"></a>用語集

**ペアリング**: プライマリ名前空間は*アクティブ*と見なされ、メッセージを受け取ります。 フェールオーバー名前空間は*パッシブ*で、メッセージを受け取りません。 両者間のメタデータは同期しているため、どちらでもアプリケーション コードを変更せずにメッセージをシームレスに受信できます。 アクティブなリージョンとパッシブなリージョン間でディザスター リカバリー構成を確立することを、リージョンの*ペアリング*といいます。

**エイリアス**: 設定したディザスター リカバリー構成の名前です。 エイリアスは、1 つの不変の完全修飾ドメイン名 (FQDN) の接続文字列を示します。 アプリケーションでは、このエイリアスの接続文字列を使用して名前空間に接続します。

**メタデータ**: 名前空間に関連付けられたイベント ハブ名、コンシューマー グループ、パーティション、スループット ユニット、エンティティ、およびプロパティを参照します。

## <a name="enable-geo-disaster-recovery"></a>geo ディザスター リカバリーを有効にする

Event Hubs geo ディザスター リカバリーは、次の 3 つの手順で有効にできます。 

1. geo ペアリングを作成して、エイリアス接続文字列を作成し、ライブ メタデータ レプリケーションを提供する。 
2. 既存のクライアント接続文字列を、手順 1 で作成したエイリアスに変更する。
3. フェールオーバーを開始する: geo ペアリングが解消され、エイリアスはセカンダリ名前空間を新しいプライマリ名前空間として示します。

次の図はこのワークフローを示しています。

![geo ペアリングのフロー][1] 

### <a name="step-1-create-a-geo-pairing"></a>手順 1: geo ペアリングの作成

2 つのリージョン間でペアリングを作成するには、プライマリ名前空間とセカンダリ名前空間が必要です。 その後、エイリアスを作成して geo ペアを形成します。 名前空間とエイリアスのペアリングが完了すると、両方の名前空間でメタデータが定期的にレプリケートされます。 

この処理方法を、次のコードで示します。

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>手順 2: 既存のクライアント接続文字列の変更

geo ペアリングが完了したら、プライマリ名前空間を指す接続文字列を、エイリアスの接続文字列を指すように変更する必要があります。 次の例で示すように、接続文字列を取得します。

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>手順 3: フェールオーバーの開始

災害が発生した場合、またはセカンダリ名前空間へのフェールオーバーを開始することにした場合は、メタデータとデータはセカンダリ名前空間へのフローを開始します。 アプリケーションはエイリアスの接続文字列を使用するため、これ以上の対応は必要ありません。セカンダリ名前空間でイベント ハブに対する読み取りと書き込みが自動的に開始されます。 

次のコードは、フェールオーバーの開始方法を示しています。

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

フェールオーバーが完了した後でプライマリ名前空間にあるデータが必要になった場合、データを抽出するために、プライマリ名前空間内のイベント ハブへの明示的な接続文字列を使用する必要があります。

### <a name="other-operations-optional"></a>その他の操作 (省略可能)

次のコードに示すように、geo ペアリングを解消したり、エイリアスを削除したりすることもできます。 エイリアス接続文字列を削除するには、次のように最初に geo ペアリングを解消する必要があります。

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>パブリック プレビューに関する考慮事項

このリリースでは次の考慮事項にご注意ください。

1. geo ディザスター リカバリー機能は、米国中北部リージョンと米国中南部リージョンでのみ利用可能です。 
2. この機能は、新しく作成された名前空間でのみサポートされます。
3. プレビュー リリースでは、メタデータ レプリケーションのみが有効になっています。 実際のデータはレプリケートされません。
4. プレビュー リリースでは、この機能を有効にするための料金はかかりません。 ただし、予約されたスループット ユニットに対しては、プライマリとセカンダリ両方の名前空間で料金が発生します。

## <a name="next-steps"></a>次のステップ

* [GitHub のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient)で、geo ペアリングを作成して災害復旧シナリオのフェールオーバーを開始する簡単なワークフローの手順について説明します。
* [REST API リファレンス](/rest/api/eventhub/disasterrecoveryconfigs)で、geo ディザスター リカバリーの構成を実行するための API について説明します。

Event Hubs の詳細については、次のリンクを参照してください。

* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

