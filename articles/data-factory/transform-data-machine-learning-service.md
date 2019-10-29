---
title: Azure Machine Learning service パイプラインを Azure Data Factory パイプラインで実行する | Microsoft Docs
description: Azure Machine Learning service パイプラインを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/10/2019
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: ddaa020b3567f8f00cfd7fc7f7728f1160d9306b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72302024"
---
# <a name="execute-azure-machine-learning-service-pipelines-in-azure-data-factory"></a>Azure Machine Learning service パイプラインを Azure Data Factory で実行する

Azure Machine Learning service パイプラインを Azure Data Factory パイプラインのステップとして実行します。 Machine Learning パイプラインの実行アクティビティにより、ローンの債務不履行の可能性の特定、センチメントの判定、顧客行動パターンの分析といったバッチ予測シナリオが可能になります。

## <a name="syntax"></a>構文

```json
{
    "name": "Machine Learning Execute Pipeline",
    "type": "AzureMLExecutePipeline",
    "linkedServiceName": {
        "referenceName": "AzureMLService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mlPipelineId": "machine learning pipeline ID",
        "experimentName": "experimentName",
        "mlPipelineParameters": {
            "mlParameterName": "mlParameterValue"
        }
    }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
名前 | パイプラインのアクティビティの名前。 | string | はい
type | アクティビティの種類は "AzureMLExecutePipeline" です | string | はい
linkedServiceName | Azure Machine Learning service に対するリンクされたサービス | リンクされたサービスの参照 | はい
mlPipelineId | 発行された Azure Machine Learning パイプラインの ID | 文字列 (または文字列の resultType を含む式) | はい
experimentName | Machine Learning パイプラインの実行の実行履歴実験名 | 文字列 (または文字列の resultType を含む式) | いいえ
mlPipelineParameters | 発行された Azure Machine Learning パイプライン エンドポイントに渡されるキーと値のペア。 キーは、発行された Machine Learning パイプラインで定義されているパイプラインのパラメーターの名前と一致する必要があります | キーと値のペアが含まれるオブジェクト (resultType オブジェクトの式) | いいえ
mlParentRunId | 親の Azure Machine Learning service パイプライン実行 ID | 文字列 (または文字列の resultType を含む式) | いいえ
continueOnStepFailure | ステップが失敗した場合に、Machine Learning パイプライン実行の他のステップの実行を続けるかどうか | ブール値 | いいえ

## <a name="next-steps"></a>次の手順
別の手段でデータを変換する方法を説明している次の記事を参照してください。

* [データ フローの実行アクティビティ](control-flow-execute-data-flow-activity.md)
* [U-SQL アクティビティ](transform-data-using-data-lake-analytics.md)
* [Hive アクティビティ](transform-data-using-hadoop-hive.md)
* [Pig アクティビティ](transform-data-using-hadoop-pig.md)
* [MapReduce アクティビティ](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streaming アクティビティ](transform-data-using-hadoop-streaming.md)
* [Spark アクティビティ](transform-data-using-spark.md)
* [.NET カスタム アクティビティ](transform-data-using-dotnet-custom-activity.md)
* [ストアド プロシージャ アクティビティ](transform-data-using-stored-procedure.md)
