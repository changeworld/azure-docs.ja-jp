---
title: ネットワークの分離
description: ユーザーは QnA Maker リソースへのパブリック アクセスを制限できます。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 87726db1f0747c3f9383168321bc3054c685c8a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038723"
---
# <a name="recommended-settings-for-network-isolation"></a>ネットワーク分離の推奨設定

QnA Maker リソースへのパブリック アクセスを制限するには、次の手順に従います。 [仮想ネットワークを構成](../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service (QnA runtime) へのアクセスを制限する

ServiceTag `CognitiveServicesMangement` を使用すると、App Service または ASE (App Service Environment) のネットワーク セキュリティ グループの受信規則の受信アクセスを制限することができます。サービス タグiの詳細については、 [「仮想ネットワーク サービス タグ」の記事](../../../virtual-network/service-tags-overview.md)を参照してください。 

### <a name="regular-app-service"></a>通常の App Service

1. Azure portal から Cloud Shell (PowerShell) を開きます。
2. ページの下部にある PowerShell ウィンドウで、次のコマンドを実行します。

```ps
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "<resource group name>" -WebAppName "<app service name>" -Name "cognitive services Tag" -Priority 100 -Action Allow -ServiceTag "CognitiveServicesManagement" 
```
3.  追加されたアクセス規則が **[ネットワーク]** タブの **[アクセス制限]** セクションに存在することを確認します。  

    > [!div class="mx-imgBorder"]
    > [ ![アクセス制限規則のスクリーンショット]( ../media/network-isolation/access-restrictions.png) ](  ../media/network-isolation/access-restrictions.png#lightbox)

4. https://qnamaker.ai ポータルの **テスト ペイン** にアクセスするには、ポータルにアクセスする **マシンのパブリック IP アドレス** を追加します。 **[アクセス制限]** ページから **[ルールの追加]** を選択し、クライアント IP へのアクセスを許可します。 

    > [!div class="mx-imgBorder"]
    > [ ![パブリック IP アドレスを追加したアクセス制限規則のスクリーンショット]( ../media/network-isolation/public-address.png) ](  ../media/network-isolation/public-address.png#lightbox)

### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker App Service をホストするように App Service Environment を構成する

App Service Environment (ASE) を使用して、QnA Maker App Service のインスタンスをホストできます。 次の手順に従います。

1. [新しい Azure Cognitive Search リソース](https://ms.portal.azure.com/#create/Microsoft.Search)を作成します。
2. App Service を使用して外部 ASE を作成します。
    - 手順については、この [App Service のクイックスタート](../../../app-service/environment/create-external-ase.md#create-an-ase-and-an-app-service-plan-together)に従います。 このプロセスには最大で 1 から 2 時間かかります。
    - 最後に、App Service エンドポイントが `https://<app service name>.<ASE name>.p.azurewebsite.net` のように表示されます。 
    - 例: `https:// mywebsite.myase.p.azurewebsite.net`  
3. 次の App Service の構成を追加します。
    
    | 名前                       | 値                                                     |
    |:---------------------------|:----------------------------------------------------------| 
    | PrimaryEndpointKey         | `<app service name>-PrimaryEndpointKey`                   | 
    | AzureSearchName            | `<Azure Cognitive Search Resource Name from step #1>`     | 
    | AzureSearchAdminKey        | `<Azure Cognitive Search Resource admin Key from step #1>`| 
    | QNAMAKER_EXTENSION_VERSION | `latest`                                                  |
    | DefaultAnswer              | `no answer found`                                         |

4. https://qnamaker.ai ポータルのテスト ペインへのアクセスを許可するには、CORS origin "*" を App Service に追加します。 **CORS** は、[App Service] ペインの API ヘッダーの下にあります。

    > [!div class="mx-imgBorder"]
    > [ ![App Service UI 内の CORS インターフェイスのスクリーンショット]( ../media/network-isolation/cross-orgin-resource-sharing.png) ](  ../media/network-isolation/cross-orgin-resource-sharing.png#lightbox)

5. Azure Resource Manager を使用して QnA Maker Cognitive Services インスタンス (Microsoft.CognitiveServices/accounts) を作成します。 QnA Maker エンドポイントは、上で作成した App Service エンドポイント (`https:// mywebsite.myase.p.azurewebsite.net`) に設定する必要があります。 [参照用に使用できるサンプル Azure Resource Manager テンプレート](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/QnAMakerASEArmTemplate)はこちらです。

### <a name="related-questions"></a>関連する質問

#### <a name="can-qna-maker-be-deployed-to-an-internal-ase"></a>QnA Maker を内部 ASE にデプロイすることはできますか。

外部 ASE を使用する主な理由は、QnAMaker サービス バックエンド (オーサリング API) をインターネット経由で App Service に接続するようにできるためです。 ただし、`CognitiveServicesManagement` サービス タグに関連付けられたアドレスからの接続のみを許可するように受信アクセス制限を追加することで、引き続き保護することができます。

それでも内部 ASE を使用したい場合は、アプリ ゲートウェイの DNS TLS/SSL 証明書を使用して、その特定の QnA Maker アプリをパブリック ドメインの ASE で公開する必要があります。詳細については、[App Services の Enterprise デプロイに関する記事](/azure/architecture/reference-architectures/enterprise-integration/ase-standard-deployment)を参照してください。

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search リソースへのアクセスを制限する

Cognitive Search インスタンスは、QnA Maker リソースの作成後、プライベート エンドポイントを介して分離できます。 アクセスをロック ダウンするには、次の手順に従います。

1. 新しい[仮想ネットワーク (VNet)](https://portal.azure.com/#create/Microsoft.VirtualNetwork-ARM) を作成するか、既存の ASE (App Service Environment) の VNet を使用します。
2. VNet リソースを開き、 **[サブネット]** タブで 2 つのサブネットを作成します。 1 つは App Service 用 **(appservicesubnet)** で、もう 1 つは委任なしの Cognitive Search リソース用のサブネット **(searchservicesubnet)** です。 

    > [!div class="mx-imgBorder"]
    > [ ![仮想ネットワーク サブネット UI インターフェイスのスクリーンショット]( ../media/network-isolation/subnets.png) ](  ../media/network-isolation/subnets.png#lightbox)

3. Cognitive Search サービス インスタンスの **[ネットワーク]** タブに移動し、エンドポイント接続データをパブリックからプライベートに切り替えます。 この操作は実行時間の長いプロセスであり、完了するまで **最大で 30 分かかることがあります**。

    > [!div class="mx-imgBorder"]
    > [ ![パブリック/プライベート トグル ボタンを使用したネットワーク UI のスクリーンショット]( ../media/network-isolation/private.png) ](  ../media/network-isolation/private.png#lightbox)

4. 検索リソースをプライベートに切り替えたら、 **[プライベート エンドポイント** の追加] を選択します。
    - **[基本] タブ**: 検索リソースと同じリージョンにエンドポイントを作成していることを確認します。
    - **[リソース] タブ**: 必要な検索リソースの種類 `Microsoft.Search/searchServices` を選択します。

    > [!div class="mx-imgBorder"]
    > [ ![プライベート エンドポイントの作成の UI ウィンドウのスクリーンショット]( ../media/network-isolation/private-endpoint.png) ](  ../media/network-isolation/private-endpoint.png#lightbox)

    - **[構成] タブ**: 手順 2 で作成した VNet のサブネット (searchservicesubnet) を使用します。 その後、 **[プライベート DNS 統合]** セクションで、対応するサブスクリプションを選択し、**privatelink.search.windows.net** という新しいプライベート DNS ゾーンを作成します。

     > [!div class="mx-imgBorder"]
     > [ ![サブネット フィールドが設定されたプライベート エンドポイントの作成の UI ウィンドウのスクリーンショット]( ../media/network-isolation/subnet.png) ](  ../media/network-isolation/subnet.png#lightbox)

5. 通常の App Service の VNET 統合を有効にします。 既に VNET にアクセスできるため、ASE では、この手順をスキップできます。
    - App Service の **[ネットワーク]** セクションに移動し、 **[VNet 統合]** を開きます。
    - 手順 2 で作成した専用 App Service VNet のサブネット (appservicevnet) にリンクします。
    
     > [!div class="mx-imgBorder"]
     > [ ![VNET 統合 UI のスクリーンショット]( ../media/network-isolation/integration.png) ](  ../media/network-isolation/integration.png#lightbox)

Azure Search リソースに[プライベート エンドポイントを作成](../reference-private-endpoint.md)します。

QnA Maker リソースへのパブリック アクセスを制限するには、次の手順に従います。 [仮想ネットワークを構成](../../cognitive-services-virtual-networks.md?tabs=portal)して、Cognitive Services リソースをパブリック アクセスから保護します。

VNet に基づいて Cognitive Service リソースへのアクセスを制限した後、オンプレミス ネットワークまたはローカル ブラウザーから https://qnamaker.ai ポータル上のナレッジベースを参照します。
- [オンプレミス ネットワーク](../../cognitive-services-virtual-networks.md?tabs=portal#configuring-access-from-on-premises-networks)へのアクセスを許可します。
- [ローカルのブラウザーとマシン](../../cognitive-services-virtual-networks.md?tabs=portal#managing-ip-network-rules)へのアクセスを許可します。
- **[ネットワーク]** タブの **[ファイアウォール] セクションの下にマシンのパブリック IP アドレス** を追加します。既定では、`portal.azure.com` には現在アクセスしているマシンのパブリック IP が表示され (このエントリを選択します)、次に **[保存]** を選択します。

     > [!div class="mx-imgBorder"]
     > [ ![ファイアウォールと仮想ネットワークの構成 UI のスクリーンショット]( ../media/network-isolation/firewall.png) ](  ../media/network-isolation/firewall.png#lightbox)
