---
title: Azure Automanage for VM でカスタム プロファイルを作成する
description: Azure Automanage でカスタム プロファイルを作成し、サービスと設定を選択する方法について説明します。
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 821ad720645c433b9a487383e5ed9edb5bc21e61
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478339"
---
# <a name="create-a-custom-profile-in-azure-automanage-for-vms"></a>Azure Automanage for VM でカスタム プロファイルを作成する

Azure Automanage for machine のベストプラクティスには、編集不可の既定のベスト プラクティス プロファイルがあります。 ただし、柔軟性を高める必要がある場合は、カスタム プロファイルを作成して、一連のサービスと設定を選択することができます。

サービスのオンとオフの切り替えがサポートされます。 現在、[Azure Backup](..\backup\backup-azure-arm-vms-prepare.md#create-a-custom-policy) と [Microsoft Antimalware](../security/fundamentals/antimalware.md#default-and-custom-antimalware-configuration) の設定のカスタマイズもサポートされます。


## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

> [!IMPORTANT]
> サブスクリプションで初めて Automanage を有効にする場合は、次の Azure RBAC アクセス許可が必要です: **所有者** ロール、または **共同作成者** ロールと **ユーザー アクセス管理者** ロールの併用。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com/) にサインインします。


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>既存の VM で VM に対して Automanage を有効にする

1. 検索バーで、 **[Automanage - Azure machine best practices]\(Automanage - Azure マシンのベスト プラクティスの有効化\)** を検索して選択します。

2. **[Enable on existing VM]\(既存の VM で有効にする\)** を選択します。

3. **[構成プロファイル]** で **[カスタム プロファイル]** を選択します。

4. ドロップダウンから既存のカスタム プロファイルを選択するか (ある場合)、 **[新規作成]** をクリックして新しいカスタム プロファイルを作成します。

5. **[新しいプロファイルの作成]** ブレードで、詳細を入力します。
    1. プロファイル名
    1. サブスクリプション
    1. リソース グループ
    1. Region

    :::image type="content" source="media\virtual-machine-custom-profile\create-custom-profile.png" alt-text="カスタム プロファイルの詳細を入力します。":::

6. 必要なサービスと設定でプロファイルを調整し、 **[作成]** をクリックします。

7. **[マシンの選択]** ブレードで次のようにします。
    1. 実際の **サブスクリプション** と **リソース グループ** で VM のリストにフィルターをかけます。
    1. オンボードする各仮想マシンのチェック ボックスをオンにします。
    1. **[選択]** ボタンをクリックします。

    :::image type="content" source="media\virtual-machine-custom-profile\existing-vm-select-machine.png" alt-text="使用可能な VM のリストから既存の VM を選択します。":::

    > [!NOTE]
    > **[Show ineligible machines]\(該当しないマシンを表示する\)** をクリックして、サポートされないマシンの一覧とその理由を確認します。 

## <a name="disable-automanage-for-vms"></a>VM の Automanage を無効にする

自動管理を無効にすることで、仮想マシンに対する Azure Automanage の使用をすばやく停止します。

:::image type="content" source="media\virtual-machine-custom-profile\disable-step-1.png" alt-text="仮想マシンでの Automanage の無効化。":::

1. 自動管理されている VM がすべて表示される **[Automanage - Azure マシンのベスト プラクティス]** ページに移動します。
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

よく寄せられる質問とその回答については、FAQ を参照してください。 

> [!div class="nextstepaction"]
> [よく寄せられる質問](faq.yml)