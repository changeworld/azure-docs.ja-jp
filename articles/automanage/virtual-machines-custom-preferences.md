---
title: Azure Automanage for VMs でカスタム設定を作成する
description: Azure Automanage for VMs で構成プロファイルを調整し、独自の基本設定を指定する方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 377677c9e5e81487059241db68baff639a3de033
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715043"
---
# <a name="create-a-custom-preference-in-azure-automanage-for-vms"></a>Azure Automanage for VMs でカスタム設定を作成する

Azure Automanage for virtual machines のベスト プラクティスには、必要に応じて調整できる既定の構成プロファイルがあります。 この記事では、新規または既存の VM で自動管理を有効にするときに、独自の構成プロファイルの基本設定を指定する方法について説明します。

現在、[Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) と [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration) のカスタマイズをサポートしています。


> [!NOTE]
> Automanage が有効になっている間は、VM の構成プロファイルまたは設定を変更できません。 その VM の Automanage を無効にしてから、必要な構成プロファイルと基本設定を使用して Automanage を再度有効にする必要があります。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> Automanage を有効にするには、次の Azure RBAC アクセス許可が必要です。**所有者**ロール、または**共同作成者**ロールと**ユーザー アクセス管理者**ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>既存の VM で Automanage for VMs を有効にする

1. 検索バーで、 **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティスの有効化)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

3. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の**サブスクリプション**と**リソース グループ**で VM のリストにフィルターをかけます。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

4. **[構成プロファイル]** で、 **[Browse and change profiles and preferences]\(プロファイルと基本設定を参照して変更\)** をクリックします。

    :::image type="content" source="media\virtual-machine-custom-preferences\existing-vm-quick-create.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

5. **[Select configuration profile + preferences]\(構成プロファイルと基本設定の選択)** ブレードで、左側のプロファイルを選択します。 *[Dev/Test]* はテスト用、 *[運用]* は運用環境用です。

    :::image type="content" source="media\virtual-machine-custom-preferences\browse-production-profile.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

6. 選択したプロファイルの **[Configuration preferences]\(構成の基本設定\)** の下に、特定のサービスに合わせて調整できるドロップダウンがあります。
    1. **[Create new preferences]\(新しい基本設定の作成\)** をクリックします。
    1. **[Create a configuration preference]\(構成の基本設定の作成\)** ブレードで、[基本] タブに入力します。
        1. サブスクリプション
        1. Resource group
        1. Preference name (基本設定の名前)
        1. Region

    :::image type="content" source="media\virtual-machine-custom-preferences\create-preference.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

7. [基本設定] タブに移動し、構成の基本設定を必要に応じて調整します。
        
    > [!NOTE]
    > プロファイルの構成を変更する場合は、ベスト プラクティスの上限と下限の範囲内に収まる調整のみが許可されます。

8. 構成プロファイルを確認します。
9. **[作成]** ボタンをクリックします。

10. **[有効]** ボタンをクリックします。


## <a name="disable-automanage-for-vms"></a>Automanage for VMs を無効にする

Automanage を無効にして、Azure Automanage for virtual machines の使用をすばやく停止します。

:::image type="content" source="media\virtual-machine-custom-preferences\disable-step-1.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

1. 自動管理されている VM がすべて表示される **[Automanage – Azure virtual machine best practices]\(Automanage - Azure 仮想マシンのベスト プラクティス\)** ページに移動します。
1. 無効にする仮想マシンの横にあるチェックボックスをオンにします。
1. **[Disable automanagent]\(自動管理の無効化\)** ボタンをクリックします。
1. 結果として表示されたポップアップのメッセージをよく読んでから、 **[Disable]\(無効\)** に同意します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

新しいリソース グループを作成して、Azure Automanage for virtual machines を試し、不要になった場合は、そのリソース グループを削除できます。 グループを削除すると、そのリソース グループ内の VM およびすべてのリソースも削除されます。

Azure Automanage では、リソースを格納するための既定のリソース グループが作成されます。 すべてのリソースをクリーンアップするために "DefaultResourceGroupRegionName" および "AzureBackupRGRegionName" という名前付け規則を使用しているリソース グループを確認します。

1. **[リソース グループ]** を選択します。
1. リソース グループのページで、 **[削除]** を選択します。
1. プロンプトが表示されたら、リソース グループ名を確認し、 **[削除]** を選択します。


## <a name="next-steps"></a>次の手順 

よく寄せられる質問とその回答については、FAQ を参照してください。 

> [!div class="nextstepaction"]
> [よく寄せられる質問](faq.md)