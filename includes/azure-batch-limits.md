---
title: インクルード ファイル
description: インクルード ファイル
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 10/11/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c98a2146a019817152be9fae76638dbaa4d9de3d
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2018
ms.locfileid: "49458859"
---
| **リソース** | **既定の制限** | **上限** |
| --- | --- | --- |
| サブスクリプションあたりのリージョンごとの Batch アカウント数 | 1 - 3 |50 |
| Batch アカウントあたりの専用コア数 | 10 - 100 | N/A<sup>1</sup> |
| Batch アカウントあたりの優先順位の低いコア数 | 10 - 100 | N/A<sup>2</sup> |
| Batch アカウントあたりのアクティブなジョブ数とジョブ スケジュール数<sup>3</sup> | 100 - 300 | 1000<sup>4</sup> |
| Batch アカウントあたりのプール数 | 20 - 100 | 500<sup>4</sup> |

> [!NOTE]
> 既定の制限は、Batch アカウントの作成に使用するサブスクリプションの種類によって異なります。 表示されるコア クォータは、Batch サービス モードの Batch アカウント用です。 [Batch アカウントのクォータを確認してください](../articles/batch/batch-quota-limit.md#view-batch-quotas)。 

<sup>1</sup> Batch アカウントあたりの専用コア数は増やすことができますが、最大数は指定されていません。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

<sup>2</sup> Batch アカウントあたりの優先順位の低いコア数は増やすことができますが、最大数は指定されていません。 このオプション値を増やす場合は、Azure サポートにお問い合わせください。

<sup>3</sup> 完了したジョブとジョブ スケジュールに制限はありません。

<sup>4</sup> この制限を超えた引き上げを要求する場合は、Azure サポートにお問い合わせください。
