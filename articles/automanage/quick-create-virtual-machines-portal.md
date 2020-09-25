---
title: クイックスタート - Azure portal で VM に対して Azure Automanage を有効にする
description: Azure portal で新規または既存の VM 上の仮想マシンに対して Automanage を迅速に有効にする方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 43e0805a828a2ed32d23984b6ffef95a7e710953
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945406"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>クイック スタート:Azure portal で仮想マシンに対して Azure Automanage を有効にする

Azure portal を使用して新規または既存の仮想マシンで自動管理を有効にすることにより、仮想マシンに対して Azure Automanage の使用を開始します。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> Automanage を有効にするには、次の RBAC アクセス許可が必要です。**所有者**ロール、または**共同作成者**ロールと**ユーザー アクセス管理者**ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>既存の VM で VM に対して Automanage を有効にする

1. 検索バーで、 **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティスの有効化)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="[Enable on existing VM]\(既存の VM で有効にする\)":::

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の**サブスクリプション**と**リソース グループ**で VM のリストにフィルターをかけます。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

4. **[構成プロファイル]** で、 **[Browse and change profiles and preferences]\(プロファイルと基本設定を参照して変更\)** をクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="[Browse and change profiles and preferences]\(プロファイルと基本設定を参照して変更\)":::

5. **[Select configuration profile + preferences]\(構成プロファイルと基本設定の選択\)** ブレードで次のようにします。
    1. 左側でプロファイルを選択します。"*Dev/Test*" はテスト用、"*運用*" は運用環境用です。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="運用環境の構成プロファイルを参照します。":::

6. **[有効]** ボタンをクリックします。


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>新規の VM で VM に対して Automanage を有効にする

1. [Azure portal で Windows VM を作成するチュートリアル](..\virtual-machines\windows\quick-create-portal.md)の作成手順に従います。

2. VM がデプロイされると、デプロイ ステータスのページが表示され、下部には推奨される **[次の手順]** が表示されます。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="デプロイ ページの下部にある [次の手順] セクション。":::

3. **[次の手順]** で、 **[Enable Automanage virtual machine best practices]\(仮想マシンの Automanage のベスト プラクティスを有効にする\)** を選択します。

4. **[Automanage – Azure virtual machine best practices]\(Automanage – Azure 仮想マシンのベストプラクティスの有効化\)** ページの **[マシン]** は、新しく作成された VM によって自動的に設定されます。

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="新しく作成された VM は、選択したマシンとして表示されます。":::

5. **[構成プロファイル]** で、 **[Browse and change profiles and preferences]\(プロファイルと基本設定を参照して変更\)** をクリックします。

6. **[Select configuration profile + preferences]\(構成プロファイルと基本設定の選択\)** ブレードで次のようにします。
    1. 左側でプロファイルを選択します。"*Dev/Test*" はテスト用、"*運用*" は運用環境用です。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="運用環境の構成プロファイルを参照します。":::

7. **[有効]** ボタンをクリックします。


## <a name="disable-automanage-for-vms"></a>VM の Automanage を無効にする

自動管理を無効にすることで、仮想マシンに対する Azure Automanage の使用をすばやく停止します。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="仮想マシンで Automanage を無効にします。":::

1. 自動管理されている VM がすべて表示されている **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティス)** ページに移動します。
1. 無効にする仮想マシンの横にあるチェック ボックスをオンにします。
1. **[Disable automanagent]\(自動管理の無効化\)** ボタンをクリックします。
1. 結果として表示されたポップアップのメッセージをよく読んでから、 **[Disable]\(無効\)** に同意します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

新しいリソース グループを作成して、仮想マシンに対して Azure Automanage を試し、不要になった場合は、そのリソース グループを削除できます。 グループを削除すると、そのリソース グループ内の VM およびすべてのリソースも削除されます。

Azure Automanage では、リソースを格納するための既定のリソース グループが作成されます。 すべてのリソースをクリーンアップするために "DefaultResourceGroupRegionName" および "AzureBackupRGRegionName" という名前付け規則を使用しているリソース グループを確認します。

1. **[リソース グループ]** を選択します。
1. リソース グループのページで、 **[削除]** を選択します。
1. プロンプトが表示されたら、リソース グループ名を確認し、 **[削除]** を選択します。


## <a name="next-steps"></a>次の手順

このクイックスタートでは、VM に対して Azure Automanage を有効にしました。 

仮想マシンで Automanage を有効にする際に、カスタマイズした基本設定を作成して適用する方法を確認してください。 

> [!div class="nextstepaction"]
> [VM の Azure Automanage - カスタム構成プロファイル](virtual-machines-custom-preferences.md)
