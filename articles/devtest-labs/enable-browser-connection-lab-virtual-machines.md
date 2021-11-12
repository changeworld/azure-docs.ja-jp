---
title: Azure DevTest Labs 仮想マシンへのブラウザー接続を有効にする
description: Azure Bastion を DevTest Labs と統合して、ブラウザー経由でラボ仮想マシン (VM) にアクセスできます。
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 876a72548c70f3e7717c75973edee802e584fca2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442982"
---
# <a name="enable-browser-connection-to-devtest-labs-vms"></a>DevTest Labs VM へのブラウザー接続を有効にする

Azure DevTest Labs は [Azure Bastion](../bastion/index.yml) と統合することにより、ブラウザーを使用してラボ仮想マシン (VM) に接続できます。 ラボ所有者は、Azure Bastion を介してすべてのラボ VM に対するブラウザー アクセスを有効にできます。

Azure Bastion は、セキュリティで保護されたシームレスなリモート デスクトップ プロトコル (RDP) と、トランスポート層セキュリティ (TLS) 経由のセキュア シェル (SSH) 接続を、Azure portal から直接提供します。 ブラウザーを介してラボ VM に接続するために、他のクライアント、エージェント、またはソフトウェアは必要ありません。 VM にパブリック IP アドレスは必要ありません。

この記事では、DevTest Labs VM への Azure Bastion ブラウザー接続を有効にする 2 つの異なる方法について説明します。

- ラボとその VM 用に、新しい Azure Bastion 対応仮想ネットワークを作成できます。
- 既存のラボ仮想ネットワークに Azure Bastion をデプロイできます。

## <a name="prerequisites"></a>前提条件

- DevTest Labs のラボがあるか、[ラボを作成します。](tutorial-create-custom-lab.md#create-a-lab)
- Azure Bastion ブラウザー アクセスを使用するには、ラボ ユーザーは、Azure Bastion ホストと、Azure Bastion が構成されたラボ仮想ネットワークに **閲覧者** ロールがある必要があります。

## <a name="option-1-connect-a-lab-to-an-azure-bastion-enabled-virtual-network"></a>オプション 1: Azure Bastion 対応の仮想ネットワークにラボを接続する

まず、Azure Bastion サブネットを持つ新しい仮想ネットワークを作成し、もう 1 つのサブネットをそのネットワークに作成します。 Azure Bastion サブネットでは、その中に Azure Bastion 以外のリソースを作成できないので、ラボ VM を作成するには、別のサブネットが必要です。

1. Azure portal で、 **[仮想ネットワーク]** を検索して選択します。
1. **[仮想ネットワーク]** ページの上部にある **[作成]** を選択します。
1. **[仮想ネットワークの作成]** 画面で、新しい仮想ネットワークの **[名前]** を入力し、ラボと同じ **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を選択します。
1. **次へ:[次へ: IP アドレス]** を選択します。
1. **[IP アドレス]** タブに、既に 1 つのサブネット、**default** があります。 **[サブネットの追加]** を選択します。
1. **[サブネットの追加]** ペインで、「*AzureBastionSubnet*」と **[名前]** の下に入力します。
1. **[サブネットのアドレス範囲]** に、仮想ネットワークのアドレス空間内にあるが、既定のサブネットと重複しないアドレス範囲を入力します。 必要に応じて、 **[仮想ネットワークの作成]** ページの空白のフィールドに新しいアドレス空間を追加できます。
1. **[追加]** を選択します。

   ![AzureBastionSubnet サブネットの作成を示すスクリーンショット。](media/enable-browser-connection-lab-virtual-machines/create-subnet.png)

1. **[確認および作成]** を選択し、検証に成功したら **[作成]** を選択します。
1. 新しい仮想ネットワークが作成されたら、そのページに移動し、左側のナビゲーションで **[サブネット]** を選択し、2 つのサブネット (**既定**) と **AzureBastionSubnet** があることを確認します。

   ![Azure Bastion 仮想ネットワーク内の 2 つのサブネットのスクリーンショット。](media/enable-browser-connection-lab-virtual-machines/second-subnet.png)

次に、ラボを新しい仮想ネットワークに接続します。

1. ラボの **[概要]** ページで、左側のナビゲーションから **[構成とポリシー]** を選択します。
1. **[構成とポリシー]** ページで、左側のナビゲーションの **[外部リソース]** の下にある **[仮想ネットワーク]** を選択します。
1. **[構成とポリシー] の [仮想ネットワーク]** ページで、上部にある **[追加]** を選択します。
1. **[仮想ネットワーク]** ページで、 **[仮想ネットワークの選択]** を選択します。
1. **[仮想ネットワークの選択]** ページで、作成した Azure Bastion 対応仮想ネットワークを選択します。
1. **[仮想ネットワーク]** ページの最上部で **[保存]** を選択します。
1. **[構成とポリシー] の [仮想ネットワーク]** ページで、ラボから以前の仮想ネットワークを削除します。 仮想ネットワークの横の **[...]** を選択し、 **[削除]** を選択し、 **[はい]** を選択します。 

   ![古いラボ仮想ネットワークの削除を示すスクリーンショット。](media/enable-browser-connection-lab-virtual-machines/add-virtual-network.png)

Azure Bastion 以外のサブネットで VM の作成を有効にします。

1. **[構成とポリシー] の [仮想ネットワーク]** ページで、Azure Bastion 対応仮想ネットワークを選択します。
1. **[仮想ネットワーク]** ページに、**AzureBastionSubnet** サブネットと **デフォルト** サブネットの両方が表示されることを確認します。 両方のサブネットが表示しない場合は、ページを閉じてから再度開きます。
1. **既定の** サブネットを選択します。
1. **[ラボ サブネット]** 画面の **[仮想マシンの作成時に使用]** で、 **[はい]** を選択し、 **[保存]** を選択します。 これで、ラボ仮想ネットワークの既定のサブネットに VM を作成できます。

   ![既定のサブネットで V M の作成を有効にする方法を示すスクリーンショット。](./media/enable-browser-connection-lab-virtual-machines/enable-vm-creation-subnet.png)

## <a name="option-2-deploy-azure-bastion-in-a-labs-existing-virtual-network"></a>オプション 2: ラボの既存の仮想ネットワークに Azure Bastion をデプロイする

まず、ラボの既存の仮想ネットワークに、新しい Azure Bastion サブネットを作成します。

1. Azure portal で、 **[仮想ネットワーク]** を検索して選択します。
1. **[仮想ネットワーク]** ページの一覧からラボの既存の仮想ネットワークを選択します。
1. 仮想ネットワーク ページで、左側のナビゲーションで **[サブネット]** を選択します。
1. **[サブネット]** ページで、トップ メニューの **[+ サブネット]** を選択します。
1. **[サブネットの追加] 画面** で、「*AzureBastionSubnet*」と **[名前]** の下に入力します。
1. **[サブネットのアドレス範囲]** に、仮想ネットワークのアドレス空間内にあるが、既存のラボ サブネットと重複しないアドレス範囲を入力します。
   >[!TIP]
   >このダイアログをキャンセルし、仮想ネットワークの左側のナビゲーションで **[アドレス空間]** を選択し、サブネットの新しいアドレス空間を作成する必要がある場合があります。
1. **[保存]** を選択します。

   ![既存の仮想ネットワークにサブネットを追加する方法を示すスクリーンショット。](./media/enable-browser-connection-lab-virtual-machines/add-subnet.png)

次に、新しい Azure Bastion サブネット内に新しい Azure Bastion ホストをデプロイします。

1. Azure portal で、**[要塞]** を検索し、選択します。
1. **[Bastions]** ページで、上部にある **[+ 作成]** を選択します。
1. **[Bastion の作成]** ページで、 **[名前]** を入力し、ラボと同じ **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を選択します。
1. **[仮想ネットワーク]** で、ドロップダウン リストからラボの仮想ネットワークを選択し、 **[サブネット]** で **[AzureBastionSubnet]** が選択されていないことを確認します。
1. **[確認および作成]** を選択し、検証に成功したら **[作成]** を選択します。

   ![既存の仮想ネットワークへの Azure Bastion のデプロイを示すスクリーンショット。](./media/enable-browser-connection-lab-virtual-machines/create-bastion.png)

## <a name="connect-to-lab-vms-through-azure-bastion"></a>Azure Bastion を使用してラボ VM に接続する

ラボ仮想ネットワークに Azure Bastion をデプロイしたら、ラボのブラウザー接続を有効にしてください。

1. ラボの **[概要]** ページで、 **[構成とポリシー]** を選択し、 **[ブラウザー接続]** を **[設定]** で選択します。
1. **[ブラウザー接続]** ページで、 **[オン]** を選択します。
1. ページの最上部で **[保存]** を選択します。

   ![ブラウザー接続の有効化を示すスクリーンショット。](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

Azure Bastion を介してラボ VM に接続するには、次の手順を実行します。

1. ラボの **[概要]** ページの **[自分の仮想マシン]** からラボ VM を選択します。
1. VM のページの上部にある **[ブラウザー接続]** を選択します。
1. **[ブラウザー接続]** ペインに、VM のユーザー名とパスワードを入力して、 **[接続]** を選択します。

## <a name="next-steps"></a>次の手順
- [Azure Bastion とは](../bastion/bastion-overview.md)
- [VM をラボに追加する](devtest-lab-add-vm.md)
