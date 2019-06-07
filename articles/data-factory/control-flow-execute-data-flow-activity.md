---
title: Azure Data Factory でのデータ フローの実行アクティビティ | Microsoft Docs
description: データ ファクトリ パイプラインの内側からデータ フローを実行する方法。
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: b0a6c6feae11f8daeed54c5e763dbff3aa711652
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153486"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Azure Data Factory でのデータ フローの実行アクティビティ
データ フローの実行アクティビティを使用して、パイプライン デバッグ (サンドボックス) 実行とパイプライン トリガー実行で ADF データ フローを実行します。

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>構文

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>型のプロパティ

* ```dataflow``` は、実行するデータ フロー エンティティの名前です。
* ```compute``` は、Spark 実行環境を示します。
* ```coreCount``` は、データ フローのこのアクティビティの実行に割り当てるコア数です。

![データ フローの実行](media/data-flow/activity-data-flow.png "データ フローの実行")

### <a name="run-on"></a>Run on (実行先)

データ フローのこの実行のコンピューティング環境を選択します。 既定では、Azure Auto-Resolve Default Integration Runtime が選択されています。 この選択肢では、データ ファクトリと同じリージョンの Spark 環境でデータ フローが実行されます。 コンピューティングの種類がジョブ クラスターになるため、コンピューティング環境の起動に数分かかります。

### <a name="debugging-pipelines-with-data-flows"></a>データ フローを使用したパイプラインのデバッグ

![デバッグ ボタン](media/data-flow/debugbutton.png "デバッグ ボタン")

データ フロー デバッグでは、ウォーミングされたクラスターを利用して、パイプライン デバッグ実行でデータ フローを対話形式でテストします。 パイプライン内でデータ フローをテストするには、パイプライン デバッグ オプションを使用します。

### <a name="run-on"></a>Run on (実行先)

これは、データ フロー アクティビティの実行に使用する統合ランタイムを定義する必須フィールドです。 既定では、Data Factory は既定の自動解決 Azure 統合ランタイムを使用します。 ただし、データ フロー アクティビティを実行するための特定のリージョン、コンピューティングの種類、コア数、および TTL を定義する、独自の Azure 統合ランタイムを作成できます。

データ フローの実行の既定の設定は、一般コンピューティングの 8 コアで、TTL は 60 分です。

自分のデータ フロー アクティビティ用の Spark 実行環境を制御できます。 [Azure Integration Runtime](concepts-integration-runtime.md) には、実行エンジンを自分のデータ フロー コンピューティング要件と一致させるための、コンピューティングの種類 (汎用、メモリ最適化、およびコンピューティング最適化)、ワーカー コアの数、および Time-To-Live の設定があります。 また、TTL を設定することで、ジョブの実行ですぐに利用できるウォーム クラスターを管理できます。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

### <a name="staging-area"></a>ステージング領域

データを Azure Data Warehouse にシンクする場合は、Polybase バッチ読み込み用のステージングの場所を選択する必要があります。

## <a name="parameterized-datasets"></a>パラメーター化されたデータセット

パラメーター化されたデータセットを使用する場合は、パラメーター値を必ず設定します。

![データ フローの実行のパラメーター](media/data-flow/params.png "パラメーター")

### <a name="debugging-parameterized-data-flows"></a>パラメーター化されたデータ フローのデバッグ

パラメーター化されたデータセットを使用するデータ フローは、データ フローの実行アクティビティを使用してパイプライン デバッグ実行からのみデバッグできます。 現時点では、ADF Data Flow での対話型デバッグ セッションは、パラメーター化されたデータセットでは機能しません。 パイプラインの実行とデバッグ実行では、パラメーターが使用されます。

設計時に完全なメタデータ列の伝達を利用できるように、静的データセットを使用してデータ フローを構築することをお勧めします。 その後、データ フロー パイプラインを運用化するときに、静的データセットを動的パラメーター化データセットに置き換えます。

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)
