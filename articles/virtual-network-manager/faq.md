---
title: Azure Virtual Network Manager についてよく寄せられる質問
description: Azure Virtual Network Manager についてよく寄せられる質問への回答を見つけましょう。
services: virtual-network-manager
author: duongau
ms.service: virtual-network-manager
ms.topic: article
ms.date: 11/02/2021
ms.author: duau
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 921c829cefae8ab4ea96066d70c5d110fc9188f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451941"
---
# <a name="azure-virtual-network-manager-faq"></a>Azure Virtual Network Manager に関する FAQ

## <a name="general"></a>全般

### <a name="what-regions-are-available-in-public-preview"></a>パブリック プレビューで利用できるリージョンは何ですか?

* 米国中北部

* 米国西部

* 米国西部 2

* 米国東部

* 米国東部 2

* 北ヨーロッパ

* 西ヨーロッパ

* フランス中部

### <a name="what-are-common-use-cases-for-using-azure-virtual-network-manager"></a>Azure Virtual Network Manager を使用する場合の一般的なユース ケースにはどのようなものがありますか?

* IT セキュリティ マネージャーは、環境とその機能の要件を満たすために、さまざまなネットワーク グループを作成できます。 たとえば、実稼働環境とテスト ネットワーク環境、開発チーム、財務部門などのネットワーク グループを作成して、大規模な接続とセキュリティ規則を管理できます。 

* 接続構成を適用して、組織のサブスクリプション全体で多数の仮想ネットワークのメッシュまたはハブおよびスポーク ネットワーク トポロジを作成できます。 

* リスクの高いトラフィックを拒否できる: 企業の管理者は、トラフィックを通常許可する NSG ルールをオーバーライドする特定のプロトコルまたはソースをブロックできます。   

* 常にトラフィックを許可する: トラフィックを拒否するように構成された NSG ルールがある場合でも、特定のセキュリティ スキャナーが、すべてのリソースへの受信接続を常に保持できるようにする必要があります。   

## <a name="technical"></a>技術

### <a name="can-a-virtual-network-belong-to-multiple-azure-virtual-network-managers"></a>仮想ネットワークが複数の Azure Virtual Network Manager に属することはできますか?

はい。仮想ネットワークは複数の Azure Virtual Network Manager に属することができます。

### <a name="what-is-a-global-mesh-network-topology"></a>グローバル メッシュ ネットワーク トポロジとは何ですか?

グローバル メッシュを使用すると、異なるリージョン間の仮想ネットワークが相互に通信できます。 その効果は、グローバル仮想ネットワーク ピアリングの動作に似ています。

### <a name="is-there-a-limit-to-how-many-network-groups-can-be-created"></a>作成できるネットワーク グループの数に制限はありますか?

作成できるネットワーク グループの数に制限はありません。

### <a name="how-do-i-remove-the-deployment-of-all-applied-configurations"></a>適用されているすべての構成のデプロイを削除するにはどうすればよいですか?

構成が適用されているすべてのリージョンに **None** 構成をデプロイする必要があります。

### <a name="can-i-add-virtual-networks-from-another-subscription-not-managed-by-myself"></a>自分で管理していない別のサブスクリプションから仮想ネットワークを追加できますか?

はい。それらの仮想ネットワークにアクセスするための適切なアクセス許可が必要です。

### <a name="what-is-dynamic-group-membership"></a>動的グループ メンバーシップとは何ですか?

詳細については、[動的メンバーシップ](concept-network-groups.md#dynamic-membership)に関するページを参照してください。

### <a name="how-does-the-deployment-of-configuration-differ-for-dynamic-membership-and-static-membership"></a>動的メンバーシップと静的メンバーシップでは、構成のデプロイがどのように異なりますか?

詳細については、[メンバーシップの種類に対するデプロイ](concept-deployments.md#deployment)に関するページを参照してください。

### <a name="how-do-i-delete-an-azure-virtual-network-manager-component"></a>Azure Virtual Network Manager コンポーネントを削除するにはどうすればよいですか?

詳細については、[コンポーネント チェックリストの削除](concept-remove-components-checklist.md)に関するページを参照してください。

### <a name="how-can-i-see-what-configurations-are-applied-to-help-me-troubleshoot"></a>トラブルシューティングのために適用済みの構成を確認するにはどうすればよいですか?

仮想ネットワークの **[Network Manager]** で、Azure Virtual Network Manager の設定を表示できます。 適用されている接続とセキュリティ管理者の両方の構成を確認できます。 詳細については、[適用済みの構成の表示](how-to-view-applied-configurations.md)に関するページを参照してください。

### <a name="can-a-virtual-network-managed-by-azure-virtual-network-manager-be-peered-to-a-non-managed-virtual-network"></a>Azure Virtual Network Manager によって管理される仮想ネットワークは、管理対象外の仮想ネットワークとピアリングできますか?

はい。既に作成されている既存のピアリングをオーバーライドまたは削除することができます。

### <a name="how-can-i-explicitly-allow-sqlmi-traffic-before-having-deny-rules"></a>SQLMI トラフィックを明示的に許可して、拒否ルールが適用されないようにするにはどうすればよいですか?

Azure SQL Managed Instance には、いくつかのネットワーク要件があります。 セキュリティ管理者のルールによってネットワーク要件がブロックされる可能性がある場合は、以下のサンプル ルールをご使用ください。SQL Managed Instance のトラフィックをブロックする可能性のある拒否ルールよりも高い優先度で SQLMI トラフィックを許可できます。

#### <a name="inbound-rules"></a>受信の規則

| Port | Protocol | source | 宛先 | アクション |
| ---- | -------- | ------ | ----------- | ------ |
| 9000、9003、1438、1440、1452 | TCP | SqlManagement | **VirtualNetwork** | Allow |
| 9000、9003 | TCP | CorpNetSaw | **VirtualNetwork** | Allow |
| 9000、9003 | TCP | CorpnetPublic | **VirtualNetwork** | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Allow |
| Any | Any | **AzureLoadBalancer** | **VirtualNetwork** | Allow |

#### <a name="outbound-rules"></a>アウトバウンド規則

| Port | Protocol | source | 宛先 | アクション |
| ---- | -------- | ------ | ----------- | ------ |
| 443、12000 | TCP  | **VirtualNetwork** | AzureCloud | Allow |
| Any | Any | **VirtualNetwork** | **VirtualNetwork** | Allow |

## <a name="limits"></a>制限

### <a name="what-are-the-service-limitation-of-azure-virtual-network-manager"></a>Azure Virtual Network Manager のサービス制限とは何ですか?

* ハブおよびスポーク トポロジのハブは、最大 250 のスポークとピアリングできます。 

* メッシュ トポロジには、最大 250 の仮想ネットワークを含めることができます。

* 仮想ネットワーク内のサブネットは、メッシュ構成内に同じアドレス空間を持っている場合、相互に通信することができません。 

* すべての管理者規則を組み合わせた IP プレフィックスの最大数は 1000 です。 

* Azure Virtual Network Manager の 1 つのレベルの管理者規則の最大数は 100 です。 

* Azure Virtual Network Manager のパブリック プレビューでは、テナント間のサポートは提供されていません。

* 仮想ネットワークは、最大で 2 つのメッシュ構成に属することができます。 

## <a name="next-steps"></a>次の手順

Azure portal を使用して [Azure Virtual Network Manager](create-virtual-network-manager-portal.md) インスタンスを作成します。
