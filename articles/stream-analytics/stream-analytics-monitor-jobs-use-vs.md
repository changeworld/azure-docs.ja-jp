---
title: Visual Studio を使用して Azure Stream Analytics ジョブを監視および管理する
description: この記事では、Visual Studio を使って Azure Stream Analytics ジョブを監視および管理する方法について説明します。
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 9cc65fc5119bedc816f825b7aae6120f4bca3b12
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246842"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Visual Studio を使用して Stream Analytics ジョブを監視および管理する

この記事では、Visual Studio で Stream Analytics ジョブを監視する方法を示します。 Visual Studio の Azure Stream Analytics ツールは、IDE を離れることなく、Azure portal と同様の監視のエクスペリエンスを提供します。 **Script.asaql** から **Azure に送信**するとすぐに、ジョブの監視を開始できます。既存のジョブは、その作成方法に関係なく、監視することができます。 

## <a name="job-summary"></a>ジョブの概要

**[ジョブの概要]** と **[ジョブ メトリック]** は、ジョブの簡単なスナップショットを提供します。 ジョブの状態およびイベント情報をひとめで確認できます。

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>ジョブのメトリックス

**[ジョブの概要]** は、折りたたむことができます。重要なメトリックとともにグラフを表示するには、**[ジョブ メトリック]** タブをクリックします。 グラフに追加するメトリックの種類にチェックを付け、グラフから削除するメトリックの種類のチェックを外します。

![Stream Analytics メトリック](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>エラーの監視

**[エラー]** タブをクリックして、エラーを監視することもできます。

![Stream Analytics エラー](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)を参照してください。 

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Visual Studio の Azure Stream Analytics ツールのインストール](stream-analytics-tools-for-visual-studio-install.md) 


