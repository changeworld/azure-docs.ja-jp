---
title: Azure VMware Solution をオンプレミス環境に接続する
description: Azure VMware Solution をオンプレミス環境に接続する方法について説明します。
ms.topic: tutorial
ms.date: 12/28/2020
ms.openlocfilehash: 753835b0206d8bbabe42b057fa40a2d6c4c8c414
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809685"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware Solution をオンプレミス環境に接続する

この記事では、[計画中に収集された情報](production-ready-deployment-steps.md)を引き続き使用して、Azure VMware Solution をオンプレミス環境に接続します。

開始する前に、Azure VMware Solution をオンプレミス環境に接続するための前提条件が 2 つあります。

- オンプレミス環境から Azure への ExpressRoute 回線 1 つ。
- ExpressRoute Global Reach ピアリングのための重複していない /29 ネットワーク アドレス ブロック。これは、[計画フェーズ](production-ready-deployment-steps.md)の一環として定義したものです。

>[!NOTE]
> VPN 経由での接続は可能ですが、このクイック スタート ドキュメントの範囲外です。

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute Global Reach 接続を確立する

ExpressRoute Global Reach を使用して Azure VMware Solution プライベート クラウドへのオンプレミス接続を確立するには、「[ピアオンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)」というチュートリアルに従ってください。

## <a name="verify-on-premises-network-connectivity"></a>オンプレミス ネットワークの接続性を検証する

この時点で、NSX-T ネットワーク セグメントと Azure VMware Solution 管理セグメントが、ExpressRoute によってどこに接続されているか、**オンプレミスのエッジ ルーター** に表示されるはずです。

>[!IMPORTANT]
>すべてのユーザーが異なる環境を使用しており、一部では、これらのルートをオンプレミス ネットワークに再び伝達できるようにする必要があります。  

一部の環境では、ExpressRoute 回線を保護するファイアウォールが配置されています。  ファイアウォールがなく、ルートの排除も行われない場合は、オンプレミス環境から、Azure VMware Solution の vCenter サーバーや [NSX-T セグメント上の VM](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) に対して ping を実行します。 さらに、NSX-T セグメントの VM からは、オンプレミス環境のリソースに到達できます。

## <a name="next-steps"></a>次の手順

次のセクションに進み、VMware HCX をデプロイして構成します

> [!div class="nextstepaction"]
> [VMware HCX をデプロイして構成する](tutorial-deploy-vmware-hcx.md)