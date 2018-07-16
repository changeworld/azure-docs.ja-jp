---
title: Azure クイックスタート - Azure Automation アカウントを作成する | Microsoft Docs
description: Azure Automation アカウントを作成し、Runbook を実行する方法について説明します
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/13/2017
ms.topic: quickstart
ms.service: automation
ms.component: process-automation
ms.custom: mvc
ms.openlocfilehash: 3accd62415cd7d3a8210b7a05aa1edfd7a380edc
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055156"
---
# <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

Azure を使用して Azure Automation アカウントを作成することができます。 この方法では、ブラウザーベースのユーザー インターフェイスで Automation アカウントとそれに関連するリソースを作成して構成できます。 このクイックスタートでは、Automation アカウントを作成し、そのアカウントで Runbook を実行する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure にサインインします ( https://portal.azure.com )。

## <a name="create-automation-account"></a>Automation アカウントを作成する

1. Azure の左上にある **[リソースの作成]** ボタンをクリックします。

1. **[監視 + 管理]**、**[Automation]** の順に選択します。

1. アカウント情報を入力します。 **[Azure 実行アカウントの作成]** で **[はい]** を選択し、Azure に対する認証が簡単になるアーティファクトが自動的に有効になるようにします。 完了したら、**[作成]** をクリックして、Automation アカウントの展開を開始します。

    ![このページに Automation アカウントに関する情報を入力します](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

1. Automation アカウントは Azure ダッシュボードにピン留めされています。 展開が完了すると、Automation アカウントの概要が自動的に開きます。

    ![Automation アカウントの概要](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Runbook を実行する

チュートリアルの Runbook のいずれかを実行します。

1. **[プロセス オートメーション]** の **[Runbook]** をクリックします。 Runbook の一覧が表示されます。 既定では、アカウントのいくつかのチュートリアル Runbook が有効です。

    ![Automation アカウントの Runbook 一覧](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. **AzureAutomationTutorialScript** Runbook を選択します。 この操作で、Runbook の概要ページが開きます。

    ![Runbook の概要](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. **[開始]** をクリックし、**[Runbook の開始]** ページで **[OK]** をクリックして Runbook を開始します。

    ![Runbook ジョブ ページ](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. **[ジョブの状態]** が "**実行中**" に変わったら、**[出力]** または **[すべてのログ]** をクリックして Runbook ジョブの出力を確認します。 このチュートリアル Runbook では、Azure リソースの一覧が出力されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、Automation アカウント、および関連するすべてのリソースは、不要になったら削除します。 削除するには、Automation アカウントのリソース グループを選択し、**[削除]** をクリックします。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Automation アカウントを展開し、Runbook ジョブを開始し、ジョブ結果を表示しました。 Azure Automation の詳細については、最初の Runbook を作成するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [Automation クイックスタート - Runbook を作成する](./automation-quickstart-create-runbook.md)
