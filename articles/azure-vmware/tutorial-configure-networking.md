---
title: チュートリアル - Azure で VMware プライベート クラウド用のネットワークを構成する
description: Azure にプライベート クラウドをデプロイするために必要なネットワークを作成して構成する方法について説明します
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 5a8086f78f465f52d0f9107932c09c4690f505e8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873865"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>チュートリアル:Azure で VMware プライベート クラウド用のネットワークを構成する

Azure VMware Solution (AVS) プライベート クラウドには、仮想ネットワークが必要です。 AVS ではプレビュー期間中はオンプレミスの vCenter がサポートされないため、ご使用のオンプレミス環境と統合するための追加の手順が必要になります。 ExpressRoute 回線と仮想ネットワーク ゲートウェイの設定も必要になるため、このチュートリアルではこれについても説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 仮想ネットワークを作成します
> * 仮想ネットワーク ゲートウェイを作成する
> * ExpressRoute 回線をゲートウェイに接続する
> * vCenter および NSX Manager の URL を見つける

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[前のチュートリアル](tutorial-create-private-cloud.md)で作成したリソース グループに移動し、 **[+ 追加]** を選択して新しいリソースを定義します。

**[Marketplace を検索]** テキスト ボックスに「**仮想ネットワーク**」と入力します。 仮想ネットワーク リソースを見つけて選択します。

[仮想ネットワーク] ページで、 **[作成]** を選択して、プライベート クラウド用の仮想ネットワークを設定します。

**[仮想ネットワークの作成]** ページで、仮想ネットワークの関連する詳細を入力します。次の表に、各プロパティの説明を示します。

> [!IMPORTANT]
> 前のチュートリアルでプライベート クラウドを作成したときに使用したアドレス空間と重複**しない**アドレス空間を使用する必要があります。

**[基本]** タブで、仮想ネットワークの名前を入力し、適切なリージョンを選択して、 **[次へ: IP アドレス]** を選択します

**[IP アドレス]** タブの **[IPv4 アドレス空間]** に、前のチュートリアルで作成したアドレス空間を入力します。

**[+ サブネットの追加]** を選択し、 **[サブネットの追加]** ページで、サブネットに名前と適切なアドレス範囲を指定します。 完了したら、 **[追加]** を選択します。

**[確認と作成]** を選択します

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="仮想ネットワークを作成する" border="true":::

情報を確認し、 **[作成]** を選択します。 デプロイが完了すると、リソース グループに仮想ネットワークが表示されます。

## <a name="create-a-virtual-network-gateway"></a>仮想ネットワーク ゲートウェイを作成する

前のセクションで仮想ネットワークを作成したので、次は仮想ネットワーク ゲートウェイを作成します。

リソース グループで **[+ 追加]** を選択して、新しいリソースを追加します。

**[Marketplace を検索]** テキスト ボックスに「**仮想ネットワーク ゲートウェイ**」と入力します。 仮想ネットワーク リソースを見つけて選択します。

**[仮想ネットワーク ゲートウェイ]** ページで、 **[作成]** を選択します。

**[仮想ネットワーク ゲートウェイの作成]** ページの [基本] タブで、各フィールドの値を指定します。 次の表に、各フィールドの説明を示します。

| フィールド | 値 |
| --- | --- |
| **サブスクリプション** | この値には、リソース グループが属しているサブスクリプションが既に設定されています。 |
| **リソース グループ** | この値は、現在のリソース グループに対して既に設定されています。 これは、前のテストで作成したリソース グループである必要があります。 |
| **名前** | 仮想ネットワーク ゲートウェイの一意の名前を入力します。 |
| **リージョン** | 仮想ネットワーク ゲートウェイの地理的な場所を選択します。 |
| **ゲートウェイの種類** | **[ExpressRoute]** を選択します。 |
| **VPN の種類** | **[ルート ベース]** を選択します。 |
| **SKU** | 既定値 **[標準]** のままにします。 |
| **Virtual Network** | 前に作成した仮想ネットワークを選択します。 仮想ネットワークが表示されない場合は、ゲートウェイのリージョンが仮想ネットワークのリージョンと一致していることを確認してください。 |
| **ゲートウェイ サブネットのアドレス範囲** | この値は、仮想ネットワークを選択したときに設定されます。 既定値を変更しないでください。 |
| **パブリック IP アドレス** | **[新規作成]** を選択します。 |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="ゲートウェイを作成する" border="true":::

**[確認および作成]** を選択して、次のページで詳細が正しいことを確認し、 **[作成]** を選択して仮想ネットワーク ゲートウェイのデプロイを開始します。 デプロイが完了したら、このチュートリアルの次のセクションに進み、ExpressRoute 回線をプライベート クラウドを含む仮想ネットワークに接続します。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>ExpressRoute を仮想ネットワーク ゲートウェイに接続する

このセクションでは、AVS プライベート クラウドと、作成した仮想ネットワーク ゲートウェイの間の接続を追加する方法について説明します。

前のチュートリアルで作成したプライベート クラウドに移動し、 **[管理]** の下の **[接続]** を選択して、 **[ExpressRoute]** タブを選択します。

承認キーをコピーします。 承認キーがない場合は作成する必要があります。これを行うには、 **[+ Request an authorization key]\(+ 承認キーの要求\)** を選択します

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="承認キーを要求する" border="true":::

前の手順で作成した仮想ネットワーク ゲートウェイに移動し、 **[設定]** の下の **[接続]** を選択します。 **[接続]** ページで、 **[+ 追加]** を選択します。

**[接続の追加]** ページで、各フィールドの値を指定します。 次の表に、各フィールドの説明を示します。

| フィールド | 値 |
| --- | --- |
| **名前**  | 接続名を入力します。  |
| **接続の種類**  | **[ExpressRoute]** を選択します。  |
| **承認の利用**  | このボックスが選択されていることを確認します。  |
| **仮想ネットワーク ゲートウェイ** | 前に作成した仮想ネットワーク ゲートウェイ  |
| **承認キー**  | リソース グループの [ExpressRoute] タブで承認キーをコピーし、貼り付けます。 |
| **ピア回線の URI**  | リソース グループの [ExpressRoute] タブで ExpressRoute ID をコピーし、貼り付けます。  |

**[OK]** を選択します。 これにより、ExpressRoute 回線と仮想ネットワークの間の接続が作成されます。

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="接続を追加する" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>vCenter および NSX Manager の URL を見つける

vCenter と NSX Manager にサインインするには、vCenter Web クライアントと NSX-T Manager サイトの URL が必要です。 URL を検索するには、次のようにします。

AVS プライベート クラウドに移動し、 **[管理]** の下の **[ID]** を選択します。ここで必要な情報を確認できます。

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="vCenter の URL を見つける" border="true":::

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 仮想ネットワークを作成します
> * 仮想ネットワーク ゲートウェイを作成する
> * ExpressRoute 回線をゲートウェイに接続する
> * vCenter および NSX Manager の URL を見つける

次のチュートリアルに進み、プライベート クラウドをローカルで管理できるように、環境への接続に使用するジャンプ ボックスを作成する方法を学習します。

> [!div class="nextstepaction"]
> [プライベート クラウドへのアクセス](tutorial-access-private-cloud.md)
