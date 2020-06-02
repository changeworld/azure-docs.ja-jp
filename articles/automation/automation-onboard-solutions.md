---
title: Runbook から Azure Automation の Update Management を有効にする
description: この記事では、Runbook から Update Management を有効にする方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 39ebdb6937b03d72365e9d3785af9571ebb66186
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836058"
---
# <a name="enable-update-management-from-a-runbook"></a>Runbook から Update Management を有効にする

この記事では、Runbook を使用して、お客様の環境内の VM に対して [Update Management](automation-update-management.md) 機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、Update Management を使用して既存の VM を有効にする必要があります。 

> [!NOTE]
> Update Management を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](automation-offering-get-started.md)。
* [仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="enable-update-management"></a>Update Management の有効化

1. Automation アカウントの **[更新の管理]** で **[Update Management]** を選択します。

2. Log Analytics ワークスペースを選択し、 **[有効にする]** をクリックします。 Update Management が有効化されている間、青いバナーが表示されます。 

    ![Update Management の有効化](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>管理する Azure VM の選択

Update Management が有効になると、更新プログラムを受け取る Azure VM を追加できます。

1. Automation アカウントから、 **[更新の管理]** で **[Update Management]** を選択します。

2. **[Azure VM の追加]** を選択して VM を追加します。

3. 一覧から VM を選択し、 **[有効にする]** をクリックして VM の更新を設定します。 

   ![VM の Update Management の有効化](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Update Management の設定が完了する前に別の機能を有効にしようとすると、`Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.` というメッセージが表示されます。

## <a name="install-and-update-modules"></a>モジュールのインストールと更新

VM の Update Management を正常に有効にするには、最新の Azure モジュールに更新し、[Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0) モジュールをインポートする必要があります。

1. Automation アカウントで、 **[共有リソース]** の **[モジュール]** を選択します。 
2. **[Azure モジュールの更新]** を選択して、Azure モジュールを最新バージョンに更新します。 
3. **[はい]** を選択して、既存の Azure モジュールすべてを最新バージョンに更新します。

    ![モジュールの更新](media/automation-onboard-solutions/update-modules.png)

4. **[共有リソース]** の下にある **[モジュール]** に戻ります。 
5. **[ギャラリーの参照]** を選択して、モジュール ギャラリーを開きます。 
6. `Az.OperationalInsights` を検索し、このモジュールを Automation アカウントにインポートします。

    ![OperationalInsights モジュールのインポート](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Update Management を有効にする Runbook のインポート

1. お使いの Automation アカウントから、 **[プロセスの自動化]** の **[Runbook]** を選択します。
2. **[ギャラリーの参照]** を選択します。
3. `update and change tracking` を検索します。
4. Runbook を選択し、[ソースの表示] ページの **[インポート]** をクリックします。 
5. **[OK]** をクリックして、その Runbook を Automation アカウントにインポートします。

   ![設定用の Runbook のインポート](media/automation-onboard-solutions/import-from-gallery.png)

6. [Runbook] ページで、 **[編集]** をクリックし、 **[発行]** を選択します。 
7. [Runbook の発行] ペインで **[はい]** をクリックして Runbook を発行します。

## <a name="start-the-runbook"></a>Runbook の起動

Azure VM でこの Runbook を起動するには、Update Management を有効にしておく必要があります。 この機能がパラメーターで有効にされている既存の VM とリソース グループが必要です。

1. **Enable-MultipleSolution** Runbook を開きます。

   ![複数のソリューション Runbook](media/automation-onboard-solutions/runbook-overview.png)

2. [開始] ボタンをクリックし、次のフィールドにパラメーター値を入力します。

   * **VMNAME** - Update Management に追加する既存の VM の名前。 リソース グループ内のすべての VM を追加するには、このフィールドを空白のままにします。
   * **VMRESOURCEGROUP** - VM を有効にするリソース グループの名前。
   * **SUBSCRIPTIONID** - 有効にする新しい VM のサブスクリプション ID。 ワークスペースのサブスクリプションを使用するには、このフィールドを空白のままにします。 別のサブスクリプション ID を使用する場合は、ご自分の Automation アカウントの実行アカウントをサブスクリプションの共同作成者として追加してください。
   * **ALREADYONBOARDEDVM** - 既に更新が手動で有効にされている VM の名前。
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - VM が属しているリソース グループの名前。
   * **SOLUTIONTYPE** - 「**Updates**」と入力します。

   ![Enable-MultipleSolution Runbook パラメーター](media/automation-onboard-solutions/runbook-parameters.png)

3. **[OK]** を選択して、Runbook ジョブを開始します。
4. Runbook ジョブ ページで進行状況とエラーを監視します。

## <a name="next-steps"></a>次のステップ

* Runbook のスケジュールを設定するには、「[Azure Automation のスケジュールを管理する](shared-resources/schedules.md)」を参照してください。
* VM の Update Management を使用するには、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」を参照してください。
* スコープ構成については、「[スコープ構成を使用する](automation-scope-configurations-update-management.md)」を参照してください。
* Log Analytics ワークスペースが不要になった場合は、「[Update Management の Automation アカウントからワークスペースのリンクを解除する](automation-unlink-workspace-update-management.md)」の手順を参考にしてください。
* Update Management から VM を削除するには、「[Update Management から VM を削除する](automation-remove-vms-from-update-management.md)」を参照してください。
* Update Management の一般的なエラーのトラブルシューティングについては、「[Update Management ソリューションに関する問題のトラブルシューティング](troubleshoot/update-management.md)」を参照してください。
* Windows Update エージェントに関する問題のトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題のトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。