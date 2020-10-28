---
title: チュートリアル - Azure VMware Solution で NSX-T ネットワーク セグメントを作成する
description: vCenter 内の VM に使用される NSX-T ネットワーク セグメントを作成する方法について説明します
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: f88f186d2af10bcc114d64920a3ac489ef7be54f
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367671"
---
# <a name="tutorial-create-an-nsx-t-network-segment-in-azure-vmware-solution"></a>チュートリアル:Azure VMware Solution で NSX-T ネットワーク セグメントを作成する

vCenter に作成された仮想マシン (VM) は、NSX-T に作成されたネットワーク セグメントに配置されて、vCenter に表示されるようになります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * NSX-T Manager を操作してネットワーク セグメントを追加する
> * 新しいネットワーク セグメントを追加する
> * vCenter で新しいネットワーク セグメントを監視する

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager の管理インターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウドが必要です。 詳細については、[ネットワークの構成](tutorial-configure-networking.md)に関するチュートリアルを参照してください。

## <a name="provision-a-network-segment-in-nsx-t"></a>NSX-T でネットワーク セグメントをプロビジョニングする

1. プライベート クラウドの vCenter で、 **[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\)** の順に選択します。まだネットワークがないことに注目してください。

   :::image type="content" source="media/nsxt/vcenter-without-ls01.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. プライベート クラウドの NSX-T Manager で、 **[Networking]\(ネットワーク\)** を選択します。

   :::image type="content" source="media/nsxt/nsxt-network-overview.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. **[Segments]\(セグメント\)** を選択します。

   :::image type="content" source="media/nsxt/nsxt-select-segments.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. NSX-T のセグメントの概要ページで、 **[ADD SEGMENT]\(セグメントの追加\)** を選択します。 プライベート クラウドのプロビジョニングの一環として 3 つのセグメントが作成されますが、VM には使用できません。  そのために新しいネットワーク セグメントを追加する必要があります。

   :::image type="content" source="media/nsxt/nsxt-segments-overview.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. セグメントに名前を付け、 **[Connected Gateway]\(接続されているゲートウェイ\)** に、事前構成済みの Tier1 ゲートウェイ (TNTxx-T1) を選択します。 **[Type]\(種類\)** は [Flexible]\(柔軟\) のままにし、事前構成済みのオーバーレイ **トランスポート ゾーン** (TNTxx-OVERLAY-TZ) を選択して、[Set Subnets]\(サブネットの設定\) を選択します。 このセクションのその他すべての設定および **[PORTS]\(ポート\)** と **[SEGMENT PROFILES]\(セグメント プロファイル\)** は既定の設定のままでかまいません。

   :::image type="content" source="media/nsxt/nsxt-create-segment-specs.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. 新しいセグメントのゲートウェイの IP アドレスを設定し、 **[ADD]\(追加\)** を選択します。 使用する IP アドレスは、重複しない RFC1918 アドレス ブロック上に存在する必要があります。これで新しいセグメント上の VM に確実に接続することができます。

   :::image type="content" source="media/nsxt/nsxt-create-segment-gateway.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. **[APPLY]\(適用\)** を選択して新しいネットワーク セグメントを適用し、 **[SAVE]\(保存\)** を使用して構成を保存します。

   :::image type="content" source="media/nsxt/nsxt-create-segment-apply.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

   :::image type="content" source="media/nsxt/nsxt-create-segment-save.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. これで新しいネットワーク セグメントが作成されました。ここで、 **[NO]\(いいえ\)** を選択して、セグメントの構成を続行するのを辞退します。

   :::image type="content" source="media/nsxt/nsxt-create-segment-continue-no.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. NSX-T に新しいネットワーク セグメントが存在することを確認します。これには、 **[Networking]\(ネットワーク\) > [Segments]\(セグメント\)** を選択し、新しいセグメント (この場合は "ls01") が一覧に表示されていることを確認してください。

   :::image type="content" source="media/nsxt/nsxt-new-segment-overview-2.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

1. 新しいネットワーク セグメントが vCenter に存在することを確認します。これには、 **[Networking]\(ネットワーク\) > [SDDC-Datacenter]\(SDDC データセンター\)** を選択し、新しいセグメント (この場合は "ls01") が一覧に表示されていることを確認してください。

   :::image type="content" source="media/nsxt/vcenter-with-ls01-2.png" alt-text="プライベート クラウドの vCenter で、[SDDC-Datacenter]\(SDDC データセンター\) > [Networks]\(ネットワーク\) の順に選択します。まだネットワークがないことに注目してください。":::

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、vCenter 内の VM に使用される NSX-T ネットワーク セグメントを作成しました。 これで、[Azure VMware Solution に VM をデプロイするためのコンテンツ ライブラリを作成](deploy-vm-content-library.md)できるようになりました。 このチュートリアルで作成したネットワークに VM をプロビジョニングすることもできます。

できない場合は、Azure VMware Solution のプライベート クラウドに対して ExpressRoute Global Reach ピアリングを作成する方法に関する次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [オンプレミス環境をプライベート クラウドにピアリングする](tutorial-expressroute-global-reach-private-cloud.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
