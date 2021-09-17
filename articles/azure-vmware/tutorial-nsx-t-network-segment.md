---
title: チュートリアル - Azure VMware Solution にネットワーク セグメントを追加する
description: 仮想マシン (VM) に使用するネットワーク セグメントを vCenter で追加する方法について説明します。
ms.topic: tutorial
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: a678e29591bc3ca17a1ec6638e111ffeb8c264b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734202"
---
# <a name="tutorial-add-a-network-segment-in-azure-vmware-solution"></a>チュートリアル:Azure VMware Solution にネットワーク セグメントを追加する 

Azure VMware Solution をデプロイすると、NSX-T Manager または Azure portal から NSX-T ネットワーク セグメントを構成することができます。 構成が完了すると、Azure VMware Solution、NSX-T Manager、vCenter でセグメントを確認できます。 NSX-T には、**アクティブ/アクティブ** モードの NSX-T Tier-0 ゲートウェイと、**アクティブ/スタンバイ** モードの既定の NSX-T Tier-1 ゲートウェイが既定で事前プロビジョニングされています。  これらのゲートウェイを使用すると、セグメント (論理スイッチ) に接続し、East-West および North-South 接続を提供できます。 

>[!TIP]
>Azure portal には、VMware 管理者が日常的に必要とする NSX-T 操作のうち、NSX-T Manager に慣れていないユーザーを対象とするものを簡略化したビューが表示されます。 


このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * NSX-T Manager または Azure portal のいずれかを使用してネットワーク セグメントを追加する
> * 新しいネットワーク セグメントを確認する 

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager のインターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウド。 詳細については、[ネットワークの構成](tutorial-configure-networking.md)に関するチュートリアルを参照してください。

## <a name="use-nsx-t-manager-to-add-network-segment"></a>NSX-T Manager を使用してネットワーク セグメントを追加する 

vCenter に作成された仮想マシン (VM) は、NSX-T に作成されたネットワーク セグメントに配置されて、vCenter に表示されるようになります。

[!INCLUDE [add-network-segment-steps](includes/add-network-segment-steps.md)]

## <a name="use-azure-portal-to-add-an-nsx-t-segment"></a>Azure portal を使用して NSX-T セグメントを追加する

[!INCLUDE [create-nsxt-segment-azure-portal-steps](includes/create-nsxt-segment-azure-portal-steps.md)]


## <a name="verify-the-new-network-segment"></a>新しいネットワーク セグメントを確認する

新しいネットワーク セグメントが存在することを確認します。 この例では、**ls01** が新しいネットワーク セグメントです。

1. NSX-T Manager で、 **[Networking]\(ネットワーク\)**  >  **[Segments]\(セグメント\)** を選択します。 

    :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="新しいネットワーク セグメントが NSX-T に存在することの確認と状態を示すスクリーンショット。":::

1. vCenter で、 **[Networking]\(ネットワーク\)**  >  **[SDDC-Datacenter]\(SDDC データセンター\)** を選択します。

    :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="新しいネットワーク セグメントが vCenter に存在することの確認を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM に使用する NSX-T ネットワーク セグメントを vCenter で作成しました。 

次のことができるようになりました。 

- [Azure VMware Solution 用の DHCP を構成して管理する](configure-dhcp-azure-vmware-solution.md)
- [Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する](deploy-vm-content-library.md) 
- [オンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
