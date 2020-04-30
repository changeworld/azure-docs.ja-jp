---
title: Azure Batch 分析
description: 一括分析のトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。
ms.topic: article
ms.date: 04/20/2017
ms.openlocfilehash: d35bf249a3ae5dd06f42b89c3d5c2773f9508603
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82113070"
---
# <a name="batch-analytics"></a>一括分析
一括分析のトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。

Batch 診断ログの有効化と利用の詳細については「[Azure Batch 診断ログ](https://azure.microsoft.com/documentation/articles/batch-diagnostics/)」を参照してください。

## <a name="diagnostic-logs"></a>診断ログ

Azure Batch サービスは、特定の Batch リソースの有効期間中に、次の診断ログ イベントを出力します。

**サービス ログ イベント**
* [プール作成](batch-pool-create-event.md)
* [プール削除の開始](batch-pool-delete-start-event.md)
* [プール削除の完了](batch-pool-delete-complete-event.md)
* [プールのサイズ変更の開始](batch-pool-resize-start-event.md)
* [プールのサイズ変更の完了](batch-pool-resize-complete-event.md)
* [タスク開始](batch-task-start-event.md)
* [タスク完了](batch-task-complete-event.md)
* [タスク失敗](batch-task-fail-event.md)