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
ms.date: 06/18/2021
ms.openlocfilehash: e4157e3a80bd0a7ec7eac2399c71bda2d6d03032
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288257"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます。** たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。 機械学習パイプラインの場合は、各コンピューティング ターゲットに対して、適切な[パイプライン ステップ](/python/api/azureml-pipeline-steps/azureml.pipeline.steps)を使用します。

ほとんどのジョブのトレーニング コンピューティング ターゲットには、次のいずれかのリソースを使用できます。 一部のリソースは、自動機械学習、機械学習パイプライン、またはデザイナーで使用することができません。 Azure Databricks は、ローカル実行および機械学習パイプライン用のトレーニング リソースとして使用できますが、他のトレーニング用のリモート ターゲットとしては使用できません。

|トレーニング &nbsp;ターゲット|[自動機械学習](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning パイプライン](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning デザイナー](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/how-to-attach-compute-targets.md#local)| はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| はい | はい | はい |
|[Azure Machine Learning コンピューティング インスタンス](../articles/machine-learning/how-to-create-manage-compute-instance.md) | はい (SDK を使用)  | はい | はい |
|[リモート VM](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | はい  | はい | &nbsp; |
|[Apache Spark プール (プレビュー)](../articles/machine-learning/how-to-attach-compute-targets.md#synapse)| はい (SDK ローカル モードのみ) | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| はい (SDK ローカル モードのみ) | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | はい | &nbsp; |
|[Azure Kubernetes Service](../articles/machine-learning/how-to-attach-compute-targets.md#kubernetes) (プレビュー) | はい | はい | はい |
|[Azure Arc 対応 Kubernetes](../articles/machine-learning/how-to-attach-compute-targets.md#kubernetes) (プレビュー) | はい | はい | はい |

> [!TIP]
> コンピューティング インスタンスには 120 GB の OS ディスクがあります。 ディスク領域が不足する場合は、コンピューティング インスタンスを[停止または再起動](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage)する前に、[ターミナルを使用して](../articles/machine-learning/how-to-access-terminal.md)少なくとも 1 GB から 2 GB をクリアしてください。
