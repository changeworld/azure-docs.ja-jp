---
title: クイックスタート - Azure portal で VM に対して Azure Automanage を有効にする
description: Azure portal で新規または既存の VM 上の仮想マシンに対して Automanage を迅速に有効にする方法について説明します。
author: ju-shim
ms.author: jushiman
ms.date: 02/17/2021
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.custom: mode-portal
ms.openlocfilehash: ab9a6f5ff85fe0b5b2d1df4d2a3f64bb4cb21207
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465699"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>クイック スタート:Azure portal で仮想マシンに対して Azure Automanage を有効にする

Azure portal を使用して新規または既存の仮想マシンで自動管理を有効にすることにより、仮想マシンに対して Azure Automanage の使用を開始します。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> Automanage を有効にするには、VM を含むリソース グループの **共同作成者** ロールが必要です。 サブスクリプションで初めて Automanage を有効にする場合は、次のアクセス許可が必要です: サブスクリプションでの **所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure ポータル](https://portal.azure.com)

## <a name="enable-automanage-on-existing-machines"></a>既存のマシンで Automanage を有効にする

1. 検索バーで、 **[Automanage – Azure machine best practices]\(Automanage - Azure マシンのベスト プラクティスの有効化)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="[Enable on existing VM]\(既存の VM で有効にする\)":::

4. **[構成プロファイル]** で、プロファイルの種類として、 **[Azure のベスト プラクティス - 運用]** 、 **[Azure のベストプラクティス - Dev/Test]** 、または [ **[カスタム プロファイル]** ](virtual-machines-custom-profile.md) を選択します。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="環境を選択します。":::

   環境の相違点を確認するには、 **[Azure のベスト プラクティスのプロファイルを表示]** をクリックします。
    1. ドロップダウンで環境を選択します。テストの場合は *[Dev/Test]* 、運用環境の場合は *[運用]* 。
    1. **[OK]** をクリックします。

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="運用環境を参照する。":::

5. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** でリストをフィルター処理します。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。
    > [!NOTE]
    > Azure VM と Azure Arc 対応サーバーの両方を選択できます。

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::


6. **[有効]** ボタンをクリックします。


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

仮想マシンで Automanage を有効にする際に、カスタマイズしたプロファイルを作成して適用する方法を確認してください。

> [!div class="nextstepaction"]
> [Azure Automanage カスタム プロファイル](virtual-machines-custom-profile.md)
