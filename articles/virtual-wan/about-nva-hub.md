---
title: Azure Virtual WAN:ハブのネットワーク仮想アプライアンスの概要
description: Virtual WAN ハブのネットワーク仮想アプライアンスについて説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/02/2021
ms.author: scottnap
ms.openlocfilehash: 68e5216257fd32237f3d67f05f0e17a0b8e16dbd
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411910"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>Azure Virtual WAN ハブのネットワーク仮想アプライアンスの概要

Azure Virtual WAN は、ネットワーク パートナーと協力して、仮想ハブ内の Azure VPN ゲートウェイに顧客構内設備 (CPE) を簡単に接続できる自動化を構築しました。 Azure は厳選したネットワーク パートナーと協力して、お客様がサード パーティのネットワーク仮想アプライアンス (NVA) を仮想ハブに直接デプロイできるように取り組んでいます。 これにより、ブランチ CPE を仮想ハブにある同じブランドの NVA に接続したいお客様は、独自のエンドツーエンド SD-WAN 機能を活用できるようになります。

Barracuda Networks と Cisco Systems は、Virtual WAN ハブに直接デプロイできる NVA を提供する最初のパートナーです。  それぞれの製品ドキュメントについては、「[Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan)」、「[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701)」、「[VMware SD-WAN](https://kb.vmware.com/s/article/82746)」を参照してください。 Azure ではさらに多くのパートナーとの連携を行っているため、今後他のオファリングもご提供できる予定です。

> [!NOTE]
> Virtual WAN ハブにデプロイできるのは、Virtual WAN ハブへのデプロイが可能な NVA オファーのみです。 Azure の任意の仮想ネットワークにデプロイすることはできません。

## <a name="how-does-it-work"></a><a name="how"></a>動作のしくみ

Azure Virtual WAN ハブに直接デプロイできる NVA は、仮想ハブで使用するために特別に設計されています。 NVA オファーはマネージド アプリケーションとして Azure Marketplace に公開され、お客様は Azure Marketplace から直接オファーをデプロイすることも、Azure portal を介して仮想ハブからオファーをデプロイすることもできます。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="プロセスの概要":::

各パートナーの NVA オファリングでは、そのデプロイ要件に基づいてエクスペリエンスと機能が若干異なります。 しかし、Virtual WAN ハブのすべての NVA のパートナー オファリングに共通するものもあります。

* Azure Marketplace を通じて提供されるマネージド アプリケーションのエクスペリエンス。
* NVA インフラストラクチャのユニットベースの容量と課金。
* 正常性メトリックは Azure Monitor を通じて表示。

### <a name="managed-application"></a><a name="managed"></a>マネージド アプリケーション

Virtual WAN ハブにデプロイできるすべての NVA サービスには、Azure Marketplace で入手できる **マネージド アプリケーション** があります。 パートナーは、マネージド アプリケーションで次のことを行うことができます。

* NVA のカスタム デプロイ エクスペリエンスを構築。
* 専用の Resource Manager テンプレートを指定して、Virtual WAN ハブで直接 NVA を作成。
* ソフトウェア ライセンスのコストを直接、または Azure Marketplace を通じて請求。
* カスタム プロパティとリソース メーターを公開。

NVA パートナーは、アプライアンスのデプロイ、構成のライセンス、および管理のニーズに応じて、異なるリソースを作成する場合があります。 お客様が Virtual WAN ハブで NVA を作成すると、すべてのマネージド アプリケーションと同様に、サブスクリプションに 2 つのリソース グループが作成されます。

* **カスタマー リソース グループ** - これには、マネージド アプリケーションのアプリケーション プレースホルダーが含まれます。 パートナーはこれを使用して、ここで選択したカスタマー プロパティを公開できます。
* **管理対象リソース グループ** - このリソース グループ内のリソースは、マネージド アプリケーションの公開元によって制御されるため、ユーザーが構成したり変更したりすることはできません。 このリソース グループには、**NetworkVirtualAppliances** リソースが含まれます。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="マネージド アプリケーション リソース グループ":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA インフラストラクチャ ユニット

Virtual WAN ハブで NVA を作成する場合は、デプロイに使用する NVA インフラストラクチャ ユニットの数を選択する必要があります。 **NVA インフラストラクチャ ユニット** は、Virtual WAN ハブの NVA の集計帯域幅容量の単位です。 **NVA インフラストラクチャ ユニット** は、容量やサイズに関する考え方の点において、VPN [スケール ユニット](pricing-concepts.md#scale-unit)に似ています。

* 1 NVA インフラストラクチャ ユニットは、この NVA に入ってくるすべてのブランチ サイト接続の総帯域幅 (500 Mbps) を、1 時間あたり $0.25 のコストで表します。
* Azure では、特定の NVA 仮想ハブ デプロイに対して 1-80 NVA インフラストラクチャ ユニットがサポートされています。
* 各パートナーは、サポートされているすべての NVA インフラストラクチャ ユニット構成のサブセットである、異なる NVA インフラストラクチャ ユニット バンドルを提供します。

VPN スケール ユニットと同様に、*1 NVA インフラストラクチャ ユニット = 500 Mbps* を選択した場合は、冗長性のための 2 つのインスタンスが作成され、それぞれの最大スループットが 500 Mbps であることを意味します。 たとえば、ブランチで 10 Mbps を実行する 5 つのブランチがある場合、ヘッド エンドで 50 Mbps の集計が必要になります。 NVA の合計容量を計画する場合は、ハブに対するブランチ数をサポートするために必要な容量を評価した後に行う必要があります。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>ネットワーク仮想アプライアンスの構成プロセス

パートナーは、デプロイ プロセスの一環として NVA を自動的に構成するエクスペリエンスを提供できるよう取り組んできました。 NVA が仮想ハブにプロビジョニングされた後は、NVA で必要になる可能性がある追加の構成は NVA パートナー ポータルまたは管理アプリケーションを使用して行う必要があります。 NVA への直接アクセスは使用できません。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>NVA を使用したサイト リソースと接続リソース

Azure VPN Gateway 構成とは異なり、ブランチ サイトを Virtual WAN ハブの NVA に接続する際に **サイト** リソース、**サイト間接続** リソース、または **ポイント対サイト接続** リソースを作成する必要はありません。 これはすべて NVA パートナーを通じて管理されます。

Virtual WAN ハブを Azure 仮想ネットワークに接続するためには、今後も Hub-to-VNet 接続を作成する必要があります。

## <a name="supported-regions"></a><a name="regions"></a>サポートされているリージョン

仮想ハブの NVA は、以下のリージョンで利用できます。

|地理的リージョン | Azure Azure リージョン|
|---|---|
| 北米| カナダ中部、カナダ東部、米国中部、米国東部、米国東部 2、米国中南部、米国中北部、米国中西部、米国西部、米国西部 2 |
| 南アメリカ | ブラジル南部、ブラジル南東部 |
| ヨーロッパ | フランス中部、フランス南部、ドイツ北部、ドイツ中西部、北ヨーロッパ、ノルウェー東部、ノルウェー西部、スイス北部、スイス西部、英国南部、英国西部、西ヨーロッパ|
|  中東 | アラブ首長国連邦北部 |
| アジア |  東アジア、東日本、西日本、韓国中部、韓国南部、東南アジア | 
| オーストラリア | オーストラリア南東部、オーストラリア東部、オーストラリア中部、オーストラリア中部 2|
| アフリカ | 南アフリカ北部 |
| インド | インド南部、インド西部、インド中部 | 

## <a name="nva-faq"></a>NVA の FAQ

[!INCLUDE [NVA FAQ](../../includes/virtual-wan-nva-hub-faq.md)]

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
