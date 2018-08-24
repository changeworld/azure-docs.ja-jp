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
ms.openlocfilehash: 5601f8d90f107636d2899a024772dccc8f75b69d
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2018
ms.locfileid: "40131643"
---
### <a name="what-is-azure-firewall"></a>Azure Firewall とは

Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 サブスクリプションと仮想ネットワークをまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。 現在、Azure Firewall はパブリック プレビュー段階にあります。

### <a name="what-capabilities-are-supported-in-the-azure-firewall-public-preview-release"></a>Azure Firewall パブリック プレビュー リリースではどのような機能がサポートされていますか?  

* サービスとしてのステートフル ファイアウォール
* 無制限のクラウド スケーラビリティを備えたビルトインの高可用性
* FQDN フィルタリング 
* ネットワーク トラフィックのフィルタリング規則
* 送信 SNAT サポート
* Azure サブスクリプションと VNET をまたいでアプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録できます。
* Azure Monitor との完全統合によるログ記録と分析 

### <a name="how-can-i-join-the-azure-firewall-public-preview"></a>Azure Firewall パブリック プレビューに参加する方法を教えてください

現在、Azure Firewall はマネージド パブリック プレビューです。Azure Firewall パブリック プレビューのドキュメントで説明されているように、Register-AzureRmProviderFeature PowerShell コマンドを使用して参加できます。

### <a name="what-is-the-pricing-for-azure-firewall"></a>Azure Firewall の価格を教えてください

Azure Firewall には、固定コスト + 変動コストがあります。 価格は以下のとおりです。パブリック プレビュー中は、さらに 50% 割り引かれます。

* 固定料金: 1.25 ドル/ファイアウォール/時間
* 変動料金: $ 0.03/ファイアウォールで処理される GB (イングレスまたはエグレス)。

### <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Azure Firewall の一般的なデプロイ モデルを教えてください

Azure Firewall は任意の VNET にデプロイできますが、通常、ハブ & スポーク モデルでは、中央の VNET と、そのピアの VNET に Azure Firewall をデプロイします。 こうすることで、この中央のファイアウォール VNET を指すようにピアリングされた VNET からの既定のルートを設定できます。

### <a name="how-can-i-install-the-azure-firewall"></a>Azure Firewall のインストール方法を教えてください

Azure Firewall の設定には、Azure portal、PowerShell、REST API、またはテンプレートを使用できます。 手順については、「[チュートリアル: Azure portal を使用して Azure Firewall をデプロイして構成する](../articles/firewall/tutorial-firewall-deploy-portal.md)」を参照してください。

### <a name="what-are-some-azure-firewall-concepts"></a>Azure Firewall の概念をいくつか教えてください

Azure Firewall は、ルールとルール コレクションをサポートしています。 ルール コレクションは、同じ順序と優先度を共有するルールのセットです。 ルール コレクションは優先順位に沿ってで実行され、ネットワーク ルール コレクションはアプリケーション ルール コレクションよりも優先されます。また、すべてのルールは終了されます。
次の 2 つの種類のルール コレクションがあります。

* アプリケーション ルール: サブネットからアクセスできる完全修飾ドメイン名 (FQDN) を構成することができます。 
* ネットワーク ルール: 送信元アドレス、プロトコル、宛先ポート、および送信先アドレスを含むルールを構成できます。 

### <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>Azure Firewall は受信トラフィック フィルター処理をサポートしていますか?

Azure Firewall パブリック プレビューは、送信のフィルター処理のみをサポートしています。 一般公開 (GA) の Azure Firewall では、非 HTTP/S プロトコル (例: RDP、SSH、FTP) の受信保護が暫定的に予定されています。  
 
### <a name="what-logginganalytics-is-supported-by-the-azure-firewall"></a>Azure Firewall はどのようなログ/分析をサポートしていますか?

Azure Firewall は、Firewall ログの表示と分析について Azure Monitor と統合されています。 Log Analytics、Azure Storage、または Event Hub にログを送信できます。 Log Analytics や、Excel や Power BI などのさまざまなツールで分析できます。 詳細については、「[チュートリアル: Azure Firewall のログを監視する](../articles/firewall/tutorial-diagnostics.md)」を参照してください。

### <a name="how-does-azure-firewall-work-relative-to-existing-like-nvas-in-the-marketplace"></a>Azure Firewall は市場の NVA などの既存製品と関連してどのように機能しますか?

Azure Firewall は、特定のお客様のシナリオに対応できる基本的なファイアウォール サービスです。 サード パーティ製の NVA と Azure Firewall を組み合わせることが予想されます。 よりよい連携が重要な優先項目です。
 
### <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Application Gateway WAF と Azure Firewall の違いは何ですか?

Web アプリケーション ファイアウォール (WAF) は、一般的な脆弱性やその悪用から Web アプリケーションの受信保護を一元的に行う Application Gateway の機能です。 Azure Firewall は、すべてのポートとプロトコルに対する送信ネットワーク レベルの保護と、送信 HTTP/S に対するアプリケーション レベルの保護を提供します。 一般公開 (GA) の Azure Firewall では、非 HTTP/S プロトコル (例: RDP、SSH、FTP) の受信保護が暫定的に予定されています。

### <a name="what-is-the-difference-between-network-security-groups-nsg-and-azure-firewall"></a>ネットワーク セキュリティ グループ (NSG) と Azure Firewall の違いは何ですか?

Azure Firewall サービスは、ネットワーク セキュリティ グループの機能を補完し、それらの機能と連携して優れた多層防御のネットワーク セキュリティ機能を提供します。 NSG には、各サブスクリプションの仮想ネットワーク内のリソースに対するトラフィックを制限する分散ネットワーク レイヤーのトラフィック フィルター機能があります。  Azure Firewall は、完全にステートフルな一元化されたネットワーク ファイアウォールです。さまざまなサブスクリプションと仮想ネットワーク (VNet) 全体にネットワークとアプリケーション レベルの保護を提供します。 

### <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>サービス エンドポイントに Azure Firewall を設定するにはどうすればよいですか?

PaaS サービスへのアクセスをセキュリティで保護するには、サービス エンドポイントをお勧めします。 Azure Firewall をご利用の場合、Azure Firewall サブネットでサービス エンドポイントを有効にし、接続されているスポーク VNET 上でサービス エンドポイントを無効にすると、すべてのトラフィックについてサービス エクスポートのセキュリティと一元的なログという 2 つの機能の恩恵を受けることができます。

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

* Azure Firewall には、1000 TB/ファイアウォール/月のソフト制限があります。 
* 中央の VNET で実行されている Azure Firewall の場合、VNET のピアリングの上限 (最大 50 個のスポーク VNET) に従います。  
* Azure Firewall はグローバル ピアリングで機能しないので、リージョンごとにファイアウォールを少なくとも 1 つ展開する必要があります。
* Azure Firewall は、10,000 個のアプリケーション ルールと 10,000 個のネットワーク ルールをサポートします。