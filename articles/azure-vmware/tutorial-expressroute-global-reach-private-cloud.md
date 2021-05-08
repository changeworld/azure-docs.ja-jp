---
title: チュートリアル - オンプレミス環境をプライベート クラウドにピアリングする
description: Azure VMware Solution のプライベート クラウドに対する ExpressRoute Global Reach ピアリングを作成する方法について説明します。
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 798b822989127ccbb00e971de2cc4147ac234259
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449564"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>チュートリアル:オンプレミス環境をプライベート クラウドにピアリングする

ExpressRoute Global Reach では、オンプレミス環境を Azure VMware Solution のプライベート クラウドに接続します。 プライベート クラウドの ExpressRoute 回線と、オンプレミス環境への既存の ExpressRoute 接続との間に、ExpressRoute Global Reach 接続が確立されます。 

[Azure で VMware プライベート クラウド用のネットワークを構成する](tutorial-configure-networking.md)ときに使用する ExpressRoute 回線には、認可キーを作成して使用する必要があります。  ExpressRoute 回線からの認可キーを既に 1 つ使用していたことを前提に、このチュートリアルでは、オンプレミスの ExpressRoute 回線とピアリングするための 2 つ目の認可キーを作成します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プライベート クラウドの ExpressRoute 回線である "_回線 2_" 用に 2 つ目の認可キーを作成します。
> * "_回線 1_" のサブスクリプションで、Azure portal を使用するか、Cloud Shell で Azure CLI を使用するかいずれかの方法で、オンプレミスからプライベート クラウドへの ExpressRoute Global Reach ピアリングを有効にします。


## <a name="before-you-begin"></a>開始する前に

ExpressRoute Global Reach を使用して 2 つの ExpressRoute 回線間の接続を有効にする前に、[異なる Azure サブスクリプションで接続を有効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)方法に関するドキュメントを確認してください。  

## <a name="prerequisites"></a>前提条件

- ExpressRoute 回線が Azure 仮想ネットワーク (VNet) 内の ExpressRoute ゲートウェイとピアリングされている Azure VMware Solution プライベート クラウドとの間に確立された接続。これがピアリング手順から "回線 2" になります。
- オンプレミス環境を Azure に接続するために使用される、正常に機能している別個の ExpressRoute 回線。ピアリング手順の観点からこれが "回線 1" になります。
- ExpressRoute Global Reach ピアリングのための重複していない /29 [ネットワーク アドレス ブロック](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings)。
- ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。

>[!IMPORTANT]
>これらの前提条件の環境では、オンプレミスの ExpressRoute 回線が "_回線 1_" で、プライベート クラウドの ExpressRoute 回線は、別のサブスクリプション内に存在しており、"_回線 2_" というラベルが付けられています。

## <a name="create-an-expressroute-authorization-key-in-the-private-cloud-expressroute-circuit"></a>プライベート クラウドの ExpressRoute 回線で ExpressRoute 承認キーを作成する

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>認可キーを使用してプライベート クラウドをオンプレミスにピアリングする
プライベート クラウドの ExpressRoute 回線用に承認キーを作成したので、プライベート クラウドの ExpressRoute 回線をオンプレミスの ExpressRoute 回線とピアリングすることができます。 ピアリングは、**Azure portal** で、または **Cloud Shell で Azure CLI** を使用して、オンプレミスの ExpressRoute 回線の観点から行われます。 どちらの方法でも、プライベート クラウドの ExpressRoute 回線のリソース ID と承認キーを使用して、ピアリングを完了します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)
 
1. オンプレミスの ExpressRoute 回線と同じサブスクリプションを使用して [Azure portal](https://portal.azure.com) にサインインします。

1. プライベート クラウドの **[概要]** の [管理] で、 **[接続]**  >  **[ExpressRoute Global Reach]**  >  **[追加]** を選択します。

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution プライベート クラウドの [ExpressRoute Global Reach] タブを示すスクリーンショット。":::

1. オンプレミス クラウド接続を作成します。 次のいずれかを行ってから、 **[作成]** を選択します。

   - 一覧から **ExpressRoute 回線** を選択します。または
   - 回線 ID がある場合は、それをフィールドに貼り付け、先ほど作成した承認キーを指定します。

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="ExpressRoute ID と承認キーを入力し、[作成] を選択します。":::   
   
   新しい接続が、[オンプレミスのクラウド接続] の一覧に表示されます。

>[!TIP]
>**[その他]** を選択すると、一覧から接続を削除または切断できます。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="オンプレミス接続を切断または削除する":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

オンプレミス環境と Azure VMware Solution プライベート クラウドとの間で ExpressRoute Global Reach ピアリングを構成する助けとなるように、具体的な詳細や例によって [CLI コマンド](../expressroute/expressroute-howto-set-global-reach-cli.md)が強化されてきています。

>[!TIP]
>Azure CLI コマンド出力を簡潔にするために、これらの手順では [`–query` 引数](/cli/azure/query-azure-cli)を使用して JMESPath クエリを実行して、必要な結果のみを表示しています。

1. オンプレミスの ExpressRoute 回線と同じサブスクリプションを使用して [Azure portal](https://portal.azure.com) にサインインします。 

1. Cloud Shell を開き、シェルを bash のままにします。

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Azure portal の Cloud Shell を示すスクリーンショット。":::

1. 回線 2 のリソース ID と認可キーを渡して、回線 1 に対してピアリングを作成します。 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="コマンドと、回線 1 と回線 2 の間のピアリングが成功した結果を示すスクリーンショット。":::

オンプレミス環境から ExpressRoute Global Reach ピアリングを介してプライベート クラウドに接続できます。

>[!TIP]
>ピアリングを削除するには、「[オンプレミス ネットワーク間の接続を無効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks)」の手順に従います。


---

## <a name="next-steps"></a>次の手順

このチュートリアルでは、オンプレミスからプライベート クラウドへの ExpressRoute Global Reach ピアリングを有効にする方法について説明しました。 

次のチュートリアルに進み、Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法を学習します。

> [!div class="nextstepaction"]
> [VMware HCX をデプロイして構成する](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
