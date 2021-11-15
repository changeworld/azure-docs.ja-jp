---
title: 'クイックスタート: VM 設定から Bastion を構成する'
titleSuffix: Azure Bastion
description: 仮想マシン設定から Azure Bastion ホストを作成し、ブラウザーを使用してプライベート IP アドレスで VM に安全に接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 243b8a068c5301c5545c2afe8567ae1fbf71284c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426717"
---
# <a name="quickstart-configure-azure-bastion-from-vm-settings"></a>クイックスタート: VM 設定から Azure Bastion を構成する

このクイックスタートの記事では、Azure portal で VM の設定に基づいて Azure Bastion を構成し、プライベート IP アドレスを使用して VM に接続する方法について説明します。 サービスがプロビジョニングされると、同じ仮想ネットワーク内のすべての仮想マシンで RDP/SSH エクスペリエンスを使用できるようになります。 この VM には、パブリック IP アドレスやクライアント ソフトウェア、エージェント、特殊な構成は必要ありません。 VM ではパブリック IP アドレスを他のことで必要としない場合、削除してかまいません。 次に、プライベート IP アドレスを利用してポータルから VM に接続します。 Azure Bastion の詳細については、「[Azure Bastion とは](bastion-overview.md)」を参照してください。 

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
| パブリック IP アドレス |  新規作成 |
| パブリック IP アドレス名 | VNet1-ip  |
| パブリック IP アドレスの SKU |  Standard  |
| 割り当て  | 静的 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion ホストの作成

bastion ホストは、いくつかの方法で構成できます。 以降の手順では、Azure portal で VM から bastion ホストを直接作成します。 VM からホストを作成すると、実際の仮想マシンや仮想ネットワークに応じて、さまざまな設定が自動的に読み込まれます。

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
1. 接続先の VM に移動し、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/vm-connect.png" alt-text="仮想マシンの設定のスクリーンショット。" lightbox="./media/quickstart-host-portal/vm-connect.png":::
1. ドロップダウンから **[Bastion]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/bastion.png" alt-text="ドロップダウンの [Bastion] のスクリーンショット。" lightbox="./media/quickstart-host-portal/bastion.png":::
1. **[TestVM] の [接続] ページ** で、 **[Bastion を使用]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="[Bastion を使用する] のスクリーンショット。":::

1. **[Azure Bastion を使用して接続する]** ページの **手順 1** では、VM から直接 bastion ホストを作成しているため、値が事前に設定されています。

   :::image type="content" source="./media/quickstart-host-portal/create-step-1.png" alt-text="手順 1 で事前設定した設定のスクリーンショット。" lightbox="./media/quickstart-host-portal/create-step-1.png":::

1. **[Azure Bastion を使用して接続する]** ページの **手順 2** で、値を構成します。 AzureBastionSubnet アドレス空間には、推奨されるアドレス空間があらかじめ設定されています。 AzureBastionSubnet には、/26 以上 (/25、/24 など) のアドレス空間を指定する必要があります。 ホストのスケーリングが制限されないように、/26 を使用することをお勧めします。 この設定の構成が完了したら、 **[サブネットの作成]** をクリックして AzureBastionSubnet を作成します。

     :::image type="content" source="./media/quickstart-host-portal/create-subnet.png" alt-text="Bastion サブネットの作成のスクリーンショット。":::

1. サブネットが作成されたら、ページは自動的に **手順 3** に進みます。 手順 3 では、次の値を使用します。

   * **[名前]** : bastion ホストに名前を付けます。
   * **価格レベル:** 価格レベルは SKU です。 この演習では、ドロップダウンから **Standard** を選択します。 Standard SKU を選択すると、ホスト スケーリングのインスタンス数を構成できます。 Basic SKU では、ホストのスケーリングはサポートされていません。 詳細については、[構成設定 - SKU](configuration-settings.md#skus) に関するページを参照してください。
   * **インスタンス数:** これは、ホスト スケーリングの設定です。 スライダーを使用して構成します。 Basic レベルの SKU を指定した場合、インスタンスは 2 つに制限され、この設定を構成することはできません。 詳細については、[構成設定 - ホスト スケーリング](configuration-settings.md#instance)に関するページを参照してください。 インスタンス数は、Standard SKU に依存します。 このクイックスタートでは、スケール ユニットの [価格](https://azure.microsoft.com/pricing/details/azure-bastion) に関する考慮事項に留意しながら、希望するインスタンス数を選択できます。
   * **[パブリック IP アドレス]**: **[新規作成]** を選択します｡
   * **[パブリック IP アドレス名]** : パブリック IP アドレス リソースの名前です。
   * **[パブリック IP アドレスの SKU]** : **[Standard]** に事前構成されています。
   * **[割り当て]** : **[静的]** に事前構成されています。 動的割り当ては Azure Bastion に使用できません。
   * **[リソース グループ]** : VM と同じリソース グループ。

   :::image type="content" source="./media/quickstart-host-portal/create-step-3.png" alt-text="手順 3 のスクリーンショット。":::
1. 値の入力が完了したら、 **[Create Azure Bastion using defaults]\(既定値を使用して Azure Bastion を作成する\)** を選択します。 Azure によって設定が検証され、ホストが作成されます。 ホストとそのリソースの作成およびデプロイには、約 5 分かかります。

## <a name="remove-vm-public-ip-address"></a><a name="remove"></a>VM のパブリック IP アドレスを削除する

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a><a name="connect"></a>VM への接続

Bastion が仮想ネットワークにデプロイされると、画面が [接続] ページに変わります。

1. 仮想マシン用のユーザー名とパスワードを入力します。 次に、 **[接続]** を選択します。

   :::image type="content" source="./media/quickstart-host-portal/connect.png" alt-text="スクリーンショットは、[Azure Bastion を使用して接続する] ダイアログを示しています。":::
1. Bastion を使用したこの仮想マシンへの RDP 接続は、ポート 443 と Bastion サービスを使用して (HTML5 を介して) Azure portal で直接開きます。 クリップボードへのアクセス許可を求めるメッセージが表示された場合は、 **[許可]** をクリックします。 これにより、画面の左側にあるリモート クリップボードの矢印を使用できます。

   * 接続したとき、VM のデスクトップの外観は、サンプルのスクリーンショットとは違うものになることがあります。 
   * VM に接続しているとき、キーボード ショートカット キーを使用すると、ローカル コンピューターのショートカット キーとは同じ動作にならないことがあります。 たとえば、Windows クライアントから Windows VM に接続しているとき、CTRL + ALT + END は、ローカル コンピューターで CTRL + ALT + Delete のキーボード ショートカットになります。 Windows VM に接続しているとき、Mac からこれを行うには、キーボード ショートカットは Fn + CTRL + ALT + Backspace になります。

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="RDP 接続":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想ネットワークと仮想マシンを使い終えたら、リソース グループとそれに含まれるすべてのリソースを削除します。

1. ポータルの上部にある **検索** ボックスに、お使いのリソース グループの名前を入力し、検索結果からそれを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[リソース グループ名を入力してください]** に、お使いのリソース グループを入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、仮想ネットワーク用の bastion ホストを作成し、Bastion を介して仮想マシンに安全に接続しました。 次に、仮想マシン スケール セットに接続する場合は、次のステップに進むことができます。

> [!div class="nextstepaction"]
> [Azure Bastion を使用して仮想マシン スケール セットに接続する](bastion-connect-vm-scale-set.md)
