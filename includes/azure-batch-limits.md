---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 06/03/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 54a789aba793d9a6b44d5fe9a1e0f42023002b5a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025710"
---
| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| サブスクリプションあたりのリージョンごとの Azure Batch アカウント数 | 1 ～ 3 |50 |
| Batch アカウントあたりの専用コア数 | 90 から 900 | サポートにお問い合せください |
| Batch アカウントあたりの優先順位の低いコア数 | 10 から 100 | サポートにお問い合せください |
| Batch アカウントあたりの **[アクティブな](/rest/api/batchservice/job/get#jobstate)** ジョブおよびジョブ スケジュール (**完了した** ジョブには制限なし) | 100 から 300 | 1,000<sup>1</sup> |
| Batch アカウントあたりのプール数 | 20 から 100 | 500<sup>1</sup> |

<sup>1</sup>この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。

> [!IMPORTANT]
> 専用クォータをリクエストおよび管理する方法を変更しています。  合計専用 vCPU が現在適用されている値ですが、間もなく VM シリーズごとの専用クォータを適用します。 優先度の低いクォータは、合計の制限に基づいて引き続き適用されます。VM シリーズ別に適用されることはありません。

> [!NOTE]
> 既定の制限は、Batch アカウントの作成に使用するサブスクリプションの種類によって異なります。 表示されるコア クォータは、Batch サービス モードの Batch アカウント用です。 [Batch アカウントのクォータを確認してください](../articles/batch/batch-quota-limit.md#view-batch-quotas)。

> [!IMPORTANT]
> 世界的なパンデミックの間、より適切に容量を管理できるようにするために、一部のリージョンの新規 Batch アカウントおよび一部の種類のサブスクリプションの既定のコア クォータが、上記の値の範囲よりも (場合によってはゼロ コアに) に減らされています。 新規の Batch アカウントを作成するときに、[コア クォータを確認し](../articles/batch/batch-quota-limit.md#view-batch-quotas)、必要に応じて[コア クォータの増加を要求してください](../articles/batch/batch-quota-limit.md#increase-a-quota)。 または、十分なクォータが既にある Batch アカウントを再利用することを検討してください。