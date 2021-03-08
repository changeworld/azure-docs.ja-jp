---
title: チュートリアル - オンプレミス環境をプライベート クラウドにピアリングする
description: Azure VMware Solution のプライベート クラウドに対する ExpressRoute Global Reach ピアリングを作成する方法について説明します。
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: 04be94849f1cd78ff915ca271845b283c8cfc64c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558830"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>チュートリアル:オンプレミス環境をプライベート クラウドにピアリングする

ExpressRoute Global Reach では、オンプレミス環境を Azure VMware Solution のプライベート クラウドに接続します。 プライベート クラウドの ExpressRoute 回線と、オンプレミス環境への既存の ExpressRoute 接続との間に、ExpressRoute Global Reach 接続が確立されます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure portal を使用して、オンプレミスからプライベート クラウドへの ExpressRoute Global Reach ピアリングを有効にする。


## <a name="before-you-begin"></a>開始する前に

ExpressRoute Global Reach を使用して 2 つの ExpressRoute 回線間の接続を有効にする前に、[異なる Azure サブスクリプションで接続を有効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)方法に関するドキュメントを確認してください。  


## <a name="prerequisites"></a>前提条件

- オンプレミス環境を Azure に接続するために使用する、正常に機能している別の ExpressRoute 回線。
- ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。


## <a name="create-an-expressroute-authorization-key-in-the-on-premises-expressroute-circuit"></a>オンプレミスの ExpressRoute 回線で ExpressRoute 承認キーを作成する

1. **[ExpressRoute 回線]** ブレードの [設定] で、 **[承認]** を選択します。

2. 承認キーの名前を入力し、 **[保存]** を選択します。

    :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="[承認] を選択し、承認キーの名前を入力します。":::
  
     作成された新しいキーが、回線の承認キーの一覧に表示されます。
 
 4. 承認キーと ExpressRoute ID を書き留めておきます。 これらは、次の手順でピアリングを行う際に使用します。
 
 
 ## <a name="peer-private-cloud-to-on-premises"></a>プライベート クラウドからオンプレミスにピアリングする

1. プライベート クラウドの **[概要]** の [管理] で、 **[接続] > [ExpressRoute Global Reach] > [追加]** を選択します。

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="メニューから、[接続]、[ExpressRoute Global Reach] タブと選択してから、[追加] を選択する。":::

2. ExpressRoute ID と、前のセクションで作成した承認キーを入力します。

    :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="ExpressRoute ID と承認キーを入力し、[作成] を選択します。":::

3. **［作成］** を選択します 新しい接続が、[オンプレミスのクラウド接続] の一覧に表示されます。  

>[!TIP]
>**[その他]** を選択すると、一覧から接続を削除または切断できます。  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="オンプレミス接続を切断または削除する":::


## <a name="next-steps"></a>次の手順

このチュートリアルでは、オンプレミスからプライベート クラウドへの ExpressRoute Global Reach ピアリングを有効にする方法について説明しました。 

次のチュートリアルに進み、Azure VMware Solution プライベート クラウドのために VMware HCX ソリューションをデプロイして構成する方法を学習します。

> [!div class="nextstepaction"]
> [VMware HCX をデプロイして構成する](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
