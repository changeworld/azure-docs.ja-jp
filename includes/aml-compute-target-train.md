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
ms.date: 09/17/2020
ms.openlocfilehash: cdf2c3d1840f64b267f1aac1f8877d5f0bf76082
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841954"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます。** たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。 機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)を使用します。

ほとんどのジョブのトレーニング コンピューティング ターゲットには、次のいずれかのリソースを使用できます。 一部のリソースは、自動機械学習、機械学習パイプライン、またはデザイナーで使用することができません。

|トレーニング &nbsp;ターゲット|[自動機械学習](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning パイプライン](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning デザイナー](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/how-to-attach-compute-targets.md#local)| はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| はい | はい | はい |
|[Azure Machine Learning コンピューティング インスタンス](../articles/machine-learning/how-to-create-manage-compute-instance.md) | はい (SDK を使用)  | はい |  |
|[リモート VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | はい  | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| はい (SDK ローカル モードのみ) | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | はい | &nbsp; |
