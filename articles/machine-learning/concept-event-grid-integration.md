---
title: Azure Machine Learning イベントを使用する
titleSuffix: Azure Machine Learning
description: この記事では、Azure Event Grid を使用して、Azure Machine Learning によって生成されたイベントにサブスクライブ、対応、およびアンサブスクライブする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: andyxu
author: gogowings
ms.date: 11/04/2019
ms.openlocfilehash: d8a975487c68a21b2c8b6fa2f07d86c312243f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139047"
---
# <a name="consume-azure-machine-learning-events-preview"></a>Azure Machine Learning イベントを使用する (プレビュー)

Azure Machine Learning は、モデルのトレーニング、モデルのデプロイ、監視など、機械学習プロセスのライフサイクル全体を管理します。 Azure Machine Learning イベントによって、アプリケーションは、最新のサーバーレス アーキテクチャを使用することで、機械学習のライフサイクル中のイベント (トレーニングの実行の完了、モデルの登録とデプロイ、データ ドリフトの検出など) に対応できます。 

これらのイベントは [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通じて発行されます。 お客様は Azure portal、PowerShell、または Azure CLI を使用して、[1 つまたは複数のイベントの種類およびフィルター条件を指定する](/azure/event-grid/event-filtering)ことによってイベントを簡単にサブスクライブできます。 また、Azure Functions、Azure Logic Apps、汎用 Webhook などのさまざまなイベント ハンドラーを構築することもできます。 Azure Machine Learning は、Azure Event Grid と連携し、イベント ドリブン アプリケーションを構築するための高い可用性、信頼性、およびフォールト トレランスを備えたイベント配信プラットフォームを提供します。

Azure Machine Learning と Event Grid の併用について詳しくは、「[イベント ドリブン Machine Learning ワークフローの作成 (プレビュー)](how-to-use-event-grid.md)」を参照してください。

## <a name="the-event-model"></a>イベント モデル 

Azure Event Grid は、Azure Machine Learning やその他の Azure サービスなどのソースからイベントを読み取ります。 これらのイベントは、その後 Azure Event Hubs、Azure Functions、Logic Apps などのイベント ハンドラーに送信されます。 次の図は、Event Grid がソースとハンドラーをどのように接続するかを示すもので、サポートされる統合の包括的な一覧ではありません。

![Azure Event Grid の機能モデル](./media/concept-event-grid-integration/azure-event-grid-functional-model.png)

イベント ソースとイベント ハンドラーの詳細については、「[Event Grid とは](/azure/event-grid/overview)」を参照してください。

## <a name="azure-machine-learning-event-types"></a>Azure Machine Learning のイベントの種類

Azure Machine Learning は、機械学習のライフサイクルのさまざまな時点でイベントを提供します。 

| イベントの種類 | 説明 |
| ---------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | 機械学習実験の実行が完了したときに発生します |
| `Microsoft.MachineLearningServices.ModelRegistered` | 機械学習モデルがワークスペースに登録されたときに発生します |
| `Microsoft.MachineLearningServices.ModelDeployed` | 1 つ以上のモデルを持つ推論サービスのデプロイが完了したときに発生します |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | 2 つのデータセットのデータ ドリフト検出ジョブが完了したときに発生します |
| `Microsoft.MachineLearningServices.RunStatusChanged` | 実行状態が変更されたときに発生します。現時点では、実行状態が 'failed' の場合にのみ発生します |

## <a name="subscribe-to-machine-learning-events"></a>Machine Learning イベントをサブスクライブする

Azure Machine Learning イベントのサブスクリプションは、ロールベースのアクセス制御 (RBAC) によって保護されています。 ワークスペースの[共同作成者または所有者](how-to-assign-roles.md#default-roles)だけが、イベント サブスクリプションの作成、更新、削除を行うことができます。

イベント サブスクリプションは、さまざまな条件に基づいてフィルター処理できます。 イベント サブスクリプションの[作成](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)中、または[作成後](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)に、イベント サブスクリプションをフィルター処理することができます。 

### <a name="filter-by-event-type"></a>イベントの種類でフィルター処理する
イベント サブスクリプションでは、1 つまたは複数の Azure Machine Learning イベントの種類を指定できます。

### <a name="filter-by-event-subject"></a>イベントの件名でフィルター処理する
Azure Event Grid は、__次で始まる__ と __次で終わる__ という一致に基づく件名フィルターをサポートするため、件名が一致するイベントはサブスクライバーに配信されます。 機械学習イベントが異なると、件名の形式も異なります。

| イベントの種類 | 件名の形式 | 件名の例 |
| ---------- | ----------- | ----------- |
| `Microsoft.MachineLearningServices.RunCompleted` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` |
| `Microsoft.MachineLearningServices.ModelRegistered` | `models/{modelName}:{modelVersion}` | `models/sklearn_regression_model:3` |
| `Microsoft.MachineLearningServices.ModelDeployed` | `endpoints/{serviceId}` | `endpoints/my_sklearn_aks` |
| `Microsoft.MachineLearningServices.DatasetDriftDetected` | `datadrift/{data.DataDriftId}/run/{data.RunId}` | `datadrift/4e694bf5-712e-4e40-b06a-d2a2755212d4/run/my_driftrun1_1550564444_fbbcdc0f` |
| `Microsoft.MachineLearningServices.RunStatusChanged` | `experiments/{ExperimentId}/runs/{RunId}` | `experiments/b1d7966c-f73a-4c68-b846-992ace89551f/runs/my_exp1_1554835758_38dbaa94` | 

### <a name="advanced-filtering"></a>高度なフィルター処理

Azure Event Grid は、発行されたイベント スキーマに基づく高度なフィルター処理もサポートしています。 Azure Machine Learning のイベント スキーマの詳細については、「[Azure Machine Learning の Azure Event Grid イベント スキーマ](../event-grid/event-schema-machine-learning.md)」を参照してください。

実行できる高度なフィルター処理の例をいくつか次に示します。

* `Microsoft.MachineLearningServices.ModelRegistered` イベントの場合、モデルのタグ値をフィルター処理するには、次のようにします。

    ```
    --advanced-filter data.ModelTags.key1 StringIn ('value1')
    ```

フィルターを適用する方法の詳細については、「[Event Grid のイベントのフィルター処理](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)」をご覧ください。

## <a name="consume-machine-learning-events"></a>Machine Learning イベントを使用する

Machine Learning イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。

> [!div class="checklist"]
> * 複数のサブスクリプションが同じイベント ハンドラーにイベントをルーティングするように構成される場合があるため、イベントが特定のソースからのものであると見なすのではなく、メッセージのトピックをチェックして、予測している Machine Learning ワークスペースから来ていることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * メッセージは順不同で到着したり、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。  また、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。
> * わからないフィールドは無視します。 この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
> * 失敗またはキャンセルされた Azure Machine Learning 操作はイベントをトリガーしません。 たとえば、モデル デプロイが失敗した場合、Microsoft.MachineLearningServices.ModelDeployed はトリガーされません。 アプリケーションを設計するときは、このような障害モードを考慮してください。 Azure Machine Learning SDK、CLI、またはポータルをいつでも使用して、操作の状態を確認して失敗の詳しい理由を理解することができます。

Azure Event Grid を使用すると、Azure Machine Learning イベントによってトリガー可能な、分離されたメッセージ ハンドラーを構築できます。 メッセージ ハンドラーの主要な例を次に示します。
* Azure Functions
* Azure Logic Apps
* Azure Event Hubs
* Azure Data Factory パイプライン
* 汎用 Webhook (Azure プラットフォームまたは他の場所でホストされる場合があります)

## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Azure Machine Learning のイベントを試してみてください。

- [Event Grid について](../event-grid/overview.md)
- [Azure Machine Learning 用の Azure Event Grid イベント スキーマ](../event-grid/event-schema-machine-learning.md)
- [Azure Machine Learning を使用してイベント ドリブン ワークフローを作成する](how-to-use-event-grid.md)
