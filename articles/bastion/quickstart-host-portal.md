---
title: 'クイックスタート: VM から Bastion ホストを作成し、プライベート IP アドレスを使用して接続する'
titleSuffix: Azure Bastion
description: このクイックスタートの記事では、仮想マシンから Azure Bastion ホストを作成し、プライベート IP アドレスを使用して安全に接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019054"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>クイック スタート:プライベート IP アドレスと Azure Bastion を使用して仮想マシンに接続する

このクイックスタートの記事では、Azure Bastion と Azure portal を使用して、ブラウザーを介して仮想マシンに接続する方法について説明します。 Azure portal では、Azure VM から仮想ネットワークに bastion をデプロイできます。 bastion をデプロイしたら、Azure portal を使用して、そのプライベート IP アドレスを介して VM に接続できます。 VM には、パブリック IP アドレスや特別なソフトウェアは必要ありません。 VNet の bastion ホストを VM から直接作成する利点の 1 つは、設定の多くが事前に設定されることです。

サービスがプロビジョニングされると、同じ仮想ネットワーク内のすべての仮想マシンで RDP/SSH エクスペリエンスを使用できるようになります。 Azure Bastion の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

## <a name="prerequisites"></a><a name="prereq"></a>前提条件

* 仮想ネットワーク。
* 仮想ネットワーク内の Windows 仮想マシン。
* 次の必須ロール:
  * 仮想マシンに対する閲覧者ロール。
  * 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。

* ポート:VM に接続するには、VM で次のポートを開いておく必要があります。
  * 受信ポート:RDP (3389)

### <a name="example-values"></a>値の例

|**名前** | **Value** |
| --- | --- |
| 名前 |  TestVNet1-bastion |
| 仮想ネットワーク |  TestVNet1 (VM に基づく) |
| + サブネット名 | AzureBastionSubnet |
| AzureBastionSubnet アドレス |  10.1.254.0/27 |
| パブリック IP アドレス |  新規作成 |
| パブリック IP アドレス名 | VNet1BastionPIP  |
| パブリック IP アドレスの SKU |  Standard  |
| 割り当て  | 静的 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion ホストの作成

Azure portal で既存の仮想マシンを使用して bastion ホストを作成すると、お使いの仮想マシンまたは仮想ネットワークに対応する値が、さまざまな設定に既定で自動的に入力されます。

1. [Azure Portal](https://portal.azure.com)を開きます。 お使いの仮想マシンに移動し、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="仮想マシンの設定" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. ドロップダウンから **[Bastion]** を選択します。
1. **[TestVM] の [接続] ページ**で、 **[Bastion を使用]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="仮想マシンの設定" border="false":::

1. **[Bastion]** ページで、次の設定フィールドに入力します。

   * **名前**:bastion ホストに名前を付けます。
   * **サブネット**:Bastion リソースがデプロイされる仮想ネットワーク内のサブネットです。 このサブネットは **AzureBastionSubnet** という名前で作成される必要があります。 この名前により、Bastion リソースをデプロイするサブネットが Azure で認識されます。 これは、ゲートウェイ サブネットとは異なります。 /27 かそれより大きいサブネットを使用する必要があります (/27、/26、/25 など)。
   
      * **[サブネット構成の管理]** を選択します。
      * **AzureBastionSubnet** を選択します。
      * 必要に応じて、アドレス範囲を CIDR 表記で調整します。 (例: 10.1.254.0/27)。
      * その他の設定は調整しないでください。 サブネットの変更を受け入れて保存するには **[OK]** を選択します。変更を加えない場合は、ページの上部にある **[x]** を選択します。
1. ブラウザーの [戻る] ボタンをクリックして **[Bastion]** ページに戻り、値の指定を続けます。
   * **パブリック IP アドレス名**:パブリック IP アドレス リソースの名前です。
   * **[パブリック IP アドレス]** : これは、RDP/SSH が (ポート 443 経由で) アクセスされる Bastion リソースのパブリック IP です。 新しいパブリック IP を作成します。
1. **[作成]** を選択して bastion ホストを作成します。 Azure によって設定が検証され、ホストが作成されます。 ホストとそのリソースの作成およびデプロイには、約 5 分かかります。

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="仮想マシンの設定":::

## <a name="connect"></a><a name="connect"></a>接続

Bastion が仮想ネットワークにデプロイされると、画面が [接続] ページに変わります。

1. 仮想マシン用のユーザー名とパスワードを入力します。 次に、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="仮想マシンの設定":::
1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="仮想マシンの設定":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想ネットワークと仮想マシンを使い終えたら、リソース グループとそれに含まれるすべてのリソースを削除します。

1. ポータルの上部にある**検索**ボックスに、お使いのリソース グループの名前を入力し、検索結果からそれを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に、お使いのリソース グループを入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想ネットワーク用の Bastion ホストを作成し、その Bastion ホストを介して安全に仮想マシンに接続しました。 次に、仮想マシン スケール セットに接続する場合は、次のステップに進むことができます。

> [!div class="nextstepaction"]
> [Azure Bastion を使用して仮想マシン スケール セットに接続する](bastion-connect-vm-scale-set.md)
