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
ms.openlocfilehash: 2591ab6984ebe4f864540ab290881a6e47f0be71
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580592"
---
**コンピューティング ターゲットは、各トレーニング ジョブで順に再利用できます**。 たとえば、リモート VM をワークスペースにアタッチした後、複数のジョブでそれを再利用できます。

|トレーニング &nbsp;ターゲット| GPU のサポート |[自動化された ML](../articles/machine-learning/service/concept-automated-ml.md) | [ML パイプライン](../articles/machine-learning/service/concept-ml-pipelines.md) | [Azure Machine Learning デザイナー](../articles/machine-learning/service/concept-designer.md)
|----|:----:|:----:|:----:|:----:|
|[ローカル コンピューター](../articles/machine-learning/service/how-to-set-up-training-targets.md#local)| 可能性あり | はい | &nbsp; | &nbsp; |
|[Azure Machine Learning コンピューティング クラスター](../articles/machine-learning/service/how-to-set-up-training-targets.md#amlcompute)| はい | はい、 <br/>ハイパーパラメーター&nbsp;調整 | はい | はい |
|[リモート VM](../articles/machine-learning/service/how-to-set-up-training-targets.md#vm) |はい | はい、 <br/>ハイパーパラメーター調整 | はい | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#databricks)| &nbsp; | はい | はい | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/service/how-to-create-your-first-pipeline.md#adla)| &nbsp; | &nbsp; | はい | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/service/how-to-set-up-training-targets.md#hdinsight)| &nbsp; | &nbsp; | はい | &nbsp; |
|[Azure Batch](../articles/machine-learning/service/how-to-set-up-training-targets.md#azbatch)| &nbsp; | &nbsp; | はい | &nbsp; |
