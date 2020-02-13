---
title: Azure Event Grid - トピックの診断ログを有効にする
description: この記事では、Azure Event Grid トピックの診断ログを有効にする手順について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960204"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Azure Event Grid トピックの診断ログ
Event Grid ユーザーは、診断設定を使用して、Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースのいずれかで発行および配信のエラー ログをキャプチャして表示することができます。 この記事では、Event Grid トピックの診断ログを有効にする手順について説明します。

## <a name="prerequisites"></a>前提条件

- プロビジョニングされた Event Grid トピック
- 診断ログのキャプチャに使用するプロビジョニングされた宛先。 次のいずれかの宛先を使用できます。
    - Azure ストレージ アカウント
    - イベント ハブ
    - Log Analytics ワークスペース


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>トピックの診断ログを有効にする手順

1. [Azure portal](https://portal.azure.com) にサインインします。
2. 診断ログ設定を有効にするイベント グリッド トピックに移動します。 
3. 左側のメニューの **[監視]** の下にある **[診断設定]** を選択します。
4. **[診断設定]** ページで、 **[Add New Diagnostic Setting]\(新しい診断設定の追加\)** を選択します。 
    
    ![[診断設定を追加する] ボタン](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. 診断設定の **[名前]** を指定します。 

    ![[診断設定] - [名前]](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. ログの 1 つまたは複数のキャプチャ先を有効にしてから、前に作成したキャプチャ リソースを選択して構成します。 
    - **[ストレージ アカウントへのアーカイブ]** を選択した場合は、 **[ストレージ アカウント - 構成]** を選択してから、Azure サブスクリプションのストレージ アカウントを選択します。 

        ![Azure ストレージ アカウントにアーカイブする](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - **[イベント ハブへのストリーム]** を選択した場合は、 **[イベント ハブ - 構成]** を選択してから、Event Hubs 名前空間、イベント ハブ、およびアクセス ポリシーを選択します。 
        ![イベント ハブへのストリーム](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - **[Log Analytics への送信]** を選択した場合は、Log Analytics ワークスペースを選択します。
        ![Log Analytics への送信](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. **[ログ]** セクションの **[DeliveryFailures]** および **[PublishFailures]** オプションを選択します。 
    ![エラーを選択する](./media/enable-diagnostic-logs-topic/log-failures.png)
8. **[保存]** を選択します。 右上隅にある **[X]** を選択して、ページを閉じます。 
9. 次に **[診断設定]** ページに戻り、 **[診断設定]** テーブルに新しいエントリが表示されていることを確認します。 
    ![一覧の診断設定](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     また、トピックのすべてのメトリックの収集を有効にすることもできます。 

## <a name="next-steps"></a>次のステップ
さらにサポートが必要な場合は、[Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-eventgrid)に問題を投稿するか、[サポート チケット](https://azure.microsoft.com/support/options/)を開いてください。 
