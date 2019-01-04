---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 12/14/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: b1550fc07ee4edfe98482a2e880f201ceb66705f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53995727"
---
### <a name="what-is-azure-firewall"></a>Azure Firewall とは

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。

### <a name="what-capabilities-are-supported-in-azure-firewall"></a>Azure Firewall ではどのような機能がサポートされていますか?

* サービスとしてのステートフル ファイアウォール
* 無制限のクラウド スケーラビリティを備えたビルトインの高可用性
* FQDN フィルタリング
* FQDN のタグ
* ネットワーク トラフィックのフィルタリング規則
* 送信 SNAT サポート
* 受信 DNAT のサポート
* Azure サブスクリプションと VNET をまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。
* Azure Monitor との完全統合によるログ記録と分析

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall の一般的なデプロイ モデルを教えてください

Azure Firewall は任意の仮想ネットワークにデプロイできますが、通常、お客様は中央の仮想ネットワークにデプロイし、ハブ アンド スポーク モデルでこれに他の仮想ネットワークをピアリングします。 こうすることで、この中央のファイアウォールの仮想ネットワークを指すように、ピアリングされた仮想ネットワークから既定のルートを設定できます。

### <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall のインストール方法を教えてください

Azure portal、PowerShell、REST API、またはテンプレートを使用して、Azure Firewall を設定できます。 手順については、「[チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する](../articles/firewall/tutorial-firewall-deploy-portal.md)」を参照してください。

### <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall の概念をいくつか教えてください。

Azure Firewall は、ルールとルール コレクションをサポートしています。 ルール コレクションは、同じ順序と優先度を共有するルールのセットです。 ルール コレクションは、その優先度の順に実行されます。 ネットワーク ルール コレクションはアプリケーション ルール コレクションよりも優先されます。また、すべてのルールは終了されます。

次の 2 つの種類のルール コレクションがあります。

* "*アプリケーション ルール*": サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を構成できます。
* "*ネットワーク ルール*": 送信元アドレス、プロトコル、宛先ポート、および送信先アドレスを含むルールを構成できます。

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall は受信トラフィック フィルター処理をサポートしていますか?

Azure Firewall は受信と送信のフィルター処理をサポートしています。 受信保護は HTTP/S プロトコル用です。 たとえば、RDP、SSH、および FTP プロトコルです。

### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall では、どのログ記録および分析サービスがサポートされていますか?

Azure Firewall は、ファイアウォール ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hubs にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 詳細については、[Azure Firewall のログを監視する方法に関するチュートリアル](../articles/firewall/tutorial-diagnostics.md)を参照してください。

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall の動作は、マーケットプレースの NVA などの既存のサービスとどのように異なりますか?

Azure Firewall は、特定のお客様のシナリオに対応できる基本的なファイアウォール サービスです。 サード パーティ製の NVA と Azure Firewall を組み合わせることが想定されています。 よりよい連携が重要な優先項目です。

### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF と Azure Firewall の違いは何ですか?

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションの受信保護を一元的に行う Application Gateway の機能です。 Azure Firewall は、非 HTTP/S プロトコル (例: RDP、SSH、FTP など) の受信保護、すべてのポートとプロトコルに対する送信ネットワークレベルの保護、送信 HTTP/S に対するアプリケーションレベルの保護を提供します。

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>ネットワーク セキュリティ グループ (NSG) と Azure Firewall の違いは何ですか?

Azure Firewall サービスは、ネットワーク セキュリティ グループの機能を補完します。 全体で、優れた "多層防御" ネットワーク セキュリティを実現します。 ネットワーク セキュリティ グループには、分散ネットワーク レイヤーのトラフィック フィルター機能があり、この機能によって各サブスクリプションの仮想ネットワーク内のリソースに対するトラフィックを制限します。 Azure Firewall は、完全にステートフルな一元化されたネットワーク ファイアウォールです。さまざまなサブスクリプションと仮想ネットワーク全体にネットワークレベルとアプリケーションレベルの保護を提供します。

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>サービス エンドポイントに Azure Firewall を設定するにはどうすればよいですか?

PaaS サービスへのアクセスをセキュリティで保護するには、サービス エンドポイントをお勧めします。 Azure Firewall サブネット内のサービス エンドポイントを有効にし、接続されているスポーク仮想ネットワーク上ではそれらを無効にすることを選択できます。 このようにして、サービス エンドポイントのセキュリティとすべてのトラフィックの一元的ログ記録という、両方の機能の長所を生かすことができます。

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall の価格を教えてください

Azure Firewall には、固定コスト + 変動コストがあります。

* 固定料金: 1.25 ドル/ファイアウォール/時間
* 変動料金: $ 0.03/ファイアウォールで処理される GB (イングレスまたはエグレス)

割り当て解除済みファイアウォールに関するコストは発生しません。

詳細については、「[Azure Firewall の価格](https://azure.microsoft.com/pricing/details/azure-firewall/)」を参照してください。

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall の停止と起動の方法を教えてください

Azure PowerShell の *deallocate* メソッドと *allocate* メソッドを使用できます。

例: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzureRmFirewall -AzureFirewall $azfw
```

```azurepowershell
#Start a firewall

$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzureRmPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzureRmFirewall -AzureFirewall $azfw
```

> [!NOTE]
> ファイアウォールとパブリック IP を、元のリソース グループとサブスクリプションに再割り当てする必要があります。

### <a name="what-are-the-known-service-limits"></a>既知のサービスの制限には何がありますか?

Azure Firewall サービスの制限については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../articles/azure-subscription-service-limits.md#azure-firewall-limits)」を参照してください。

### <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>Azure Firewall では、ハブ仮想ネットワークで 2 つのスポーク仮想ネットワーク間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

はい。ハブ仮想ネットワークで Azure Firewall を使用して、2 つのスポーク仮想ネットワーク間のトラフィックをルーティングしたりフィルター処理したりできます。 各スポーク仮想ネットワークのサブネットを正常に動作させるためには、このシナリオで既定のゲートウェイとして Azure Firewall をポイントする UDR が必要です。

### <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>Azure Firewall では、同じ仮想ネットワーク (またはピアリングされた仮想ネットワーク) のサブネット間のネットワーク トラフィックを転送したりフィルター処理したりできますか?

同じ仮想ネットワーク内または直接ピアリングされた仮想ネットワーク内のサブネット間のトラフィックは、UDR が既定のゲートウェイとして Azure Firewall をポイントする場合でも直接ルーティングされます。 内部ネットワークのセグメント化の推奨される方法は、ネットワーク セキュリティ グループを使用することです。 このシナリオでサブネット間トラフィックをファイアウォールに送信するには、UDR に両方のサブネットのターゲットのサブネット ネットワーク プレフィックスを明示的に含める必要があります。

### <a name="are-there-any-firewall-resource-group-restrictions"></a>ファイアウォール リソース グループの制限はありますか。

はい。 ファイアウォール、サブネット、VNet、パブリック IP アドレスすべては同じリソース グループに属していなければなりません。

### <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>インバウンド ネットワーク トラフィックの DNAT を構成する際、そのトラフィックを許可するための、対応するネットワーク ルールも構成しなければならないのでしょうか。

いいえ。 NAT ルールは、変換されたトラフィックを許可するための対応するネットワーク ルールを暗黙的に追加します。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 Azure Firewall ルール処理ロジックの詳細については、「[Azure Firewall ルール処理ロジック](../articles/firewall/rule-processing.md)」を参照してください。
