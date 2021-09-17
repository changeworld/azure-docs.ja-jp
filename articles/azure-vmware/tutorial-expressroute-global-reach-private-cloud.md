---
title: オンプレミス環境を Azure VMware Solution にピアリングする
description: Azure VMware Solution のプライベート クラウドに対する ExpressRoute Global Reach ピアリングを作成する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy21q4, contperf-fy22q1
ms.date: 07/28/2021
ms.openlocfilehash: b930aab15ef9af8e43919af1671f44c20e6b2075
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730256"
---
# <a name="peer-on-premises-environments-to-azure-vmware-solution"></a>オンプレミス環境を Azure VMware Solution にピアリングする

Azure VMware Solution のプライベート クラウドをデプロイしたら、それをご自分のオンプレミスの環境に接続します。 ExpressRoute Global Reach では、オンプレミス環境を Azure VMware Solution のプライベート クラウドに接続します。 プライベート クラウドの ExpressRoute 回線と、オンプレミス環境への既存の ExpressRoute 接続との間に、ExpressRoute Global Reach 接続が確立されます。 

:::image type="content" source="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" alt-text="ExpressRoute Global Reach のオンプレミス ネットワーク接続を示す図。" lightbox="media/pre-deployment/azure-vmware-solution-on-premises-diagram.png" border="false":::

>[!NOTE]
>VPN 経由での接続は可能ですが、このクイック スタート ガイドの範囲外です。

この記事では、次のことを行います。

> [!div class="checklist"]
> * オンプレミスの ExpressRoute 回線で ExpressRoute 承認キーを作成する
> * オンプレミスの ExpressRoute 回線でプライベート クラウドをピアリングする
> * オンプレミス ネットワークの接続性を検証する

完了したら、最後に推奨される次の手順に従って、この入門ガイドの手順に進みます。

## <a name="prerequisites"></a>前提条件

- [さまざまな Azure サブスクリプションで接続を有効にする](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions)方法に関するドキュメントを確認します。  

- オンプレミス環境を Azure に接続するための、正常に機能している別の ExpressRoute 回線。これが、ピアリングの "_回線 1_" になります。

- ExpressRoute プロバイダーのサービスを含め、すべてのゲートウェイで 4 バイトの自律システム番号 (ASN) がサポートされていることを確認します。 Azure VMware Solution では、4 バイトのパブリック ASN を使用してルートをアドバタイズします。

>[!NOTE]
>Azure への既定のルート (0.0.0.0/0) をアドバタイズする場合は、既定のルートに加えて、必ずオンプレミスのネットワークを含むより具体的なルートがアドバタイズされるようにして、Azure VMware Solution への管理アクセスを有効にします。 単一の 0.0.0.0/0 ルートは、サービスが正常に動作するように、Azure VMware Solution の管理ネットワークによって破棄されます。

## <a name="create-an-expressroute-auth-key-in-the-on-premises-expressroute-circuit"></a>オンプレミスの ExpressRoute 回線で ExpressRoute 承認キーを作成する

回線所有者が承認を作成すると、承認キーが作成されます。回線ユーザーは、その承認キーを使用して、各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続します。 承認は、1 つの接続に対してのみ有効です。

> [!NOTE]
> 接続ごとに別個の認可が必要です。

1. **[ExpressRoute 回線]** ブレードの [設定] で、 **[承認]** を選択します。

1. 承認キーの名前を入力し、 **[保存]** を選択します。

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key-on-premises-expressroute.png" alt-text="[承認] を選択し、承認キーの名前を入力します。":::

   作成された新しいキーが、回線の承認キーの一覧に表示されます。

1. 承認キーと ExpressRoute ID をコピーします。 これらは、次の手順でピアリングを行う際に使用します。

## <a name="peer-private-cloud-to-on-premises"></a>プライベート クラウドからオンプレミスにピアリングする 
プライベート クラウドの ExpressRoute 回線用に承認キーを作成したので、プライベート クラウドの ExpressRoute 回線をオンプレミスの ExpressRoute 回線とピアリングすることができます。 ピアリングは、**Azure portal** でオンプレミスの ExpressRoute 回線から行われます。 プライベート クラウドの ExpressRoute 回線のリソース ID (ExpressRoute 回線 ID) と承認キーを使用してピアリングを完了します。

1. プライベート クラウドの [管理] で、 **[接続]**  >  **[ExpressRoute Global Reach]**  >  **[追加]** の順に選択します。

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Azure VMware Solution プライベート クラウドの [ExpressRoute Global Reach] タブを示すスクリーンショット。":::

1. ExpressRoute ID と、前のセクションで作成した承認キーを入力します。

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="接続情報を入力するためのダイアログを示すスクリーンショット。":::   

1. **［作成］** を選択します [オンプレミスのクラウド接続] の一覧に新しい接続が表示されます。

>[!TIP]
>**[その他]** を選択すると、一覧から接続を削除または切断できます。  
>
>:::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Azure VMware Solution でオンプレミスの接続を切断または削除する方法を示すスクリーンショット。":::


## <a name="verify-on-premises-network-connectivity"></a>オンプレミス ネットワークの接続性を検証する

この時点で、NSX-T ネットワーク セグメントと Azure VMware Solution 管理セグメントが、ExpressRoute によってどこに接続されているか、**オンプレミスのエッジ ルーター** に表示されるはずです。

>[!IMPORTANT]
>すべてのユーザーが異なる環境を使用しており、一部では、これらのルートをオンプレミス ネットワークに再び伝達できるようにする必要があります。  

## <a name="next-steps"></a>次の手順
次のチュートリアルに進み、Azure VMware Solution プライベート クラウドに VMware HCX アドオンをインストールします。

> [!div class="nextstepaction"]
> [VMware HCX のインストール](install-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
