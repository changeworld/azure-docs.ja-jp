---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: ''
ms.topic: include
ms.date: 8/13/2018
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: a63a12658bd0a4b4d018d51824af9814691a3cbf
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "40183725"
---
### <a name="what-is-azure-firewall"></a>Azure Firewall とは

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 完全にステートフルなサービスとしてのファイアウォールで、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えています。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。 現在、Azure Firewall はパブリック プレビュー段階にあります。

### <a name="which-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Azure Firewall パブリック プレビュー リリースではどの機能がサポートされていますか?  

* サービスとしてのステートフル ファイアウォール
* 無制限のクラウド スケーラビリティを備えたビルトインの高可用性
* FQDN フィルタリング 
* ネットワーク トラフィックのフィルタリング規則
* 送信 SNAT サポート
* Azure サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録する機能
* Azure Monitor との完全統合によるログ記録と分析 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Azure Firewall パブリック プレビューに参加する方法を教えてください

現在、Azure Firewall はマネージド パブリック プレビューです。Register-AzureRmProviderFeature PowerShell コマンドを使用して参加できます。 このコマンドについては、Azure Firewall パブリック プレビューのドキュメントで説明されています。

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall の価格を教えてください

Azure Firewall には、固定コストと変動コストがあります。 価格は以下のとおりです。パブリック プレビュー中は、さらに 50% 割り引かれます。

* 固定料金: 1.25 ドル/ファイアウォール/時間
* 変動料金: $ 0.03/ファイアウォールで処理される GB (イングレスまたはエグレス)

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

Azure Firewall パブリック プレビューは、送信のフィルター処理のみをサポートしています。 一般公開 (GA) の Azure Firewall では、非 HTTP/S プロトコル (例: RDP、SSH、または FTP) の受信保護が暫定的に予定されています。  
 
### <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Azure Firewall では、どのログ記録および分析サービスがサポートされていますか?

Azure Firewall は、ファイアウォール ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hubs にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 詳細については、「[チュートリアル: Azure Firewall のログを監視する](../articles/firewall/tutorial-diagnostics.md)」を参照してください。

### <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Azure Firewall の動作は、マーケットプレースの NVA などの既存のサービスとどのように異なりますか?

Azure Firewall は、特定のお客様のシナリオに対応できる基本的なファイアウォール サービスです。 サード パーティ製の NVA と Azure Firewall を組み合わせることが想定されています。 よりよい連携が重要な優先項目です。
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF と Azure Firewall の違いは何ですか?

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションの受信保護を一元的に行う Application Gateway の機能です。 Azure Firewall は、すべてのポートとプロトコルに対する送信ネットワークレベルの保護と、送信 HTTP/S に対するアプリケーションレベルの保護を提供します。 一般公開 (GA) の Azure Firewall では、非 HTTP/S プロトコル (例: RDP、SSH、FTP) の受信保護が暫定的に予定されています。

### <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>ネットワーク セキュリティ グループ (NSG) と Azure Firewall の違いは何ですか?

Azure Firewall サービスは、ネットワーク セキュリティ グループの機能を補完します。 全体で、優れた "多層防御" ネットワーク セキュリティを実現します。 ネットワーク セキュリティ グループには、分散ネットワーク レイヤーのトラフィック フィルター機能があり、この機能によって各サブスクリプションの仮想ネットワーク内のリソースに対するトラフィックを制限します。 Azure Firewall は、完全にステートフルな一元化されたネットワーク ファイアウォールです。さまざまなサブスクリプションと仮想ネットワーク全体にネットワークレベルとアプリケーションレベルの保護を提供します。 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>サービス エンドポイントに Azure Firewall を設定するにはどうすればよいですか?

PaaS サービスへのアクセスをセキュリティで保護するには、サービス エンドポイントをお勧めします。 Azure Firewall サブネット内のサービス エンドポイントを有効にし、接続されているスポーク仮想ネットワーク上ではそれらを無効にすることを選択できます。 このようにして、サービス エンドポイントのセキュリティとすべてのトラフィックの一元的ログ記録という、両方の機能の長所を生かすことができます。

### <a name="how-can-i-stop-and-start-azure-firewall"></a>Azure Firewall の停止と起動の方法を教えてください

Azure PowerShell の *deallocate* メソッドと *allocate* メソッドを使用できます。

例: 

```azurepowershell
# Stop an exisitng firewall

$azfw = Get-AzureRmFirewall -Name "FW Name” -ResourceGroupName "RG Name"
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

### <a name="what-are-the-known-service-limits"></a>既知のサービスの制限には何がありますか?

* Azure Firewall には、1 か月あたり 1 ファイアウォールにつき 1,000 TB のソフト制限があります。 
* 中央の仮想ネットワークで実行される Azure Firewall のインスタンスには、仮想ネットワーク ピアリングの上限があり、スポーク仮想ネットワークは最大 50 個に制限されます。  
* Azure Firewall はグローバル ピアリングで機能しないので、リージョンごとにファイアウォールを少なくとも 1 つ展開する必要があります。
* Azure Firewall では、10,000 個のアプリケーション ルールと 10,000 個のネットワーク ルールがサポートされます。