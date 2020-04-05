---
title: Azure Batch 分析
description: 一括分析のトピックには、Batch サービスのリソースで使用できるイベントとアラートに関するリファレンス情報が含まれています。
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d9538461dfa48a961f9325566b2adaf265e8daeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025964"
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