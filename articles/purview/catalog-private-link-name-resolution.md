---
title: プライベート エンドポイントの DNS 名前解決を構成する
description: この記事では、Purview アカウントにプライベート エンド ポイントを使用する方法の概要について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/18/2021
ms.openlocfilehash: e97ed074cd9c0fee7a2fc3b3c7795d205292da28
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254156"
---
# <a name="configure-and-verify-dns-name-resolution-for-azure-purview-private-endpoints"></a>Azure Purview プライベート エンドポイントの DNS 名前解決の構成と検証

> [!IMPORTANT]
> **2021 年 9 月 27 日 15:30 UTC より前に** Purview アカウントの "_ポータル_" プライベート エンドポイントを作成した場合は、「[ポータル プライベート エンドポイント用に DNS を再構成する](./catalog-private-link.md#reconfigure-dns-for-portal-private-endpoints)」で詳しく説明されているように、必要なアクションを実行する必要があります。 **これらの操作は、2021 年 11 月 12 日より前に完了する必要があります。そうしない場合、既存のポータルのプライベート エンドポイントの機能が停止します**。

## <a name="conceptual-overview"></a>概念の概要
Azure Purview アカウントのプライベート エンドポイントを設定する場合、正確な名前解決は重要な要件です。 

デプロイするシナリオによっては、データ ソースや管理マシンから Azure Purview アカウントやセルフホステッド統合ランタイムまで、プライベート エンドポイントの IP アドレスを完全修飾ドメイン名 (FQDN) に解決するために DNS 設定で内部名前解決を有効にする必要があります。

次の例は、仮想ネットワークの外部からの (Azure プライベート エンドポイントが構成されていない場合の) Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="CorpNet の外部からの Azure Purview の名前解決を示すスクリーンショット。":::

次の例は、仮想ネットワーク内からの Azure Purview の DNS 名前解決を示しています。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="CorpNet 内からの Purview の名前解決を示すスクリーンショット。":::

## <a name="deployment-options"></a>配置オプション 

Azure Purview アカウントにプライベート エンドポイントを使用する場合、次のいずれかのオプションを使用して内部の名前解決を設定します。

- プライベート エンドポイントのデプロイの一環として、Azure 環境に[新しい Azure プライベート DNS ゾーンをデプロイ](#option-1---deploy-new-azure-private-dns-zones)します。 (既定のオプション)
- [既存の Azure プライベート DNS ゾーン](#option-2---use-existing-azure-private-dns-zones)を使用します。 このオプションを使用するのは、異なるサブスクリプションから、または同じサブスクリプション内でも、ハブアンドスポーク モデルでプライベート エンドポイントを使用する場合です。 
- DNS フォワーダーを使用せず、代わりにオンプレミスの DNS サーバーで A レコードを直接管理する場合は、[独自の DNS サーバーを使用します](#option-3---use-your-own-dns-servers)。

## <a name="option-1---deploy-new-azure-private-dns-zones"></a>オプション 1 - 新しい Azure プライベート DNS ゾーンをデプロイする  

### <a name="deploy-new-azure-private-dns-zones"></a>新しい Azure プライベート DNS ゾーンをデプロイする
内部の名前解決を有効にするには、Azure Purview アカウントがデプロイされている Azure サブスクリプション内に必要な Azure DNS ゾーンをデプロイします。 

   :::image type="content" source="media/catalog-private-link/purview-pe-dns-zones.png" alt-text="DNS ゾーンを示すスクリーンショット。":::

インジェスト、ポータル、およびアカウントのプライベート エンドポイントを作成すると、Azure Purview の DNS CNAME リソース レコードは、`privatelink` というプレフィックスを持ついくつかのサブドメイン内で自動的に別名に更新されます。

- 既定では、Purview アカウントの "_アカウント_" プライベート エンドポイントのデプロイ中に、プライベート エンドポイントの DNS A リソース レコードを含む `privatelink.purview.azure.com` として Azure Purview の `privatelink` サブドメインに対応する [プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。

- Purview アカウントの "_ポータル_" プライベート エンドポイントのデプロイ中に、_Web_ の DNS A リソース レコードを含む `privatelink.purviewstudio.azure.com` として Azure Purview の `privatelink` サブドメインに対応する新しいプライベート DNS ゾーンも作成されます。

- インジェストのプライベート エンドポイントを有効にする場合、管理対象リソースに追加の DNS ゾーンが必要になります。 

次の表は、デプロイ時に _プライベート DNS 統合_ を有効にした場合、Azure Purview アカウントのプライベート エンドポイントの構成の一部としてデプロイされる Azure プライベート DNS ゾーンと DNS A レコードの例を示しています。 

プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン (新規)  |A レコード (例) |
|---------|---------|---------|---------|
|Account     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|ポータル     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

### <a name="validate-virtual-network-links-on-azure-private-dns-zones"></a>Azure プライベート DNS ゾーン上の仮想ネットワーク リンクを検証する

プライベート エンドポイントのデプロイが完了したら、プライベート エンドポイントがデプロイされた Azure 仮想ネットワークに対応するすべての Azure プライベート DNS ゾーンに、[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)があることを確認します。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="DNS ゾーン上の仮想ネットワーク リンクを示すスクリーンショット。":::

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

### <a name="verify-internal-name-resolution"></a>内部名前解決を検証する

プライベート エンドポイントがある仮想ネットワークの外部から Azure Purview エンドポイントの URL を解決すると、Azure Purview のパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている仮想ネットワークから解決されると、Azure Purview エンドポイントの URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、Azure Purview アカウント名が "Contoso-Purview" の場合、プライベート エンドポイントをホストする仮想ネットワークの外部から解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Contoso-Purview の DNS リソース レコードは、プライベート エンドポイントをホストしている仮想ネットワーク内で解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | A | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-2---use-existing-azure-private-dns-zones"></a>オプション 2 - 既存の Azure プライベート DNS ゾーンを使用する

### <a name="use-existing-azure-private-dns-zones"></a>既存の Azure プライベート DNS ゾーンを使用する

Azure Purview プライベート エンドポイントのデプロイ時に、既存の Azure プライベート DNS ゾーンを使用して _[プライベート DNS 統合]_ を選択することができます。 これは、プライベート エンドポイントが Azure の他のサービスに使用されている組織によくあるケースです。 このケースでは、プライベート エンドポイントのデプロイ時に、新しい DNS ゾーンを作成するのではなく、既存の DNS ゾーンを選択してください。 

このシナリオは、組織がすべての Azure プライベート DNS ゾーンに対して中央またはハブ サブスクリプションを使用している場合にも適用されます。

次の一覧は、Purview プライベート エンドポイントに必要な Azure DNS ゾーンと A レコードを示しています。

> [!NOTE]
> `Contoso-Purview`、`scaneastusabcd1234`、`atlas-12345678-1234-1234-abcd-123456789abc` のすべての名前を、環境内の対応する Azure リソース名に更新します。 たとえば、`scaneastusabcd1234` ではなく、Azure Purview マネージド ストレージ アカウントの名前を使用します。

プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン (既存)  |A レコード (例) |
|---------|---------|---------|---------|
|Account     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
|ポータル     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
|取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
|取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
|取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Azure Purview の名前解決を示す図。"lightbox="media/catalog-private-link/purview-name-resolution-diagram.png":::

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」の「[カスタム DNS サーバーのない仮想ネットワークのワークロード](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)」と「[DNS フォワーダーを使用しているオンプレミスのワークロード](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)」を参照してください。

### <a name="verify-virtual-network-links-on-azure-private-dns-zones"></a>Azure プライベート DNS ゾーン内の仮想ネットワーク リンクを検証する

プライベート エンドポイントのデプロイが完了したら、プライベート エンドポイントがデプロイされた Azure 仮想ネットワークに対応するすべての Azure プライベート DNS ゾーンに、[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)があることを確認します。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-link.png" alt-text="DNS ゾーン上の仮想ネットワーク リンクを示すスクリーンショット。":::

詳細については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」を参照してください。

### <a name="configure-dns-forwarders-if-custom-dns-is-used"></a>カスタム DNS が使用されている場合に DNS フォワーダーを構成する

さらに、セルフホステッド統合ランタイム VM または管理 PC が配置されている Azure 仮想ネットワーク上の DNS 構成を検証する必要があります。 

   :::image type="content" source="media/catalog-private-link/purview-pe-custom-dns.png" alt-text="Azure 仮想ネットワークのカスタム DNS を示す図":::

- "_既定値_" に構成されている場合、この手順ではこれ以上の操作は必要ありません。

-  カスタム DNS サーバーが使用されている場合は、次のゾーンの DNS サーバー内に対応する DNS フォワーダーを追加する必要があります。
  
   -  Purview.azure.com
   -  Blob.core.windows.net
   -  Queue.core.windows.net
   -  Servicebus.windows.net

### <a name="verify-internal-name-resolution"></a>内部名前解決を検証する

プライベート エンドポイントがある仮想ネットワークの外部から Azure Purview エンドポイントの URL を解決すると、Azure Purview のパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている仮想ネットワークから解決されると、Azure Purview エンドポイントの URL はプライベート エンドポイントの IP アドレスに解決されます。

たとえば、Azure Purview アカウント名が "Contoso-Purview" の場合、プライベート エンドポイントをホストする仮想ネットワークの外部から解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview Studio public endpoint\> |

Contoso-Purview の DNS リソース レコードは、プライベート エンドポイントをホストしている仮想ネットワーク内で解決されると、次のようになります。

| 名前 | Type | 値 |
| ---------- | -------- | --------------- |
| `Contoso-Purview.purview.azure.com` | CNAME | `Contoso-Purview.privatelink.purview.azure.com` |
| `Contoso-Purview.privatelink.purview.azure.com` | A | \<Purview account private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview portal private endpoint IP address\> |

## <a name="option-3---use-your-own-dns-servers"></a>オプション 3 - 独自の DNS サーバーを使用する

DNS フォワーダーを使用しないで、代わりにオンプレミスの DNS サーバーで A レコードを直接管理し、プライベート IP アドレスを介してエンドポイントを解決する場合は、DNS サーバーに次の A レコードを作成することが必要になる場合があります。

> [!NOTE]
> `Contoso-Purview`、`scaneastusabcd1234`、`atlas-12345678-1234-1234-abcd-123456789abc` のすべての名前を、環境内の対応する Azure リソース名に更新します。 たとえば、`scaneastusabcd1234` ではなく、Azure Purview マネージド ストレージ アカウントの名前を使用します。

| Name | Type | 値 |
| ---------- | -------- | --------------- |
| `web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.blob.core.windows.net` | A | \<blob-ingestion private endpoint IP address of Azure Purview> |
| `scaneastusabcd1234.queue.core.windows.net` | A | \<queue-ingestion private endpoint IP address of Azure Purview> |
| `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`| A | \<namespace-ingestion private endpoint IP address of Azure Purview> |
| `Contoso-Purview.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `Contoso-Purview.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `gateway.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `Contoso-Purview.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |


## <a name="verify-and-dns-test-name-resolution-and-connectivity"></a>名前解決と接続の検証と DNS テスト 

1. Azure プライベート DNS ゾーンを使用している場合、次の DNS ゾーンと対応する A レコードが Azure サブスクリプションで作成されていることを確認します。

   |プライベート エンドポイント  |プライベート エンドポイントの関連付け対象  |DNS ゾーン  |A レコード (例) |
   |---------|---------|---------|---------|
   |Account     |Azure Purview         |`privatelink.purview.azure.com`         |Contoso-Purview         |
   |ポータル     |Azure Purview          |`privatelink.purviewstudio.azure.com`        |Web         |
   |取り込み     |Purview マネージド ストレージ アカウント - BLOB          |`privatelink.blob.core.windows.net`          |scaneastusabcd1234         |
   |取り込み   |Purview マネージド ストレージ アカウント - キュー         |`privatelink.queue.core.windows.net`         |scaneastusabcd1234         |
   |取り込み     |Purview マネージド ストレージ アカウント - イベント ハブ         |`privatelink.servicebus.windows.net`         |atlas-12345678-1234-1234-abcd-123456789abc         |

2. 内部の名前解決を可能にするために、Azure プライベート DNS ゾーンで Azure 仮想ネットワークの[仮想ネットワーク リンク](../dns/private-dns-virtual-network-links.md)を作成します。
   
3. 管理 PC とセルフホステッド統合ランタイム VM から、Nslookup.exe や PowerShell などのツールを使用して、名前解決と Azure Purview アカウントへのネットワーク接続をテストします。

名前解決をテストするには、プライベート IP アドレス介して次の FQDN を解決する必要があります (Contoso-Purview、scaneastusabcd1234、atlas-12345678-1234-1234-abcd-123456789abc ではなく、Purview アカウント名と管理対象リソース名に関連付けられたホスト名を使用します)

- `Contoso-Purview.purview.azure.com`
- `web.purview.azure.com`
- `scaneastusabcd1234.blob.core.windows.net`
- `scaneastusabcd1234.queue.core.windows.net`
- `atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net`

ネットワーク接続をテストするには、セルフホステッド統合ランタイム VM から PowerShell コンソールを起動し、`Test-NetConnection` を使用して接続をテストします。 各エンドポイントをプライベート エンドポイントで解決し、TcpTestSucceeded を True として取得する必要があります (Contoso-Purview、scaneastusabcd1234、atlas-12345678-1234-1234-abcd-123456789abc ではなく、Purview アカウント名と管理対象リソース名に関連付けられたホスト名を使用します)

- `Test-NetConnection -ComputerName Contoso-Purview.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName web.purview.azure.com -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.blob.core.windows.net -port 443`
- `Test-NetConnection -ComputerName scaneastusabcd1234.queue.core.windows.net -port 443`
- `Test-NetConnection -ComputerName atlas-12345678-1234-1234-abcd-123456789abc.servicebus.windows.net -port 443` 

## <a name="next-steps"></a>次の手順

- [Azure Purview アカウントのプライベート エンドポイント構成のトラブルシューティング](catalog-private-link-troubleshoot.md)
- [Azure Purview でデータ ソースを管理する](./manage-data-sources.md)
