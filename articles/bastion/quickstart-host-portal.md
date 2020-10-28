---
title: 'クイックスタート: Azure Bastion を構成し、プライベート IP アドレスとブラウザーを使用して VM に接続する'
titleSuffix: Azure Bastion
description: このクイックスタートの記事では、仮想マシンから Azure Bastion ホストを作成し、ブラウザーを使用してプライベート IP アドレスで VM に安全に接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150487"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>クイックスタート: ブラウザーを使用してプライベート IP アドレスで VM に安全に接続する

Azure portal と Azure Bastion を使用してブラウザーで仮想マシン (VM) に接続することができます。 このクイックスタート記事では、ご利用の VM の設定に基づいて Azure Bastion を構成し、ポータルから VM に接続する方法について説明します。 この VM には、パブリック IP アドレスやクライアント ソフトウェア、エージェント、特殊な構成は必要ありません。 サービスがプロビジョニングされると、同じ仮想ネットワーク内のすべての仮想マシンで RDP/SSH エクスペリエンスを使用できるようになります。 Azure Bastion の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。

## <a name="prerequisites"></a><a name="prereq"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 ない場合は、[無料で作成](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)してください。 Bastion を使用してブラウザーで VM に接続するためには、Azure portal にサインインできる必要があります。

* 仮想ネットワーク内の Windows 仮想マシン。 VM がない場合は、[VM の作成に関するクイックスタート](../virtual-machines/windows/quick-create-portal.md)を参照して作成してください。

  * 値の例が必要な場合は、「[値の例](#values)」を参照してください。
  * 既存の仮想ネットワークがある場合は、VM の作成時に [ネットワーク] タブでそれを選択してください。
  * 仮想ネットワークがまだない場合は、VM を作成するときに一緒に作成できます。
  * この VM に Azure Bastion 経由で接続するために、パブリック IP アドレスは必要ありません。

* 必要な VM ロール:
  * 仮想マシンに対する閲覧者ロール。
  * 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール。
  
* 必要な VM ポート:
  * 受信ポート:RDP (3389)

### <a name="example-values"></a><a name="values"></a>値の例

この構成を作成する際は、次の例の値を使用できます。また、独自の値に置き換えることもできます。

**VNet と VM の基本的な値:**

|**名前** | **Value** |
| --- | --- |
| 仮想マシン| TestVM) の名前を |
| Resource group | TestRG |
| リージョン | 米国東部 |
| 仮想ネットワーク | TestVNet1 |
| アドレス空間 | 10.0.0.0/16 |
| サブネット | FrontEnd:10.0.0.0/24 |

**Azure Bastion の値:**

|**名前** | **Value** |
| --- | --- |
| 名前 | TestVNet1-bastion |
| + サブネット名 | AzureBastionSubnet |
| AzureBastionSubnet アドレス | VNet アドレス空間内のサブネット。サブネット マスクは /27 とします (例: 10.0.1.0/27)。  |
| パブリック IP アドレス |  新規作成 |
| パブリック IP アドレス名 | VNet1BastionPIP  |
| パブリック IP アドレスの SKU |  Standard  |
| 割り当て  | 静的 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion ホストの作成

bastion ホストは、いくつかの方法で構成できます。 以降の手順では、Azure portal で VM から bastion ホストを直接作成します。 VM からホストを作成すると、実際の仮想マシンや仮想ネットワークに応じて、さまざまな設定が自動的に読み込まれます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 接続先の VM に移動し、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="仮想マシンの設定" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. ドロップダウンから **[Bastion]** を選択します。
1. **[TestVM] の [接続] ページ** で、 **[Bastion を使用]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="仮想マシンの設定" border="false":::

1. **[Bastion]** ページで、次の設定フィールドに入力します。

   * **名前** :bastion ホストに名前を付けます。
   * **サブネット** :これは、Bastion リソースのデプロイ先となる仮想ネットワークのアドレス空間です。 このサブネットは **AzureBastionSubnet** という名前で作成される必要があります。 /27 かそれより大きいサブネットを使用する必要があります (/27、/26、/25 など)。
   * **[サブネット構成の管理]** を選択します。
1. **[サブネット]** ページで **[+ サブネット]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="仮想マシンの設定":::
    
1. **[サブネットの追加]** ページの **[名前]** に「 **AzureBastionSubnet** 」と入力します。
   * [サブネット アドレス範囲] には、仮想ネットワークのアドレス空間内のサブネット アドレスを選択します。
   * その他の設定は調整しないでください。 **[OK]** を選択してサブネットの変更を承諾し、保存します。

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="仮想マシンの設定":::
1. ブラウザーの [戻る] ボタンをクリックして **[Bastion]** ページに戻り、値の指定を続けます。
   * **[パブリック IP アドレス]** : **[新規作成]** のままにします。
   * **パブリック IP アドレス名** :パブリック IP アドレス リソースの名前です。
   * **割り当て** : 既定で [静的] になります。 動的割り当ては Azure Bastion に使用できません。
   * **[リソース グループ]** :VM と同じリソース グループを使用します。

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="仮想マシンの設定":::
1. **[作成]** を選択して bastion ホストを作成します。 Azure によって設定が検証され、ホストが作成されます。 ホストとそのリソースの作成およびデプロイには、約 5 分かかります。

## <a name="connect"></a><a name="connect"></a>接続

Bastion が仮想ネットワークにデプロイされると、画面が [接続] ページに変わります。

1. 仮想マシン用のユーザー名とパスワードを入力します。 次に、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="仮想マシンの設定":::
1. この仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="仮想マシンの設定":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想ネットワークと仮想マシンを使い終えたら、リソース グループとそれに含まれるすべてのリソースを削除します。

1. ポータルの上部にある **検索** ボックスに、お使いのリソース グループの名前を入力し、検索結果からそれを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に、お使いのリソース グループを入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想ネットワーク用の bastion ホストを作成し、Bastion を介して仮想マシンに安全に接続しました。 次に、仮想マシン スケール セットに接続する場合は、次のステップに進むことができます。

> [!div class="nextstepaction"]
> [Azure Bastion を使用して仮想マシン スケール セットに接続する](bastion-connect-vm-scale-set.md)
