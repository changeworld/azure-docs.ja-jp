---
title: "Update と Change Tracking ソリューションの Azure Automation への配布準備をする | Microsoft Docs"
description: "Update と Change Tracking ソリューションの Azure Automation への配布準備をする方法について説明します。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Update と Change Tracking ソリューションの Azure Automation への配布準備をする

このチュートリアルでは、VM から Azure Automation への Update、Change Tracking、および Inventory ソリューションの配布準備を自動的に行う方法について説明します。

> [!div class="checklist"]
> * Azure 仮想マシンの配布準備を手動で行う。
> * 必要な Azure モジュールをインストールして更新する。
> * Azure VM の配布準備を行う Runbook をインポートする。
> * Azure VM の配布準備を自動的に行う Runbook を開始する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。
+ として機能します。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
+ マシンを管理する [Automation アカウント](automation-offering-get-started.md)。

## <a name="onboard-an-azure-virtual-machine-manually"></a>Azure 仮想マシンの配布準備を手動で行う

1.  Automation アカウントを開きます。
2.  [インベントリ] ページをクリックします。
![Inventory ソリューションの配布準備](media/automation-onboard-solutions/inventory-onboard.png)
3.  既存の Log Analytics ワークスペースを選択するか、新規に作成します。 [有効] ボタンをクリックします。
4.  Change Tracking と Inventory ソリューションの配布準備通知が完了したら、[更新の管理] ページをクリックします。
![Update ソリューションの配布準備](media/automation-onboard-solutions/update-onboard.png)
4.  Update ソリューションの配布準備を行う [有効] ボタンをクリックします。
5.  Update ソリューションの配布準備通知が完了したら、[変更追跡] ページをクリックします。
![Change Tracking の配布準備](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  [Azure VM の追加] ボタンをクリックします。
![変更追跡対象の VM を選択する](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Azure VM を選択し、[有効] ボタンを押して Change Tracking と Inventory ソリューションの配布準備をします。
8.  VM の配布準備通知が完了したら、[更新の管理] ページをクリックします。
![VM の更新の管理のための配布準備](media/automation-onboard-solutions/update-onboard-vm.png)
9.  [Azure VM の追加] ボタンをクリックします。
![更新の管理対象の VM を選択する](media/automation-onboard-solutions/enable-update.png)
10.  Azure VM を選択し、[有効] ボタンを押して Update Management ソリューションの配布準備をします。

## <a name="install-and-update-required-azure-modules"></a>必要な Azure モジュールをインストールして更新する

ソリューションの配布準備を適切に自動化するには、最新の Azure モジュールに更新し、AzureRM.OperationalInsights をインポートする必要があります。
1.  [モジュール] ページをクリックします。
![モジュールの更新](media/automation-onboard-solutions/update-modules.png)
2.  [Azure モジュールの更新] ボタンをクリックして最新バージョンに更新します。 更新が完了するまで待ちます。
3.  [ギャラリーを参照] ボタンをクリックし、モジュール ギャラリーを開きます。
![OperationalInsights モジュールのインポート](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  AzureRM.OperationalInsights を検索し、そのモジュールを Automation アカウントにインポートします。

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>ソリューションの Azure VM への配布準備を行う Runbook をインポートする

1.  [プロセス オートメーション] カテゴリで [Runbook] ページをクリックします。
2.  [ギャラリーの参照] ボタンをクリックします。
3.  "更新と変更追跡" を検索し、その Runbook を Automation アカウントにインポートします。
![配布準備する Runbook のインポート](media/automation-onboard-solutions/import-from-gallery.png)
4.  [編集] をクリックして Runbook のソースを表示し、[発行] ボタンをクリックします。
![配布準備する Runbook のインポート](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Azure VM の配布準備を自動的に行う Runbook を開始する

この Runbook を開始するには、Change Tracking または Update ソリューションの Azure VM への配布準備を完了している必要があります。 パラメーターのために、ソリューションが配布準備された既存の仮想マシンとリソース グループが必要です。
1.  Enable-MultipleSolution Runbook を開きます。
![複数のソリューション Runbook](media/automation-onboard-solutions/runbook-overview.png)
2.  [開始] ボタンをクリックし、パラメーターに次の値を入力します。
    *   VMNAME。 Update または Change Tracking ソリューションに配布準備する既存の VM 名。 空のままにすると、リソース グループ内のすべての VM が配布準備されます。
    *   VMRESOURCEGROUP。 VM がメンバーであるリソース グループ名。
    *   SUBSCRIPTIONID。 配布準備対象の新しい VM があるサブスクリプション ID。 空のままにすると、ワークスペースのサブスクリプションが使用されます。 別のサブスクリプション ID を指定すると、この Automation アカウントの RunAs アカウントも、このサブスクリプションの共同作成者として追加されます。
    *   ALREADYONBOARDEDVM。 Updates または ChangeTracking ソリューションに手動で配布準備された VM 名。
    *   ALREADYONBOARDEDVMRESOURCEGROUP。 VM がメンバーであるリソース グループ名。
    *   SOLUTIONTYPE。 「Updates」または「ChangeTracking」と入力します。
    
    ![複数のソリューション Runbook パラメーター](media/automation-onboard-solutions/runbook-parameters.png)
3.  [OK] をクリックして Runbook ジョブを開始します。
4.  Runbook ジョブ ページで進行状況とエラーを監視します。

## <a name="next-steps"></a>次のステップ

詳細については、[Runbook のスケジュール設定](automation-schedules.md)に関するページを参照してください。