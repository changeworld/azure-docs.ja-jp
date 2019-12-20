---
title: Azure Machine Learning パイプラインを実行する
description: Azure Machine Learning パイプラインを Azure Data Factory パイプラインで実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.date: 10/10/2019
ms.openlocfilehash: ef630486860def2781634926f4e9385cd030c171
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913241"
---
# <a name="execute-azure-machine-learning-pipelines-in-azure-data-factory"></a>Azure Machine Learning パイプラインを Azure Data Factory で実行する

Azure Machine Learning パイプラインを Azure Data Factory パイプラインのステップとして実行します。 Machine Learning パイプラインの実行アクティビティにより、ローンの債務不履行の可能性の特定、センチメントの判定、顧客行動パターンの分析といったバッチ予測シナリオが可能になります。

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
linkedServiceName | Azure Machine Learning に対するリンクされたサービス | リンクされたサービスの参照 | はい
mlPipelineId | 発行された Azure Machine Learning パイプラインの ID | 文字列 (または文字列の resultType を含む式) | はい
experimentName | Machine Learning パイプラインの実行の実行履歴実験名 | 文字列 (または文字列の resultType を含む式) | いいえ
mlPipelineParameters | 発行された Azure Machine Learning パイプライン エンドポイントに渡されるキーと値のペア。 キーは、発行された Machine Learning パイプラインで定義されているパイプラインのパラメーターの名前と一致する必要があります | キーと値のペアが含まれるオブジェクト (resultType オブジェクトの式) | いいえ
mlParentRunId | 親の Azure Machine Learning パイプラインの実行 ID | 文字列 (または文字列の resultType を含む式) | いいえ
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
