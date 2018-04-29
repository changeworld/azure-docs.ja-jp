---
title: Azure Stream Analytics ジョブの監視アラートを設定する
description: この記事では、Azure Portal を使って Azure Stream Analytics ジョブの監視とアラートを設定する方法について説明します。
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: fb69f1b52e5b17dd6794c896969c29003a929c9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-set-up-alerts-for-azure-stream-analytics-jobs"></a>チュートリアル: Azure Stream Analytics ジョブのアラートを設定する
メトリックが指定した条件に達したときにアラートをトリガーするルールを設定できます。 たとえば、次のような条件でアラートを設定できます。

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

ルールは、ポータルでメトリックに対して設定することも、操作ログのデータに対して [プログラムによって](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) 構成することもできます。

## <a name="set-up-alerts-in-the-azure-portal"></a>Azure Portal でアラートを設定する
1. Azure Portal で、アラートを作成する Stream Analytics ジョブを開きます。 

2. **[ジョブ]** ブレードで **[監視]** セクションをクリックします。  

3. **[メトリック]** ブレードで、**[アラートの追加]** コマンドをクリックします。

      ![Azure portal setup](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. 名前と説明を入力します。

5. セレクターを使用して、アラートを送信する条件を定義します。

6. アラートの送信先の情報を入力します。

      ![Azure Streaming Analytics ジョブのアラートの設定](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Azure Portal でのアラートの構成の詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。  


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次の手順
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

