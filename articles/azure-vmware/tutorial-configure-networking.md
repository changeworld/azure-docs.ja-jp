---
title: チュートリアル - Azure で VMware プライベート クラウド用のネットワークを構成する
description: Azure にプライベート クラウドをデプロイするために必要なネットワークを作成して構成する方法について説明します
ms.topic: tutorial
ms.custom: contperf-fy21q4
ms.date: 04/23/2021
ms.openlocfilehash: 13f74c6120bd78127a2989218b3d535989bb7861
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945688"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>チュートリアル:Azure で VMware プライベート クラウド用のネットワークを構成する

Azure VMware Solution プライベート クラウドには、Azure Virtual Network が必要です。 Azure VMware Solution ではオンプレミスの vCenter はサポートされていないため、オンプレミス環境と統合するための追加の手順が必要になります。 また、ExpressRoute 回線と仮想ネットワーク ゲートウェイの設定も必要です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * 仮想ネットワーク ゲートウェイの作成
> * ExpressRoute 回線をゲートウェイに接続する


## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. [Azure portal](https://portal.azure.com) にサインインします。

1. [プライベート クラウドの作成チュートリアル](tutorial-create-private-cloud.md)で作成したリソース グループに移動し、 **[+ 追加]** を選択して新しいリソースを定義します。 

1. **[Marketplace を検索]** テキスト ボックスに「**仮想ネットワーク**」と入力します。 仮想ネットワーク リソースを見つけて選択します。

1. **[仮想ネットワーク]** ページで、 **[作成]** を選択して、プライベート クラウド用の仮想ネットワークを設定します。

1. **[仮想ネットワークの作成]** ページで、仮想ネットワークの詳細を入力します。

1. **[基本]** タブで、仮想ネットワークの名前を入力し、適切なリージョンを選択して、 **[次へ: IP アドレス]** を選択します。

1. **[IP アドレス]** タブの **[IPv4 アドレス空間]** に、前のチュートリアルで作成したアドレス空間を入力します。

   > [!IMPORTANT]
   > 前のチュートリアルでプライベート クラウドを作成したときに使用したアドレス空間と重複 **しない** アドレス空間を使用する必要があります。

1. **[+ サブネットの追加]** を選択し、 **[サブネットの追加]** ページで、サブネットに名前と適切なアドレス範囲を指定します。 完了したら、 **[追加]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="[確認と作成] を選択します。" border="true":::

1. 情報を確認し、 **[作成]** を選択します。 デプロイが完了すると、リソース グループに仮想ネットワークが表示されます。

## <a name="create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイの作成

仮想ネットワークを作成したので、次は仮想ネットワーク ゲートウェイを作成します。

1. リソース グループで **[+ 追加]** を選択して、新しいリソースを追加します。

1. **[Marketplace を検索]** テキスト ボックスに「**仮想ネットワーク ゲートウェイ**」と入力します。 仮想ネットワーク リソースを見つけて選択します。

1. **[仮想ネットワーク ゲートウェイ]** ページで、 **[作成]** を選択します。

1. **[仮想ネットワーク ゲートウェイの作成]** ページの [基本] タブで、各フィールドの値を指定し、 **[確認と作成]** を選択します。 

   | フィールド | 値 |
   | --- | --- |
   | **サブスクリプション** | 値として、リソース グループが属しているサブスクリプションが事前に設定されています。 |
   | **リソース グループ** | 値として、現在のリソース グループが事前に設定されています。 値は、前のテストで作成したリソース グループである必要があります。 |
   | **名前** | 仮想ネットワーク ゲートウェイの一意の名前を入力します。 |
   | **リージョン** | 仮想ネットワーク ゲートウェイの地理的な場所を選択します。 |
   | **ゲートウェイの種類** | **[ExpressRoute]** を選択します。 |
   | **SKU** | 既定値 **[標準]** のままにします。 |
   | **Virtual Network** | 前に作成した仮想ネットワークを選択します。 仮想ネットワークが表示されない場合は、ゲートウェイのリージョンが仮想ネットワークのリージョンと一致していることを確認してください。 |
   | **ゲートウェイ サブネットのアドレス範囲** | この値は、仮想ネットワークを選択したときに設定されます。 既定値を変更しないでください。 |
   | **パブリック IP アドレス** | **[新規作成]** を選択します。 |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="各フィールドに値を入力し、[確認と作成] を選択します。" border="true":::

1. 詳細が正しいことを確認し、 **[作成]** を選択して仮想ネットワーク ゲートウェイのデプロイを開始します。 
1. デプロイが完了したら、次のセクションに進み、ExpressRoute 回線を Azure VMware Solution プライベート クラウドを含む仮想ネットワーク ゲートウェイに接続します。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute を仮想ネットワーク ゲートウェイに接続する

仮想ネットワーク ゲートウェイをデプロイしたので、それと Azure VMware Solution プライベート クラウドの間の接続を追加します。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * 仮想ネットワーク ゲートウェイの作成
> * ExpressRoute 回線をゲートウェイに接続する


次のチュートリアルに進み、vCenter の VM に使用される NSX-T ネットワーク セグメントを作成する方法を学習します。

> [!div class="nextstepaction"]
> [NSX-T ネットワーク セグメントの作成](tutorial-nsx-t-network-segment.md)