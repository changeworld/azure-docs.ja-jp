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
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629874"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます。** たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。 機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)を使用します。

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

> [!TIP]
> コンピューティング インスタンスには 120 GB の OS ディスクがあります。 ディスク領域が不足する場合は、コンピューティング インスタンスを[停止または再起動](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage)する前に、[ターミナルを使用して](../articles/machine-learning/how-to-access-terminal.md)少なくとも 1 GB から 2 GB をクリアしてください。
