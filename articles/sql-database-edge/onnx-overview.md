---
title: Azure SQL Database Edge プレビューでの ONNX を使用した機械学習と AI | Microsoft Docs
description: Azure SQL Database Edge プレビューの機械学習では、Open Neural Network Exchange (ONNX) 形式のモデルがサポートされています。 ONNX とは、さまざまな機械学習フレームワークとツールの間でモデルを交換する場合に使用できるオープン形式です。
keywords: SQL Database Edge をデプロイする
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: c4c87f7f6f8735c9a50c61b0e083c77b915e0d98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510648"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>SQL Database Edge プレビューでの ONNX を使用した機械学習と AI

Azure SQL Database Edge プレビューの機械学習では、[Open Neural Network Exchange (ONNX)](https://onnx.ai/) 形式のモデルがサポートされます。 ONNX とは、さまざまな[機械学習フレームワークとツール](https://onnx.ai/supported-tools)の間でモデルを交換する場合に使用できるオープン形式です。

## <a name="supported-tool-kits"></a>サポートされているツール キット

ONNXMLTools を使用すると、さまざまな機械学習ツール キットのモデルを ONNX モデルに変換できます。 現在、数値データ型と単一列入力の場合、次のツール キットがサポートされています。

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Keras](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (試験段階)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>ONNX モデルを取得する

ONNX 形式のモデルを取得するには、いくつかの方法があります。

- [ONNX Model Zoo](https://github.com/onnx/models): さまざまな種類のタスクに対応する事前トレーニング済み ONNX モデルがいくつか含まれています。 Windows Machine Learning でサポートされているバージョンをダウンロードして使用することができます。

- [機械学習トレーニング フレームワークからのネイティブ エクスポート](https://onnx.ai/supported-tools): いくつかのトレーニング フレームワークでは、ONNX へのネイティブ エクスポート機能がサポートされています。これにより、ご利用のトレーニング済みモデルを特定のバージョンの ONNX 形式で保存することができます。 たとえば、Chainer、Caffee2、PyTorch があります。 さらに、[Azure Machine Learning Service](https://azure.microsoft.com/services/machine-learning-service/) や [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) などのサービスでも、ネイティブの ONNX エクスポートが用意されています。

  - Custom Vision を使用してクラウド内で ONNX モデルをトレーニングおよびエクスポートする方法については、「[チュートリアル: Custom Vision からエクスポートされた ONNX モデルを Windows ML (プレビュー) で使用する](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml)」を参照してください。

- [WinMLTools を使用して既存のモデルを変換する](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): この Python パッケージを使用すると、いくつかのトレーニング フレームワーク形式から ONNX にモデルを変換できます。 ご利用のアプリケーションがターゲットとする Windows のビルドに応じて、モデルの変換先とする ONNX のバージョンを指定することができます。 Python に馴染みがない場合は、[Windows Machine Learning UI ベースのダッシュボード](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard)を使用してモデルを変換できます。

> [!IMPORTANT]
> すべての ONNX バージョンが Azure SQL Database Edge でサポートされているわけではありません。 ONNX モデルを介した数値データ型の予測のみが現在サポートされています。

ONNX モデルを用意したら、そのモデルを Azure SQL Database Edge にデプロイできます。 次に、[ネイティブ スコアリングを PREDICT T-SQL 関数と組み合わせて](/sql/advanced-analytics/sql-native-scoring/)使用できます。

## <a name="limitations"></a>制限事項

現時点で、このサポートは**数値データ型**を持つモデルに限定されています。

- [int および bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [real および float](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql)。
  
他の数値型を、サポートされている型に変換するには、CAST と CONVERT [CAST と CONVERT](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql) を使用します。

モデルの入力については、モデルへの各入力がテーブル内の 1 つの列に対応するように構造化する必要があります。 たとえば、pandas データフレームを使用してモデルをトレーニングする場合、各入力はモデルに対して個別の列とする必要があります。

## <a name="next-steps"></a>次の手順

- [Azure portal を介して SQL Database Edge をデプロイする](deploy-portal.md)
- [Azure SQL Database Edge Preview に ONNX モデルをデプロイする](deploy-onnx.md)