---
title: 'チュートリアル:Azure Bastion ホストを作成する:Windows VM: ポータル'
description: Azure Bastion ホストを作成し、Windows VM に接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d2e071fd7dc368df867b17a53f9e994ee5c16a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430973"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>チュートリアル:Bastion を構成し、Windows VM に接続する

このチュートリアルでは、Azure Bastion と Azure portal を使用して、ブラウザーを介して仮想マシンに接続する方法について説明します。 このチュートリアルでは、Azure portal を使用して、Bastion を仮想ネットワークにデプロイします。 サービスがプロビジョニングされると、同じ仮想ネットワーク内のすべての仮想マシンで RDP/SSH エクスペリエンスを使用できるようになります。 Bastion を使用して接続する場合、VM にはパブリック IP アドレスまたは特別なソフトウェアは必要ありません。 Bastion をデプロイした後、他のものに必要でない場合は、VM からパブリック IP アドレスを削除できます。 次に、Azure portal を使用して、そのプライベート IP アドレスを介して VM に接続します。 Azure Bastion の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VNet の Bastion ホストを作成する。
> * 仮想マシンからパブリック IP アドレスを削除する。
> * Windows 仮想マシンに接続する。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* [仮想ネットワーク](../virtual-network/quick-create-portal.md)。
* 仮想ネットワーク内の Windows 仮想マシン。 VM がない場合は、[VM の作成に関するクイックスタート](../virtual-machines/windows/quick-create-portal.md)を参照して作成してください。
* 次は、リソースに必要なロールです。
   * 必要な VM ロール:
     * 仮想マシンに対する閲覧者ロール。
     * 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。

* ポート: Windows VM に接続するには、お使いの Windows VM で次のポートを開いておく必要があります。
  * 受信ポート:RDP (3389)

 >[!NOTE]
 >現時点では、Azure プライベート DNS ゾーンでの Azure Bastion の使用はサポートされていません。 開始する前に、Bastion リソースをデプロイする予定の仮想ネットワークが、プライベート DNS ゾーンにリンクされていないことを確認してください。
 >

### <a name="example-values"></a><a name="values"></a>値の例

この構成を作成する際は、次の例の値を使用できます。また、独自の値に置き換えることもできます。

**VNet と VM の基本的な値:**

|**名前** | **Value** |
| --- | --- |
| 仮想マシン| TestVM) の名前を |
| Resource group | TestRG1 |
| リージョン | 米国東部 |
| 仮想ネットワーク | VNet1 |
| アドレス空間 | 10.1.0.0/16 |
| サブネット | FrontEnd: 10.1.0.0/24 |

**Azure Bastion の値:**

|**名前** | **Value** |
| --- | --- |
| 名前 | VNet1-bastion |
| + サブネット名 | AzureBastionSubnet |
| AzureBastionSubnet アドレス | サブネット マスクが /26 以上の VNet アドレス空間内のサブネット。<br> 例: 10.1.1.0/26。  |
| 価格レベルまたは SKU | Standard |
| インスタンス数 (ホスト スケーリング)| 3 以上 |
| パブリック IP アドレス |  新規作成 |
| パブリック IP アドレス名 | VNet1-ip  |
| パブリック IP アドレスの SKU |  Standard  |
| 割り当て  | 静的 |

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Bastion ホストの作成

このセクションでは、VNet で Bastion オブジェクトを作成する方法について説明します。 これは、VNet 内の VM に対するセキュリティで保護された接続を作成するために必要です。

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
1. 検索で「**Bastion**」と入力します。
1. [サービス] の **[Bastion]** をクリックします。
1. [Bastion] ページで、 **[+ 作成]** をクリックして **[Bastion の作成]** ページを開きます。
1. **[Bastion の作成]** ページで、新しい Bastion リソースを構成します。

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="[Bastion の作成] ポータル ページのスクリーンショット。" lightbox="./media/tutorial-create-host-portal/create.png":::

### <a name="project-details"></a>プロジェクトの詳細

* **サブスクリプション**: 使用する Azure サブスクリプションです。

* **リソース グループ**:新しい Bastion リソースが作成される Azure リソース グループです。 既存のリソース グループがない場合は、新しいものを作成できます。

### <a name="instance-details"></a>インスタンスの詳細

* **名前**: 新しい Bastion リソースの名前です。

* **リージョン**: リソースが作成される Azure パブリック リージョンです。 仮想ネットワークが存在するリージョンを選択します。

* **価格レベル:** 価格レベルは **SKU** とも呼ばれます。 このチュートリアルでは、ドロップダウンから **Standard** SKU を選択します。 Standard SKU を選択すると、ホスト スケーリングのインスタンス数を構成できます。 Basic SKU では、ホストのスケーリングはサポートされていません。 詳細については、[構成設定 - SKU](configuration-settings.md#skus) に関するページを参照してください。

* **インスタンス数:** これは、**ホスト スケーリングの設定** で、スケール ユニットの増分で構成されます。 スライダーを使用して、インスタンス数を構成します。 Basic レベルの SKU を指定した場合、この設定を構成することはできません。 詳細については、[構成設定 - ホスト スケーリング](configuration-settings.md#instance)に関するページを参照してください。 このチュートリアルでは、スケール ユニットの [価格](https://azure.microsoft.com/pricing/details/azure-bastion) に関する考慮事項に留意しながら、希望するインスタンス数を選択できます。

### <a name="configure-virtual-networks"></a>仮想ネットワークを構成する

* **仮想ネットワーク**:Bastion リソースが作成される仮想ネットワークです。 このプロセス中にポータルで新しい仮想ネットワークを作成したり、既存の仮想ネットワークを使用したりすることができます。 既存の仮想ネットワークを使用している場合、Bastion サブネットの要件を反映するために、既存の仮想ネットワークに十分な空きアドレス空間があることを確認してくます。 ドロップダウンに仮想ネットワークが表示されない場合は、適切なリソースグループを選択していることを確認してください。

* **サブネット**: 仮想ネットワークを作成または選択すると、ページに [サブネット] フィールドが表示されます。 これは、Bastion インスタンスをデプロイするサブネットです。 名前は **AzureBastionSubnet** にしてください。 サブネットを追加するには、次の手順を参照してください。

#### <a name="manage-subnet-configuration"></a>サブネット構成の管理

ほとんどの場合、AzureBastionSubnet はまだ構成されていません。 Bastion サブネットを構成するには、次のようにします。 

1. **[サブネット構成の管理]** を選択します。 これにより、 **[サブネット]** ページが開きます。

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="サブネット構成の管理のスクリーンショット。":::
1. **[サブネット]** ページで **[+ サブネット]** を選択して **[サブネットの追加]** ページを開きます。 

1. 次のガイドラインに従ってサブネットを作成します。

   * サブネットの名前は「**AzureBastionSubnet**」とする必要があります。
   * サブネットは、少なくとも /26 以上である必要があります。 Standard SKU では、今後追加のホスト スケーリング インスタンスに対応するために、/26 以上をお勧めします。

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="AzureBastionSubnet サブネットのスクリーンショット。":::

1. このページの追加のフィールドに入力する必要はありません。 ページ下部の **[保存]** を選択して設定を保存し、 **[サブネットの追加]** ページを閉じます。

1. **[サブネット]** ページの上部にある **[Bastion の作成]** を選択して、Bastion の構成ページに戻ります。

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="[Bastion の作成] のスクリーンショット。":::

### <a name="public-ip-address"></a>パブリック IP アドレス

RDP/SSH が (ポート 443 経由で) アクセスされる Bastion リソースのパブリック IP アドレスです。 **新しいパブリック IP アドレス** を作成します。 パブリック IP アドレスは、作成している Bastion リソースと同じリージョン内にある必要があります。 この IP アドレスは、接続先の VM とは何の関係もありません。 Bastion ホスト リソースのパブリック IP アドレスです。

   * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。 このチュートリアルでは、既定値のままにしておいてかまいません。
   * **パブリック IP アドレスの SKU**: この設定は既定で **[標準]** に事前入力されます。 Azure Bastion では、標準パブリック IP SKU のみが使用およびサポートされます。
   * **割り当て**: この設定は既定で **[静的]** に事前入力されます。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. 設定の指定が完了したら、 **[確認および作成]** を選択します。 これにより、値が検証されます。 検証に合格したら、Bastion リソースを作成できます。
1. 設定を確認します。 
1. ページの下部にある **[作成]** を選択します。
1. デプロイが進行中であることを知らせるメッセージが表示されます。 リソースが作成されたときに、状態がこのページに表示されます。 Bastion リソースを作成してデプロイするには、約 5 分かかります。

## <a name="remove-vm-public-ip-address"></a>VM のパブリック IP アドレスを削除する

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>VM への接続

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用してリソースを削除します。

1. ポータルの上部にある **検索** ボックスにリソース グループの名前を入力します。 検索結果にそのリソース グループが表示されたら、選択します。
1. **[リソース グループの削除]** を選択します。
1. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** にそのリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Bastion ホストを作成し、それを仮想ネットワークに関連付けました。 その後、VM からパブリック IP アドレスを削除したうえで、その VM に接続しました。 Azure Bastion サブネットでネットワーク セキュリティ グループを使用することを選択できます。 これを行うには、次を参照してください。

> [!div class="nextstepaction"]
> [NSG を使用する](bastion-nsg.md)
