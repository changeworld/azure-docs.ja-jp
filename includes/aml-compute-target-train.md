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
ms.openlocfilehash: 87b1f4ab7b7091970d7bb76ae1e00b06549fb0b4
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96152675"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます。** たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。 機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](/python/api/azureml-pipeline-steps/azureml.pipeline.steps?preserve-view=true&view=azure-ml-py)を使用します。

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
