---
title: Azure SQL Database Edge プレビューでの ONNX を使用した機械学習と AI | Microsoft Docs
description: Azure SQL Database Edge プレビューの機械学習では、Open Neural Network Exchange (ONNX) 形式のモデルがサポートされています。 ONNX とは、さまざまな機械学習フレームワークとツールの間でモデルを交換する場合に使用できるオープン形式です。
keywords: SQL Database Edge をデプロイする
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822846"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge プレビューでの ONNX を使用した機械学習と AI

Azure SQL Database Edge プレビューの機械学習では、[Open Neural Network Exchange (ONNX)](https://onnx.ai/) 形式のモデルがサポートされます。 ONNX とは、さまざまな[機械学習フレームワークとツール](https://onnx.ai/supported-tools)の間でモデルを交換する場合に使用できるオープン形式です。

## <a name="overview"></a>概要

Azure SQL Database Edge で機械学習モデルを推論するには、まずモデルを取得する必要があります。 これは、事前トレーニング済みモデルでも、任意のフレームワークでトレーニングされたカスタム モデルでもかまいません。 Azure SQL Database Edge では ONNX 形式がサポートされているため、モデルをこの形式に変換する必要があります。 モデルの精度に影響を与えることはありません。ONNX モデルを入手したら、モデルを Azure SQL Database Edge にデプロイし、[PREDICT T-SQL 関数によるネイティブ スコアリング](/sql/advanced-analytics/sql-native-scoring/)を使用できます。

## <a name="get-onnx-models"></a>ONNX モデルを取得する

ONNX 形式のモデルを取得するには、いくつかの方法があります。

- [ONNX Model Zoo](https://github.com/onnx/models): ダウンロードしてすぐに使用できる、さまざまな種類のタスク用の、事前トレーニング済みの多くの ONNX モデルが含まれています。

- [ML トレーニング フレームワークからのネイティブ エクスポート](https://onnx.ai/supported-tools): いくつかのトレーニング フレームワークでは、ONNX へのネイティブ エクスポート機能がサポートされています。これにより、ご利用のトレーニング済みモデルを特定のバージョンの ONNX 形式 ([PyTorch](https://pytorch.org/docs/stable/onnx.html)、Chainer、Caffe2 など) で保存できます。 さらに、モデル構築サービス ([Azure Machine Learning の自動機械学習機能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)、[Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) など) で ONNX エクスポートが提供されています。

- [既存のモデルの変換](https://github.com/onnx/tutorials#converting-to-onnx-format): ネイティブ エクスポートをサポートしていないフレームワークの場合は、モデルを ONNX 形式に変換するためのスタンドアロンのパッケージがあります。 例とチュートリアルについては、「[ONNX 形式への変換](https://github.com/onnx/tutorials#converting-to-onnx-format)」を参照してください。 

### <a name="supported-frameworks"></a>サポートされているフレームワーク

ONNX コンバーターを使用すると、さまざまな機械学習フレームワークでトレーニングされたモデルを ONNX 形式に変換できます。 一般的なコンバーターは次のとおりです。 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

サポートされているフレームワークの完全な一覧については、「[ONNX 形式への変換](https://github.com/onnx/tutorials#converting-to-onnx-format)」を参照してください。

## <a name="limitations"></a>制限事項

現時点では、すべての ONNX モデルが Azure SQL Database Edge でサポートされているわけではありません。 サポートは、**数値データ型**を持つモデルに限定されています。

- [int および bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real および float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
他の数値型を、サポートされている型に変換するには、[CAST と CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql) を使用します。

モデルの入力については、モデルへの各入力がテーブル内の 1 つの列に対応するように構造化する必要があります。 たとえば、pandas データフレームを使用してモデルをトレーニングする場合、各入力はモデルに対して個別の列とする必要があります。

## <a name="next-steps"></a>次の手順

- [Azure portal を介して SQL Database Edge をデプロイする](deploy-portal.md)
- [Azure SQL Database Edge Preview に ONNX モデルをデプロイする](deploy-onnx.md)
