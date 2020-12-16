---
title: 新しいデータに対して ML パイプラインをトリガーする
titleSuffix: Azure Machine Learning
description: Azure Logic Apps を使用して新しいデータに応答することで Azure Machine Learning パイプラインの実行をトリガーする方法について説明します。
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4
ms.openlocfilehash: 37a18d147d3aca713d0c6bd934e23aa22b2521a5
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028888"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>ロジック アプリから Machine Learning パイプラインの実行をトリガーする

新しいデータが出現したときに、Azure Machine Learning パイプラインの実行をトリガーします。 たとえば、BLOB ストレージ アカウントに新しいデータが出現したときに、新しいモデルをトレーニングするパイプラインをトリガーすることができます。 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) を使用してトリガーを設定します。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

* 発行された Machine Learning パイプライン用の REST エンドポイント。 [パイプラインを作成して発行します](how-to-create-your-first-pipeline.md)。 次に、パイプライン ID を使用して、PublishedPipeline の REST エンドポイントを見つけます。
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* データを格納する [Azure BLOB ストレージ](../storage/blobs/storage-blobs-overview.md)。
* BLOB ストレージ アカウントの詳細を格納する、ワークスペース内の[データストア](how-to-access-data.md)。

## <a name="create-a-logic-app"></a>ロジック アプリの作成

ここで [Azure ロジック アプリ](../logic-apps/logic-apps-overview.md) インスタンスを作成します。 必要に応じて、[統合サービス環境 (ISE) を使用](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)して、ロジック アプリで使用するための[カスタマー マネージド キーを設定](../logic-apps/customer-managed-keys-integration-service-environment.md)します。

ロジック アプリがプロビジョニングされたら、次の手順に従って、パイプラインのトリガーを構成します。

1. [システム割り当てマネージド ID](../logic-apps/create-managed-service-identity.md) を作成し、アプリに Azure Machine Learning ワークスペースへのアクセス権を付与します。

1. ロジック アプリ デザイナー ビューに移動し、[空のロジックアプリ] テンプレートを選択します。 
    > [!div class="mx-imgBorder"]
    > ![空のテンプレート](media/how-to-trigger-published-pipeline/blank-template.png)

1. デザイナーで **BLOB** を検索します。 **[BLOB が追加または変更されたとき (プロパティのみ)]** トリガーを選択して、このトリガーをロジック アプリに追加します。
    > [!div class="mx-imgBorder"]
    > ![トリガーの追加](media/how-to-trigger-published-pipeline/add-trigger.png)

1. BLOB の追加または変更を監視するための BLOB ストレージ アカウントの接続情報を入力します。 監視するコンテナーを選択します。 
 
    自動で機能する、更新をポーリングする **間隔** と **頻度** を選択します。  

    > [!NOTE]
    > このトリガーでは、選択したコンテナーは監視されますが、サブフォルダーは監視されません。

1. 新しい BLOB または変更された BLOB が検出されたときに実行される HTTP アクションを追加します。 **[+ 新しいステップ]** を選択してから、HTTP アクションを検索して選択します。

  > [!div class="mx-imgBorder"]
  > ![HTTP アクションを検索する](media/how-to-trigger-published-pipeline/search-http.png)

  以下の設定を使用してアクションを構成します。

  | 設定 | 値 | 
  |---|---|
  | HTTP アクション | POST |
  | URI |[前提条件](#prerequisites)として確認した、発行されたパイプラインへのエンドポイント |
  | 認証モード | マネージド ID |

1. 使用する可能性のあるすべての [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) の値を設定するためのスケジュールを設定します。

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    ワークスペースに追加した `DataStoreName` を[前提条件](#prerequisites)として使用します。
     
    > [!div class="mx-imgBorder"]
    > ![HTTP 設定](media/how-to-trigger-published-pipeline/http-settings.png)

1. **[保存]** を選択するとスケジュールの準備ができます。

> [!IMPORTANT]
> Azure ロールベースのアクセス制御 (Azure RBAC) を使用してパイプラインへのアクセスを管理している場合は、[パイプライン シナリオ (トレーニングまたはスコアリング) のアクセス許可を設定](how-to-assign-roles.md#common-scenarios)します。

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [バッチ スコアリングに Azure Machine Learning パイプラインを使用する](tutorial-pipeline-batch-scoring-classification.md)

* [パイプライン](concept-ml-pipelines.md)に関する詳細情報
* [Jupyter を使用した Azure Machine Learning の探索](samples-notebooks.md)に関する詳細情報

