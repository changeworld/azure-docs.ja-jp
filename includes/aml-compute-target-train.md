---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 05/30/2019
ms.openlocfilehash: 50905eb987defac612f1055b450b682726f0a56f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752959"
---
|トレーニング &nbsp;ターゲット| GPU のサポート |[自動化された ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML パイプライン](../articles/machine-learning/service/concept-ml-pipelines.md) | [ビジュアル インターフェイス](../articles/machine-learning/service/ui-concept-visual-interface.md)
|----|:----:|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 可能性あり | はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| はい | はい、 <br/>ハイパーパラメーター&nbsp;調整 | はい | はい |
|[リモート VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |はい | はい、 <br/>ハイパーパラメーター調整 | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | はい | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | はい | &nbsp; |

**すべてのコンピューティング ターゲットは、複数のトレーニング ジョブで再利用できます**。 たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。