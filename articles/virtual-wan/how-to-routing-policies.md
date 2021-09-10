---
title: Virtual WAN ハブのルーティング ポリシーの構成方法
titleSuffix: Azure Virtual WAN
description: Virtual WAN のルーティング ポリシーを構成する方法について説明します
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 08/01/2021
ms.author: wellee
ms.openlocfilehash: 5fb694cd1dab2d53495e2e4b513ce6bfe2ebaff9
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101831"
---
# <a name="how-to-configure-virtual-wan-hub-routing-intent-and-routing-policies"></a>Virtual WAN ハブのルーティング インテントとルーティング ポリシーの構成方法

>[!NOTE] 
> ハブのルーティング インテントは、現在限定的なパブリック プレビュー段階にあります。 
> 
> このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 一部の機能はサポート対象ではないか、機能が制限されることがあります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
> 
> プレビューへのアクセスを取得するには、同じ Azure リージョンにゲートウェイ (サイト間 VPN ゲートウェイ、ポイント対サイトゲートウェイ、ExpressRouteGateways) の両方に 2 つのハブをデプロイし、previewinterhub@microsoft.com ルーティングの目的を構成する仮想 WAN ID、サブスクリプション ID、および Azure リージョンでアクセスしてください。 機能の有効化の確認については、48 営業時間内に応答する予定です。 機能の有効化後に作成されたゲートウェイは、仮想 WAN チームでアップグレードする必要があることに注意してください。

## <a name="background"></a>バックグラウンド 

Azure Firewall Manager を使用してパブリックおよびプライベート トラフィックのポリシーを設定しているお客様は、ルーティング インテントとルーティング ポリシーを使用して、より簡単な方法でネットワークを設定できるようになりました。

ルーティング インテントとルーティング ポリシーを使用すると、Virtual WAN ハブで、インターネット向けおよびプライベート (ポイント対サイト、サイト間、ExpressRoute、Virtual WAN ハブと Virtual Network 内部のネットワーク仮想アプライアンス) トラフィックを転送する方法を指定できます。 ルーティング ポリシーには、インターネット トラフィックとプライベート トラフィックの 2 種類のルーティング ポリシーがあります。 各 Virtual WAN ハブは、最大で 1 つのインターネット トラフィック ルーティング ポリシーと 1 つのプライベート トラフィック ルーティング ポリシーを持つことができます。それぞれでネクスト ホップのリソースが使用されます。 

プライベート トラフィックには、ブランチと Virtual Network の両方のアドレス プレフィックスが含まれますが、ルーティング ポリシーでは、それらはルーティング インテントの概念の 1 つのエンティティと見なされます。

>[!NOTE]
> Virtual WAN ハブのルーティング ポリシーの限定的なパブリック プレビューでは、Virtual WAN ハブが同じリージョンにある場合、ハブ間トラフィックは Azure Firewall によってのみ検査されます。 


* **インターネット トラフィック ルーティング ポリシー**: インターネット トラフィック ルーティング ポリシーが Virtual WAN ハブに構成されている場合、その Virtual WAN ハブへのすべてのブランチ (ユーザー VPN (ポイント対サイト VPN)、サイト間 VPN、および ExpressRoute) および Virtual Network 接続では、インターネットへのトラフィックは、ルーティング ポリシーの一部として指定された Azure Firewall リソースまたはサードパーティのセキュリティ プロバイダーに転送されます。
 

* **プライベート トラフィック ルーティング ポリシー**: プライベート トラフィック ルーティング ポリシーが Virtual WAN ハブに構成されている場合、ハブ間のトラフィックを含む、Virtual WAN ハブ内外の **すべての** ブランチおよび Virtual Network トラフィックは、プライベート トラフィック ルーティング ポリシーに指定された Azure Firewall のネクスト ホップ リソースに転送されます。 

    つまり、プライベート トラフィック ルーティング ポリシーが Virtual WAN ハブで構成されている場合、ブランチ間、ブランチから仮想ネットワーク、仮想ネットワークからブランチ、およびハブ間のすべてのトラフィックが Azure Firewall 経由で送信されます。


## <a name="key-considerations"></a>重要な考慮事項
* 既存のカスタム ルート テーブルが構成されている場合、または既定のルート テーブルに静的ルートが構成されている場合、デプロイのルーティング ポリシーを有効にすることが **できません**。
* 現在、プライベート トラフィック ルーティング ポリシーは、暗号化された ExpressRoute 接続 (ExpressRoute プライベート接続を介して実行されるサイト間 VPN トンネル) を使用するハブではサポートされません。 
* Virtual WAN ハブのルーティング ポリシーの限定的なパブリック プレビューでは、Virtual WAN ハブが同じリージョンにある場合、ハブ間トラフィックは Azure Firewall によってのみ検査されます。
* 現在、ルーティング インテントとルーティング ポリシーは、「**前提条件**」の手順 3 で提供されているカスタム ポータル リンクを使用して構成する必要があります。 ルーティング インテントとルーティング ポリシーは、Terraform、PowerShell、CLI ではサポートされていません。 

## <a name="prerequisites"></a>前提条件
1. Virtual WAN を作成します。 少なくとも 2 つの仮想ハブを **同じ** リージョンに作成します。 たとえば、米国東部に 2 つの仮想ハブを持つ Virtual WAN を作成できます。 
2. 選択したリージョンの仮想ハブに Azure Firewall をデプロイして、Virtual WAN ハブをセキュリティで保護された Virtual WAN ハブに変換します。 Virtual WAN ハブをセキュリティで保護された Virtual WAN ハブに変換する方法の詳細については、[こちらのドキュメント](howto-firewall.md)を参照してください。
3. テストに使用するサイト間 VPN、ポイント対サイト VPN、ExpressRoute ゲートウェイをデプロイします。 **previewinterhub@microsoft.com** に対し、**Virtual WAN リソース ID** と、ルーティング ポリシーを構成する **Azure 仮想ハブ リージョン** を使用して連絡します。 Virtual WAN ID を見つけるには、Azure portal を開いて Virtual WAN リソースにナビゲートして、[設定] > [プロパティ] > [リソース ID] を選択します。 次に例を示します。 
```
    /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>
```
4.  機能の有効化の確認については、24 から 48 時間以内に応答する予定です。 
5. ご使用の仮想ハブに、カスタム ルート テーブルや、defaultRouteTable に追加されたルートが **存在しない** ようにしてください。 既存のカスタム ルート テーブルが構成されている場合、または既定のルート テーブルに静的ルートが構成されている場合、デプロイのルーティング ポリシーを有効にすることが **できません**。 

## <a name="configuring-routing-policies"></a>ルーティング ポリシーの構成
1. 「**前提条件**」セクションの手順 3 の確認メールに記載されているカスタム ポータル リンクから、ルーティング ポリシーを構成する Virtual WAN ハブに移動します。
1. [セキュリティ] で、 **[Secured Virtual hub settings]\(セキュリティ保護付き仮想ハブの設定\)** を選択し、 **[Manage security provider and route settings for this Secured virtual hub in Azure Firewall Manager]\(このセキュリティ保護付き仮想ハブのセキュリティ プロバイダーとルートの設定を Azure Firewall Manager で管理する\)** を選択します。
:::image type="content" source="./media/routing-policies/secured-hub-settings.png"alt-text="セキュリティ保護付きハブの設定を変更する方法を示すスクリーンショット。"lightbox="./media/routing-policies/secured-hub-settings.png":::
1. ルーティング ポリシーを構成するハブをメニューから選択します。
1. **[設定]** の **[セキュリティの構成]** を選択します。
1. インターネット トラフィック ルーティング ポリシーを構成する場合、 **[Internet Traffic]\(インターネット トラフィック\)** で **[Azure Firewall]** または関連するインターネット セキュリティ プロバイダーを選択します。 そうでない場合は、 **[なし]** を選択します。
1. Azure Firewall 経由で (ブランチおよび仮想ネットワーク トラフィックの) プライベート トラフィック ルーティング ポリシーを構成する場合、 **[Private Traffic]\(プライベート トラフィック\)** .のドロップダウンから **[Azure Firewall]** を選択します。 そうでない場合は、 **[Bypass Azure Firewall]\(Azure Firewall をバイパスする\)** を選択します。

:::image type="content" source="./media/routing-policies/configuring-intents.png"alt-text="ルーティング ポリシーの構成方法を示すスクリーンショット。"lightbox="./media/routing-policies/configuring-intents.png":::

7. プライベート トラフィック ルーティング ポリシーを構成し、IANA 以外の RFC1918 プレフィックスをアドバタイズするブランチまたは仮想ネットワークを使用する場合は、 **[Private Traffic Prefixes]\(プライベート トラフィック プレフィックス\)** を選択し、表示されるテキスト ボックスに IANA 以外の RFC1918 プレフィックス範囲を指定します。 **[Done]** を選択します。 

:::image type="content" source="./media/routing-policies/private-prefixes.png"alt-text="プライベート トラフィック プレフィックスを編集する方法を示すスクリーンショット。"lightbox="./media/routing-policies/private-prefixes.png":::

8. **[Inter-hub]\(ハブ間\)** で **[有効]** を選択します。 このオプションを有効にすると、この Virtual WAN ハブのルーティング インテントにルーティング ポリシーが確実に適用されます。 
9. **[保存]** を選択します。 この操作は、完了するまで約 10 分かかります。 
10. ルーティング ポリシーを構成するその他のセキュリティで保護された Virtual WAN ハブに対して手順 2 から 8 を繰り返します。 
 
## <a name="routing-policy-configuration-examples"></a>ルーティング ポリシーの構成例 

次のセクションでは、セキュリティで保護された Virtual WAN ハブにルーティング ポリシーを適用する 2 つの一般的なシナリオについて説明します。

### <a name="all-virtual-wan-hubs-are-secured-deployed-with-azure-firewall"></a>すべての Virtual WAN ハブがセキュリティで保護されている (Azure Firewall を使用してデプロイ)

このシナリオでは、すべての仮想 WAN ハブが Azure Firewall を実装してデプロイされます。 このシナリオでは、インターネット トラフィック ルーティング ポリシー、プライベート トラフィック ルーティング ポリシー、またはその両方を、各 Virtual WAN ハブで構成できます。 

:::image type="content" source="./media/routing-policies/two-secured-hubs-diagram.png"alt-text="セキュリティで保護された 2 つのハブを備えたアーキテクチャを示すスクリーンショット。"lightbox="./media/routing-policies/two-secured-hubs-diagram.png":::

ハブ 1 とハブ 2 がプライベート トラフィックとインターネット トラフィックの両方に対してルーティング ポリシーを持つ次の構成を考えてみます。 

**ハブ 1 の構成:**
* プライベート トラフィック ポリシー (ネクスト ホップはハブ 1 の Azure Firewall)
* インターネット トラフィック ポリシー (ネクスト ホップはハブ 1 の Azure Firewall) 

**ハブ 2 の構成:**
* プライベート トラフィック ポリシー (ネクスト ホップはハブ 2 の Azure Firewall)
* インターネット トラフィック ポリシー (ネクスト ホップはハブ 2 の Azure Firewall) 

このような構成の結果、トラフィック フローは次のようになります。 

> [!NOTE]
> 既定のルート (0.0.0.0/0) はハブ間で伝達 **されない** ため、インターネット トラフィックは **ローカル** の Azure Firewall 経由で送信される必要があります。

| ソース |   終了 |  ハブ 1 の VNet | ハブ 1 のブランチ | ハブ 2 の VNet | ハブ 2 のブランチ| インターネット|
| -------------- | -------- | ---------- | ---| ---| ---| ---|
| ハブ 1 の VNet     | &#8594;| ハブ 1 の AzFW |   ハブ 1 の AzFW    | ハブ 1 および 2 の AzFW | ハブ 1 および 2 の AzFW | ハブ 1 の AzFW |
| ハブ 1 のブランチ   | &#8594;|  ハブ 1 の AzFW  |   ハブ 1 の AzFW    | ハブ 1 および 2 の AzFW | ハブ 1 および 2 の AzFW | ハブ 1 の AzFW|
| ハブ 2 の VNet     | &#8594;| ハブ 1 および 2 の AzFW |   ハブ 1 および 2 の AzFW    | ハブ 2 の AzFW  | ハブ 2 の AzFW | ハブ 2 の AzFW|
| ハブ 2 のブランチ   | &#8594;|   ハブ 1 および 2 の AzFW  |    ハブ 1 および 2 の AzFW    | ハブ 2 の AzFW |  ハブ 2 の AzFW | ハブ 2 の AzFW|


### <a name="mixture-of-secured-and-regular-virtual-wan-hubs"></a>セキュリティ保護と通常の Virtual WAN ハブの混合 

このシナリオでは、すべての Virtual WAN ハブが Azure Firewall を実装してデプロイされるわけではありません。 このシナリオでは、インターネット トラフィック ルーティング ポリシーとプライベート トラフィック ルーティング ポリシーを、セキュリティで保護された Virtual WAN ハブで構成できます。 

ハブ 1 (標準) とハブ 2 (セキュリティ保護) が 1 つの Virtual WAN にデプロイされている次の構成について考えてみます。 ハブ 2 には、プライベート トラフィックとインターネット トラフィックの両方のルーティング ポリシーがあります。 

**ハブ 1 の構成:**
* N/A (ハブが Azure Firewall を使用してデプロイされていない場合はルーティング ポリシーを構成できない)

**ハブ 2 の構成:**
* プライベート トラフィック ポリシー (ネクスト ホップはハブ 2 の Azure Firewall)
* インターネット トラフィック ポリシー (ネクスト ホップはハブ 2 の Azure Firewall) 


:::image type="content" source="./media/routing-policies/one-secured-one-normal-diagram.png"alt-text="1 つのセキュリティ保護されたハブと 1 つの通常のハブを含むアーキテクチャを示すスクリーンショット。"lightbox="./media/routing-policies/one-secured-one-normal-diagram.png":::


 このような構成の結果、トラフィック フローは次のようになります。 既定のルート (0.0.0.0/0) はハブ間で伝達 **されない** ため、ハブ 1 に接続されているブランチおよび仮想ネットワークはハブの Azure Firewall 経由でインターネットにアクセス **できません**。

| ソース |   終了 |  ハブ 1 の VNet | ハブ 1 のブランチ | ハブ 2 の VNet | ハブ 2 のブランチ| インターネット |
| -------------- | -------- | ---------- | ---| ---| ---| --- |
| ハブ 1 の VNet     | &#8594;| 直接 |   直接   | ハブ 2 の AzFW | ハブ 2 の AzFW | - |
| ハブ 1 のブランチ   | &#8594;|  直接 |   直接    | ハブ 2 の AzFW | ハブ 2 の AzFW | - |
| ハブ 2 の VNet     | &#8594;| ハブ 2 の AzFW |   ハブ 2 の AzFW    | ハブ 2 の AzFW  | ハブ 2 の AzFW | ハブ 2 の AzFW|
| ハブ 2 のブランチ   | &#8594;|   ハブ 2 の AzFW  |    ハブ 2 の AzFW    | ハブ 2 の AzFW |  ハブ 2 の AzFW | ハブ 2 の AzFW|


## <a name="troubleshooting"></a>トラブルシューティング

次のセクションでは、Virtual WAN ハブでルーティング ポリシーを構成するときに発生する一般的な問題について説明します。  以下のセクションを読み、それでも問題が解決されない場合は、previewinterhub@microsoft.com に連絡してサポートを依頼してください。 48 営業時間内 (月曜日から金曜日) に応答する予定です。 

### <a name="troubleshooting-configuration-issues"></a>構成の問題のトラブルシューティング
*  限定的なパブリック プレビューへのアクセスが、ご使用のサブスクリプションおよび選択したリージョンで許可されたという確認を previewinterhub@microsoft.com から受け取ったことを確認してください。 プレビューへのアクセスを許可されない限り、ルーティング ポリシーを構成することは **できません**。
* お使いのデプロイでルーティング ポリシー機能を有効にした後、確認メールの一部として提供されるカスタム ポータル リンク **のみ** を使用してください。 Virtual WAN デプロイを管理するために、PowerShell、CLI、または REST API 呼び出しを使用しないでください。  これには、新しいブランチ (サイト間 VPN、ポイント対サイト VPN、または ExpressRoute) 接続の作成が含まれます。 

>[!NOTE]
> Terraform を使用している場合、ルーティング ポリシーは現在サポートされていません。

*  ご使用の仮想ハブに、カスタム ルート テーブルや、defaultRouteTable の静的ルートが存在しないことを確認してください。 カスタム ルート テーブルが構成されていたり、defaultRouteTable に静的ルートが存在したりする場合、Virtual WAN ハブの Firewall Manager から **[Enable interhub]\(ハブ間を有効にする\)** を選択することは **できません**。 

### <a name="troubleshooting-data-path"></a>データ パスのトラブルシューティング 

* 現在、Azure Firewall を使用してハブ間トラフィックを検査する方法は、**同じ** Azure リージョンにデプロイされている Virtual WAN ハブでのみ使用できます。 
* 現在、プライベート トラフィック ルーティング ポリシーは、暗号化された ExpressRoute 接続 (ExpressRoute プライベート接続を介して実行されるサイト間 VPN トンネル) を使用するハブではサポートされません。 
* DefaultRouteTable の [Effective Routes]\(有効なルート\) を確認することで、ルーティング ポリシーが正しく適用されていることを確認できます。 プライベート ルーティング ポリシーが構成されている場合は、DefaultRouteTable のルートに、ネクスト ホップ Azure Firewall が設定されたプライベート トラフィック プレフィックスが表示されます。 インターネット トラフィック ルーティング ポリシーが構成されている場合は、ネクスト ホップ Azure Firewall が設定された DefaultRouteTable の既定の (0.0.0.0/0) ルートが表示されます。
* デプロイで機能が有効になっていると確認された **後** で作成されたサイト間 VPN ゲートウェイまたはポイント対サイト VPN ゲートウェイがある場合は、機能を有効にするためにもう一度 previewinterhub@microsoft.com にアクセスする必要があります。 

### <a name="troubleshooting-azure-firewall"></a>Azure Firewall のトラブルシューティング

* ブランチ/仮想ネットワークで [IANA RFC1918](https://datatracker.ietf.org/doc/html/rfc1918) 以外のプレフィックスを使用している場合は、Firewall Manager の [Private Prefixes]\(プライベート プレフィックス\) テキスト ボックスでこれらのプレフィックスを指定してください。
* Firewall Manager の **[Private Traffic Prefixes]\(プライベート トラフィック プレフィックス\)** テキスト ボックスの一部として RFC1918 以外のアドレスを指定した場合は、RFC1918 以外のプライベート トラフィックに対して SNAT を無効にするために、ファイアウォールで SNAT ポリシーを構成する必要がある場合があります。 詳細については、次の[ドキュメント](../firewall/snat-private-range.md)を参照してください。 
* ネットワーク トラフィックのトラブルシューティングと分析に役立つように、Azure Firewall のログを構成して表示することを検討してください。 Azure Firewall の監視を設定する方法の詳細については、次の[ドキュメント](../firewall/firewall-diagnostics.md)を参照してください。 さまざまな種類の Firewall ログの概要については、[こちら](../firewall/logs-and-metrics.md)を参照してください。
* Azure Firewall の詳細については、[Azure Firewall のドキュメント](../firewall/overview.md)を参照してください。



## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="why-cant-i-edit-the-defaultroutetable-from-the-custom-portal-link-provided-by-previewinterhubmicrosoftcom"></a>previewinterhub@microsoft.com によって提供されるカスタム ポータル リンクから defaultRouteTable を編集できないのはなぜですか?

ルーティング ポリシーの限定的なパブリック プレビューの一部として、Virtual WAN ハブ ルーティングは Firewall Manager によって完全に管理されます。 さらに、ルーティング ポリシーのマネージド プレビューは、カスタム ルーティングと一緒にサポート **されません**。 ルーティング ポリシーによるカスタム ルーティングは、後日サポートされるようになります。 

ただし、 **[Effective Routes]\(有効なルート\)** タブに移動して、DefaultRouteTable の有効なルートを表示することはできます。

### <a name="can-i-configure-a-routing-policy-for-private-traffic-and-also-send-internet-traffic-00000-via-a-network-virtual-appliance-in-a-spoke-virtual-network"></a>プライベート トラフィック用のルーティング ポリシーを構成し、同時にスポーク仮想ネットワーク 内のネットワーク仮想アプライアンスを介してインターネット トラフィック (0.0.0.0/0) を送信できますか?

このシナリオは、限定的なパブリック プレビューではサポートされていません。 ただし、このシナリオの実装に興味がある場合は、previewinterhub@microsoft.com までご連絡ください。 

### <a name="does-the-default-route-00000-propagate-across-hubs"></a>既定のルート (0.0.0.0/0) はハブ間で伝達されますか?

いいえ。 現在、ブランチと仮想ネットワークは、ブランチと仮想ネットワークが接続されている Virtual WAN ハブ内にデプロイされた Azure Firewall を使用してインターネットに送信されます。 リモート ハブの Firewall を介してインターネットにアクセスする接続を構成することはできません。

### <a name="why-do-i-see-rfc1918-prefixes-advertised-to-my-on-premises-devices"></a>オンプレミス デバイスにアドバタイズされた RFC1918 プレフィックスが表示されるのはなぜですか?

プライベート トラフィック ルーティング ポリシーが構成されている場合、Virtual WAN ゲートウェイでは、明示的なブランチと仮想ネットワーク プレフィックスに加えて、既定のルート テーブルにある静的ルート (RFC1918 プレフィックス: 10.0.0.0/8、172.16.0.0/12、192.168.0.0/16) が自動的にアドバタイズされます。

## <a name="next-steps"></a>次のステップ

仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
