---
title: Azure Automation を Event Grid と統合する | Microsoft Docs
description: 新しい VM を作成するときに自動的にタグを追加し、Microsoft Teams に通知を送信する方法について説明します。
keywords: Automation, Runbook, チーム, Event Grid, 仮想マシン, VM
services: automation
documentationcenter: ''
author: eamonoreilly
manager: ''
editor: ''
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: eamono
ms.openlocfilehash: 6270f8bad893798f46d8db91e7b1140b6a125350
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049866"
---
# <a name="integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Azure Automation を Event Grid および Microsoft Teams と統合する

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Event Grid のサンプル Runbook をインポートする。
> * オプションの Microsoft Teams webhook を作成する。
> * Runbook に対する webhook を作成する。
> * Event Grid のサブスクリプションを作成する。
> * Runbook をトリガーする VM を作成する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを行うには、Azure Event Grid のサブスクリプションからトリガーされる Runbook を保持するために [Azure Automation アカウント](../automation/automation-offering-get-started.md)が必要です。

* `AzureRM.Tags` モジュールは Automation アカウントに読み込む必要があります。モジュールを Azure Automation にインポートする方法については、[Azure Automation にモジュールをインポートする方法](../automation/automation-update-azure-modules.md)に関するページを参照してください。

## <a name="import-an-event-grid-sample-runbook"></a>Event Grid のサンプル Runbook をインポートする

1. お使いの Automation アカウントを選び、**[Runbook]** ページを選びます。

   ![Runbook を選ぶ](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. **[ギャラリーを参照]** ボタンを選びます。

3. **Event Grid** を検索し、**[Integrating Azure Automation with Event grid]\(Azure Automation と Event Grid の統合\)** を選びます。

    ![ギャラリーの Runbook をインポートする](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. **[インポート]** を選び、名前を「**Watch-VMWrite**」にします。

5. インポートされた後、**[編集]** を選んで Runbook ソースを表示します。 **[発行]** を選択します。

> [!NOTE]
> スクリプトの 74 行目は `Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose` に変更する必要があります。 `-Tags` パラメーターは `-Tag` になりました。

## <a name="create-an-optional-microsoft-teams-webhook"></a>オプションの Microsoft Teams webhook を作成する

1. Microsoft Teams で、チャネル名の隣の **[その他のオプション]** を選び、**[コネクタ]** を選びます。

    ![Microsoft Teams の接続](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. コネクタの一覧をスクロールして **[Incoming Webhook]\(受信 webhook\)** に移動し、**[追加]** を選びます。

3. 名前に「**AzureAutomationIntegration**」と入力し、**[作成]** を選びます。

4. webhook をクリップボードにコピーして保存します。 webhook の URL は、Microsoft Teams への情報の送信に使います。

5. **[完了]** を選んで webhook を保存します。

## <a name="create-a-webhook-for-the-runbook"></a>Runbook に対する webhook を作成する

1. Watch-VMWrite Runbook を開きます。

2. **[Webhook]** を選び、**[Webhook の追加]** ボタンを選びます。

3. 名前に「**WatchVMEventGrid**」と入力します。 URL をクリップボードにコピーして保存します。

    ![webhook 名を構成する](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. **[パラメータと実行設定を構成する]** を選び、**[CHANNELURL]** に Microsoft Teams の webhook の URL を入力します。 **[WEBHOOKDATA]** は空白のままにします。

    ![webhook のパラメーターを構成する](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. **[OK]** を選んで Automation Runbook の webhook を作成します。


## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する
1. **[Automation アカウント]** 概要ページで、**[イベント グリッド]** を選びます。

    ![Event Grid の選択](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. **[+ イベント サブスクリプション]** ボタンを選びます。

3. 次の情報を指定して、サブスクリプションを構成します。

    *   名前に「**AzureAutomation**」と入力します。
    *   **[トピックの種類]** で **[Azure サブスクリプション]** を選びます。
    *   **[すべてのイベントの種類を購読します]** チェック ボックスをオフにします。
    *   **[イベントの種類]** で **[Resource Write Success]\(リソース書き込み成功\)** を選びます。
    *   **[サブスクライバー エンドポイント]** に、Watch-VMWrite Runbook の webhook の URL を入力します。
    *   **[プレフィックス フィルター]** に、新しく作成された VM を検索するサブスクリプションとリソース グループを入力します。 結果は次のようになります。`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. **[作成]** を選び、Event Grid サブスクリプションを保存します。

## <a name="create-a-vm-that-triggers-the-runbook"></a>Runbook をトリガーする VM を作成する
1. Event Grid サブスクリプションのプレフィックス フィルターで指定したリソース グループに新しい VM を作成します。

2. Watch-VMWrite Runbook が呼び出されて、新しいタグが VM に追加されます。

    ![VM のタグ](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. 新しいメッセージが Microsoft Teams のチャネルに送信されます。

    ![Microsoft Teams の通知](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Event Grid と Automation の間の統合を設定しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Event Grid のサンプル Runbook をインポートする。
> * オプションの Microsoft Teams webhook を作成する。
> * Runbook に対する webhook を作成する。
> * Event Grid のサブスクリプションを作成する。
> * Runbook をトリガーする VM を作成する。

> [!div class="nextstepaction"]
> [Event Grid を使ったカスタム イベントの作成とルーティング](../event-grid/custom-event-quickstart.md)
