---
title: クイックスタート - Azure portal で VM に対して Azure Automanage を有効にする
description: Azure portal で新規または既存の VM 上の仮想マシンに対して Automanage を迅速に有効にする方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648035"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>クイック スタート:Azure portal で仮想マシンに対して Azure Automanage を有効にする

Azure portal を使用して新規または既存の仮想マシンで自動管理を有効にすることにより、仮想マシンに対して Azure Automanage の使用を開始します。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> 既存の Automanage アカウントを使用して Automanage を有効にするには、VM を含むリソース グループの **共同作成者** ロールが必要です。 新しい Automanage アカウントで Automanage を有効にする場合は、次のアクセス許可が必要です: ご使用のサブスクリプションに対する **所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://aka.ms/AutomanagePortal-Ignite21) にサインインします。

## <a name="enable-automanage-for-a-single-vm"></a>単一の VM に対して Automanage を有効にする

1. 有効にする仮想マシンを参照します。

2. **[操作]** の下の目次にある **[Automanage (プレビュー)]** エントリをクリックします。

3. **[開始]** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="単一の VM を使用開始する。":::

4. 目的の Automanage 設定 (環境、ユーザー設定、Automanage アカウント) を選択し、 **[有効化]** をクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="単一の VM を有効にする。":::

## <a name="enable-automanage-for-multiple-vms"></a>複数の VM に対して Automanage を有効にする

1. 検索バーで、 **[Automanage – Azure machine best practices]\(Automanage - Azure マシンのベスト プラクティスの有効化)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="[Enable on existing VM]\(既存の VM で有効にする\)":::

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** で VM のリストにフィルターをかけます。
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


## <a name="enable-automanage-for-a-new-vm"></a>新規の VM に対して Automanage を有効にする

Azure portal の[こちら](https://aka.ms/AutomanagePortal-Ignite21)にサインインして、新しい VM を作成し、Automanage を有効にします。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。

2. Azure Marketplace リソースの一覧の上にある検索ボックスで、 使用するイメージを検索して選択し、 **[作成]** を選択します。

> [!NOTE]
> Automanage がサポートされている [Linux ディストリビューション](automanage-linux.md#supported-linux-distributions-and-versions) および [Windows Server のバージョン](automanage-windows-server.md#supported-windows-server-versions)を確認します。

3. **[基本]** タブに VM の詳細を入力します。

> [!NOTE]
> Automanage が[サポートされているリージョン](automanage-virtual-machines#supported-regions)を確認します。

4. **[管理]** タブに移動して、ご使用の **Automanage 環境** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="[管理] タブで Automanage を有効にする。":::

5. 残りの既定値はそのままにして、ページの一番下にある **[Review + create] (確認および作成)** ボタンを選択します。

6. 検証が成功したことを示すメッセージが表示されたら、 **[作成]** を選択します。

## <a name="disable-automanage-for-vms"></a>VM の Automanage を無効にする

自動管理を無効にすることで、仮想マシンに対する Azure Automanage の使用をすばやく停止します。

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

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
