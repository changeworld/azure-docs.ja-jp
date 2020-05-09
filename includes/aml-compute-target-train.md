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
ms.date: 12/04/2019
ms.openlocfilehash: dc6c29793b71525ad64254aa9f57e12e56601adc
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82726216"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます**。 たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。  機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)を使用します。

|トレーニング &nbsp;ターゲット|[自動化された ML](../articles/machine-learning/concept-automated-ml.md) | [ML パイプライン](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning デザイナー](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/how-to-set-up-training-targets.md#local)| はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/how-to-set-up-training-targets.md#amlcompute)| はい、 <br/>ハイパーパラメーター&nbsp;調整 | はい | はい |
|[Azure Machine Learning コンピューティング インスタンス](../articles/machine-learning/concept-compute-instance.md) | はい、 <br/>ハイパーパラメーター調整 | はい | はい |
|[リモート VM](../articles/machine-learning/how-to-set-up-training-targets.md#vm) | はい、 <br/>ハイパーパラメーター調整 | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-create-your-first-pipeline.md#databricks)| はい (SDK ローカル モードのみ) | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-create-your-first-pipeline.md#adla) | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-set-up-training-targets.md#hdinsight) | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-set-up-training-targets.md#azbatch) | &nbsp; | はい | &nbsp; |
