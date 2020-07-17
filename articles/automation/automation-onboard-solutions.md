---
title: Update、Change Tracking、Inventory ソリューションの Azure Automation への配布準備をする
description: Update と Change Tracking ソリューションの Azure Automation への配布準備をする方法について説明します。
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457622"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Update、Change Tracking、Inventory ソリューションの Azure Automation への配布準備をする

このチュートリアルでは、VM から Azure Automation への Update、Change Tracking、および Inventory ソリューションの配布準備を自動的に行う方法について説明します。

> [!div class="checklist"]
> * Azure VM の配布準備
> * ソリューションの有効化
> * モジュールのインストールと更新
> * オンボード Runbook のインポート
> * Runbook の起動

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する方法](automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="onboard-an-azure-vm"></a>Azure VM の配布準備

マシンをオンボードする方法は複数あり、[仮想マシンから](automation-onboard-solutions-from-vm.md)、[複数のマシンの参照から](automation-onboard-solutions-from-browse.md)、[お使いの Automation アカウントから](automation-onboard-solutions-from-automation-account.md)、または Runbook を使用してソリューションをオンボードできます。 このチュートリアルでは、Runbook を使用して Update Management を有効にする方法について説明します。 さらに大きな規模で Azure Virtual Machines の配布準備を行うには、Change Tracking または Update Management ソリューションで既存の VM の配布準備をする必要があります。 この手順では、Update Management および Change Tracking で仮想マシンの配布準備を行います。

### <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

Change Tracking と Inventory ソリューションを使用すると、仮想マシンで[変更を追跡](automation-vm-change-tracking.md)し、[インベントリを作成](automation-vm-inventory.md)することができます。 この手順では、仮想マシンでソリューションを有効にします。

1. Azure portal で **[Automation アカウント]** を選択し、一覧からご自身の Automation アカウントを選択します。
1. **[構成管理]** で **[Inventory]** を選択します。
1. 既存の Log Analytics ワークスペースを選択するか、新規作成します。 
1. **[有効化]** をクリックします。

    ![Update ソリューションの配布準備](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Update Management の有効化

Update Management ソリューションでは、Azure Windows VM の更新プログラムとパッチを管理できます。 利用可能な更新プログラムの状態を迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。 この手順では、VM のソリューションを有効にします。

1. Automation アカウントの **[Update Management]** セクションで **[Update Management]** を選択します。
1. 選択されている Log Analytics ワークスペースは、前の手順で使用されたワークスペースです。 **[有効]** をクリックして、Update Management ソリューションの配布準備を行います。 Update Management ソリューションのインストール中、青いバナーが表示されます。 

    ![Update ソリューションの配布準備](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>管理する Azure VM を選択します

ソリューションが有効になったら、そのソリューションに、配布準備を行う Azure VM を追加できます。

1. Automation アカウントから、 **[構成管理]** の **[Change tracking]\(変更履歴\)** を選択します。 
2. [Change tracking]\(変更履歴\) ページで、 **[Azure VM の追加]** をクリックして VM を追加します。

3. 一覧から VM を選択し、 **[有効]** をクリックします。 このアクションにより、VM に対して Change Tracking と Inventory ソリューションが有効になります。

   ![VM の Update ソリューションの有効化](media/automation-onboard-solutions/enable-change-tracking.png)

4. VM オンボード通知が完了したら、 **[更新の管理]** で **[Update Management]** を選択します。

5. **[Azure VM の追加]** を選択して VM を追加します。

6. 一覧から VM を選択し、 **[有効]** を選択します。 このアクションにより、VM に対して Update Management ソリューションが有効になります。

   ![VM の Update ソリューションの有効化](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> 他のソリューションが完了するのを待たずに、次のソリューションを有効にしようとすると、"`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`" というメッセージが表示されます。

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

ソリューションの配布準備を適切に自動化するには、最新の Azure モジュールに更新し、[Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) モジュールをインポートする必要があります。

## <a name="update-azure-modules"></a>Azure モジュールの更新

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。 
2. **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。 
3. **[はい]** を選択して、既存の Azure モジュールすべてを最新バージョンに更新します。

![モジュールの更新](media/automation-onboard-solutions/update-modules.png) A

### <a name="install-azoperationalinsights-module"></a>Az.OperationalInsights モジュールのインストール

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。 
2. **[ギャラリーを参照]** を選択して、モジュール ギャラリーを開きます。 
3. Az.OperationalInsights を検索し、そのモジュールを Automation アカウントにインポートします。

![OperationalInsights モジュールのインポート](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>オンボード Runbook のインポート

1. お使いの Automation アカウントから、 **[プロセスの自動化]** の **[Runbook]** を選択します。
1. **[ギャラリーの参照]** を選択します。
1. `update and change tracking` を検索します。
3. Runbook を選択し、[ソースの表示] ページの **[インポート]** をクリックします。 
4. **[OK]** をクリックして、その Runbook を Automation アカウントにインポートします。

   ![オンボード Runbook のインポート](media/automation-onboard-solutions/import-from-gallery.png)

6. [Runbook] ページで、 **[編集]** をクリックし、 **[発行]** を選択します。 
7. [Runbook の発行] ペインで **[はい]** をクリックして Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

この Runbook を開始するには、Change Tracking または Update ソリューションの Azure VM への配布準備を完了している必要があります。 パラメーターのために、ソリューションが配布準備された既存の仮想マシンとリソース グループが必要です。

1. **Enable-MultipleSolution** Runbook を開きます。

   ![複数のソリューション Runbook](media/automation-onboard-solutions/runbook-overview.png)

1. [開始] ボタンをクリックし、パラメーターに次の値を入力します。

   * **VMNAME** - 空のままにします。 Update または Change Tracking ソリューションに配布準備する既存の VM 名。 この値を空のままにすると、リソース グループの VM すべての配布準備が行われます。
   * **VMRESOURCEGROUP** - VM の配布準備が行われるリソース グループの名前。
   * **SUBSCRIPTIONID** - 空のままにします。 配布準備が行われる新しい VM のサブスクリプション ID。 空のままにすると、ワークスペースのサブスクリプションが使用されます。 別のサブスクリプション ID を指定すると、この Automation アカウントの RunAs アカウントも、このサブスクリプションの共同作成者として追加されます。
   * **ALREADYONBOARDEDVM** - Updates または ChangeTracking ソリューションに手動で配布準備された VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM が属しているリソース グループの名前。
   * **SOLUTIONTYPE** - 「**Updates**」または「**ChangeTracking**」と入力します。

   ![Enable-MultipleSolution Runbook パラメーター](media/automation-onboard-solutions/runbook-parameters.png)

1. **[OK]** を選択して、Runbook ジョブを開始します。
1. Runbook ジョブ ページで進行状況とエラーを監視します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Update Management から VM を削除するには:

1. Log Analytics ワークスペースで、スコープ構成 `MicrosoftDefaultScopeConfig-Updates` の保存された検索条件から VM を削除します。 保存された検索条件は、ワークスペース内の **[全般]** にあります。
2. [Windows 用の Log Analytics エージェント](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources)または [Linux 用 Log Analytics エージェント](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)を削除します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure 仮想マシンの配布準備を手動で行う。
> * 必要な Azure モジュールをインストールして更新する。
> * Azure VM の配布準備を行う Runbook をインポートする。
> * Azure VM の配布準備を自動的に行う Runbook を開始する。

Runbook のスケジュール設定の詳細については、このリンクに従ってください。

> [!div class="nextstepaction"]
> [Runbook のスケジュールを設定する](automation-schedules.md)。
