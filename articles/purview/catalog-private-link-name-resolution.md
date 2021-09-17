---
title: プライベート エンドポイントの DNS 名前解決を構成する
description: この記事では、Purview アカウントにプライベート エンド ポイントを使用する方法の概要について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: b6d0b4cb58562540cd3b2024631bb9cec2d320c9
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607049"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントの DNS 名前解決の構成と検証

## <a name="conceptual-overview"></a>概念の概要
Azure Purview アカウントのプライベート エンドポイントを設定する場合、正確な名前解決は重要な要件です。 

デプロイするシナリオによっては、データ ソースや管理マシンから Azure Purview アカウントやセルフホステッド統合ランタイムまで、プライベート エンドポイントの IP アドレスを完全修飾ドメイン名 (FQDN) に解決するために DNS 設定で内部名前解決を有効にする必要があります。

次の例は、仮想ネットワークの外部からの (Azure プライベート エンドポイントが構成されていない場合の) Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="CorpNet の外部からの Azure Purview の名前解決を示すスクリーンショット。":::

次の例は、仮想ネットワーク内からの Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="CorpNet 内からの Purview の名前解決を示すスクリーンショット。":::

## <a name="deployment-options"></a>配置オプション 

Azure Purview アカウントにプライベート エンドポイントを使用する場合、次のいずれかのオプションを使用して内部の名前解決を送信します。

-  プライベート エンドポイントのデプロイの一環として、Azure 環境に[新しい Azure プライベート DNS ゾーンをデプロイ](#option-1---deploy-new-azure-private-dns-zones)します。 (既定のオプション)
- [既存の Azure プライベート DNS ゾーン](#option-2---use-existing-azure-private-dns-zones)を使用します。 このオプションを使用するのは、異なるサブスクリプションから、または同じサブスクリプション内でも、ハブアンドスポーク モデルでプライベート エンドポイントを使用する場合です。 
- DNS フォワーダーを使用せず、代わりにオンプレミスの DNS サーバーで A レコードを直接管理する場合は、[独自の DNS サーバーを使用します](#option-3---use-your-own-dns-servers)。

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>オプション 1 - 新しい Azure プライベート DNS ゾーンをデプロイする  

内部の名前解決を有効にするには、Azure Purview アカウントがデプロイされている Azure サブスクリプション内に必要な Azure DNS ゾーンをデプロイします。 

ポータルとアカウントのプライベート エンドポイントを作成すると、Azure Purview の DNS CNAME リソース レコードは、`privatelink` というプレフィックスのサブドメインの別名に自動的に更新されます。 既定では、プライベート エンドポイントの DNS A リソース レコードを含む privatelink.purview.azure.com として Azure Purview の `privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。 インジェストのプライベート エンドポイントを有効にする場合、管理対象リソースに追加の DNS ゾーンが必要になります。 

次の表は、デプロイ時に _プライベート DNS 統合_ を有効にした場合、Azure Purview アカウントのプライベート エンドポイントの構成の一部としてデプロイされる Azure プライベート DNS ゾーンと DNS A レコードの例を示しています。 

プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン (新規)  |A レコード (例) |
|---------|---------|---------|---------|
|Account     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|ポータル     |Azure Purview アカウント          |`privatelink.purview.azure.com`        |Web         |
|取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

プライベート エンドポイントがある仮想ネットワークの外部から Azure Purview エンドポイントの URL を解決すると、Azure Purview のパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている仮想ネットワークから解決されると、Azure Purview エンドポイントの URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、Azure Purview アカウント名が "PurviewA" の場合、プライベート エンドポイントをホストする仮想ネットワークの外部から解決されると、次のようになります。

| Name | Type | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

PurviewA の DNS リソース レコードは、プライベート エンドポイントをホストしている仮想ネットワーク内で解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>オプション 2 - 既存の Azure プライベート DNS ゾーンを使用する
Azure Purview プライベート エンドポイントのデプロイ時に、既存の Azure プライベート DNS ゾーンを使用して _[プライベート DNS 統合]_ を選択することができます。 これは、プライベート エンドポイントが Azure の他のサービスに使用されている組織によくあるケースです。 このケースでは、プライベート エンドポイントのデプロイ時に、新しい DNS ゾーンを作成するのではなく、既存の DNS ゾーンを選択してください。 

これは、組織がすべての Azure プライベート DNS ゾーンに対して中央のサブスクリプションまたはハブ サブスクリプションを使用している場合にも適用されます。

次の一覧は、Purview プライベート エンドポイントに必要な Azure DNS ゾーンと A レコードを示しています。

> [!NOTE]
> `PurviewA`、`scaneastusabcd1234`、`atlas-12345678-1234-1234-abcd-123456789abc` のすべての名前を、環境内の対応する Azure リソース名に更新します。 たとえば、`scaneastusabcd1234` ではなく、Azure Purview マネージド ストレージ アカウントの名前を使用します。

プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン (既存)  |A レコード (例) |
|---------|---------|---------|---------|
|Account     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
|ポータル     |Azure Purview アカウント          |`privatelink.purview.azure.com`        |Web         |
|取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」の「[カスタム DNS サーバーのない仮想ネットワークのワークロード](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)」と「[DNS フォワーダーを使用しているオンプレミスのワークロード](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)」を参照してください。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Azure Purview の名前解決を示す図。"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

ネットワークでカスタム DNS サーバーを使用している場合は、クライアントで Azure Purview エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 Private Link サブドメインを仮想ネットワークのプライベート DNS ゾーンに委任するように、DNS サーバーを構成します。 または、プライベート エンドポイントの IP アドレスを使用して `PurviewA.privatelink.purview.azure.com` の A レコードを構成します。
プライベート エンドポイントのデプロイが完了したら、プライベート エンドポイントがデプロイされた Azure 仮想ネットワークに対応する Azure プライベート DNS ゾーンに、名前解決のための[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)があることを確認します。 

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

## <a name="option-3---use-your-own-dns-servers"></a>オプション 3 - 独自の DNS サーバーを使用する

DNS フォワーダーを使用しないで、代わりにオンプレミスの DNS サーバーで A レコードを直接管理し、プライベート IP アドレスを介してエンドポイントを解決する場合は、DNS サーバーに次の A レコードを作成することが必要になる場合があります。

> [!NOTE]
> `PurviewA`、`scaneastusabcd1234`、`atlas-12345678-1234-1234-abcd-123456789abc` のすべての名前を、環境内の対応する Azure リソース名に更新します。 たとえば、`scaneastusabcd1234` ではなく、Azure Purview マネージド ストレージ アカウントの名前を使用します。

| Name | Type | 値 |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>名前解決と接続の検証と DNS テスト 

1. Azure プライベート DNS ゾーンを使用している場合、次の DNS ゾーンと対応する A レコードが Azure サブスクリプションで作成されていることを確認します。

   |プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン  |A レコード (例) |
   |---------|---------|---------|---------|
   |Account     |Azure Purview         |`privatelink.purview.azure.com`         |PurviewA         |
   |ポータル     |Azure Purview アカウント          |`privatelink.purview.azure.com`        |Web         |
   |取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. 内部の名前解決を可能にするために、Azure プライベート DNS ゾーンで Azure 仮想ネットワークの[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)を作成します。
   
3. 管理 PC とセルフホステッド統合ランタイム VM から、Nslookup.exe や PowerShell などのツールを使用して、名前解決と Azure Purview アカウントへのネットワーク接続をテストします。

名前解決をテストするには、プライベート IP アドレス介して次の FQDN を解決する必要があります (PurviewA、scaneastusabcd1234、atlas-12345678-1234-1234-abcd-123456789abc ではなく、Purview アカウント名と管理対象リソース名に関連付けられたホスト名を使用します)。

- `PurviewA.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

ネットワーク接続をテストするには、セルフホステッド統合ランタイム VM から PowerShell コンソールを起動し、`Test-NetConnection` を使用して接続をテストします。 各エンドポイントをプライベート エンドポイントで解決し、TcpTestSucceeded を True として取得する必要があります (PurviewA、scaneastusabcd1234、atlas-12345678-1234-1234-abcd-123456789abc ではなく、Purview アカウント名と管理対象リソース名に関連付けられたホスト名を使用します)。

- `Test-NetConnection -ComputerName PurviewA.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>次の手順

- [Azure Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](catalog-private-link-troubleshoot.md)
- [Azure Purview でデータ ソースを管理する](./manage-data-sources.md)
