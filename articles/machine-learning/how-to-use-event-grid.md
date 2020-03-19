---
title: イベント ドリブン Machine Learning ワークフローを作成する
titleSuffix: Azure Machine Learning
description: イベント ドリブン ソリューションを有効にするために、Azure Machine Learning でイベント グリッドを使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shipatel
author: shivp950
ms.reviewer: larryfr
ms.date: 03/11/2020
ms.openlocfilehash: fe6125682f669e453100488b7e0afc4c49409588
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129740"
---
# <a name="create-event-driven-machine-learning-workflows-preview"></a>イベント ドリブン Machine Learning ワークフローを作成する (プレビュー)

[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) では、Azure Machine Learning イベントがサポートされています。 ワークスペースにおける実行状態の変更、実行の完了、モデルの登録、モデルのデプロイ、データ誤差の検出などのイベントをサブスクライブして使用できます。

イベントの種類について詳しくは、「[Azure Machine Learning と Event Grid の統合](concept-event-grid-integration.md)」および[Azure Machine Learning イベント グリッド スキーマ](/azure/event-grid/event-schema-machine-learning)に関する記事を参照してください。

Event Grid を使用すると、次のような一般的なシナリオが有効になります。

* 実行失敗および実行完了に関するメールを送信する
* モデルの登録後に Azure 関数を使用する
* Azure Machine Learning からさまざまなエンドポイントへのイベントのストリーミング
* 誤差が検出されたときに ML パイプラインをトリガーする

> [!NOTE] 
> 現在、runStatusChanged イベントは、実行状態が **failed** の場合にのみトリガーされます
>

## <a name="prerequisites"></a>前提条件
* イベントを作成する Azure Machine Learning ワークスペースへの共同作成者または所有者アクセス。

### <a name="configure-eventgrid-using-the-azure-portal"></a>Azure portal を使用して EventGrid を構成する

1. [Azure portal](https://portal.azure.com) を開き、Azure Machine Learning ワークスペースに移動します。

1. 左側のバーで __[イベント]__ を選択し、 **[イベント サブスクリプション]** を選択します。 

    ![select-events-in-workspace.png](./media/how-to-use-event-grid/select-event.png)

1. 使用するイベントの種類を選択します。 たとえば、次のスクリーンショットでは、 __[Model registered]\(モデルが登録された\)__ 、 __[Model deployed]\(モデルがデプロイされた\)__ 、 __[Run completed]\(実行の完了\)__ 、 __[Dataset drift detected]\(データセットのドリフトの検出\)__ が選択されています。

    ![add-event-type](./media/how-to-use-event-grid/add-event-type-updated.png)

1. イベントの発行先のエンドポイントを選択します。 次のスクリーンショットでは、 __[イベント ハブ]__ が選択されたエンドポイントであることを示しています。

    ![select-event-handler](./media/how-to-use-event-grid/select-event-handler.png)

選択内容を確認したら、 __[作成]__ をクリックします。 構成の後、これらのイベントはエンドポイントにプッシュされます。


### <a name="configure-eventgrid-using-the-cli"></a>CLI を使用して EventGrid を構成する

最新の [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) をインストールするか、Azure サブスクリプションの一部として提供されている Azure Cloud Shell を使用することができます。

Event Grid 拡張機能をインストールするには、CLI から次のコマンドを使用します。

```azurecli-interactive
az add extension --name eventgrid
```

次の例では、Azure サブスクリプションの選択方法が示されており、Azure Machine Learning の新しいイベント サブスクリプションが作成されます。

```azurecli-interactive
# Select the Azure subscription that contains the workspace
az account set --subscription "<name or ID of the subscription>"

# Subscribe to the machine learning workspace.
az eventgrid event-subscription create \
  --name {eventGridFilterName} \
  --source-resource-id "/subscriptions/{subId}/resourceGroups/{rgName}/ \providers/Microsoft.MachineLearningServices/workspaces/{wsName}" \
  --endpoint {event handler endpoint} \
  --included-event-types Microsoft.MachineLearningServices.ModelRegistered \
  --subject-begins-with "models/mymodelname"
```

## <a name="filter-events"></a>イベントのフィルター処理

イベントを設定するときに、フィルターを適用して、特定のイベント データでのみトリガーするようにできます。 実行状態変更イベントについての次の例では、実行の種類でフィルター処理できます。 イベントは、条件が満たされた場合にのみトリガーされます。 フィルター処理できるイベン トデータの詳細については、[Azure Machine Learning イベント グリッド スキーマ](/azure/event-grid/event-schema-machine-learning)に関する記事を参照してください。 

1. Azure portal にアクセスし、新しいサブスクリプションまたは既存のサブスクリプションを選択します。 

1. [フィルター] タブを選択し、下にスクロールして [高度なフィルター] に移動します。 **[キー]** と **[値]** で、フィルター処理するプロパティの型を指定します。 ここでは、実行の種類がパイプラインの実行またはパイプライン ステップの実行の場合にのみ、イベントがトリガーされることがわかります。  

    :::image type="content" source="media/how-to-use-event-grid/select-event-filters.png" alt-text="イベントのフィルター処理":::

## <a name="sample-scenarios"></a>サンプル シナリオ

### <a name="use-a-logic-app-to-send-email-alerts"></a>ロジック アプリを使用して電子メール アラートを送信する

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) を活用して、すべてのイベントの電子メールを構成します。 条件を使用してカスタマイズし、共同作業を行うチーム全体でコラボレーションと意識を高める受信者を指定します。

1. Azure portal で、Azure Machine Learning ワークスペースに移動し、左側のバーの [イベント] タブを選択します。 ここで、 __[ロジック アプリ]__ を選択します。 

    ![select-logic-ap](./media/how-to-use-event-grid/select-logic-ap.png)

1. ロジック アプリの UI にサインインし、トピックの種類として Machine Learning サービスを選択します。 

    ![select-topic-type](./media/how-to-use-event-grid/select-topic-type.png)

1. 通知するイベントを選択します。 たとえば、次のスクリーンショットで __[RunCompleted]__ を選択します。

    ![select-event-runcomplete](./media/how-to-use-event-grid/select-event-runcomplete.png)

1. 上のセクションのフィルター処理方法を使用するか、フィルターを追加して、イベントの種類のサブセットでのみロジック アプリをトリガーすることができます。 次のスクリーンショットでは、 __/datadriftID/runs/__ の__プレフィックス フィルター__が使用されています。

    ![filter-events](./media/how-to-use-event-grid/filtering-events.png)

1. 次に、このイベントを使用して電子メールを検索するステップを追加します。 イベントの受信に使用できるメール アカウントはいくつかあります。 また、電子メール アラートを送信する条件を構成することもできます。

    ![select-email-action](./media/how-to-use-event-grid/select-email-action.png)

1. __[メールの送信]__ を選択し、パラメーターを入力します。 件名には、イベントのフィルター処理に役立つ __[Event Type]\(イベントの種類\)__ と __[Topic]\(トピック\)__ を含めることができます。 メッセージ本文で実行するために、ワークスペース ページへのリンクを含めることもできます。 

    ![configure-email-body](./media/how-to-use-event-grid/configure-email-body.png)

1. この操作を保存するには、ページの左端にある **[名前を付けて保存]** を選択します。 表示される右側のバーで、このアクションの作成を確認します。

    ![confirm-logic-app-create](./media/how-to-use-event-grid/confirm-logic-app-create.png)


### <a name="use-a-logic-app-to-trigger-retraining-workflows-when-data-drift-occurs"></a>ロジック アプリを使用して、データ ドリフトが発生したときに再トレーニング ワークフローをトリガーする

モデルは時間の経過と共に古くなり、実行されているコンテキストでは役に立たなくなります。 モデルの再トレーニングを行う時期であることを通知する方法の 1 つは、データ ドリフトを検出することです。 

この例では、Azure Logic App でイベント グリッドを使用して再トレーニングをトリガーする方法を示します。 この例では、モデルのトレーニングとサービス提供データセットの間でデータ ドリフトが発生したときに、Azure Data Factory パイプラインをトリガーします。

開始する前に、次の操作を行います。

* データセット モニターを設定して、ワークスペース内の[データの誤差を検出する]( https://aka.ms/datadrift)
* 発行される [Azure Data Factory パイプライン](https://docs.microsoft.com/azure/data-factory/)を作成する。

この例では、単純な Data Factory パイプラインを使用してファイルを blob ストアにコピーし、発行された Machine Learning パイプラインを実行します。 このシナリオの詳細については、[Azure Data Factory の Machine Learning の手順](https://docs.microsoft.com/azure/data-factory/transform-data-machine-learning-service)を設定する方法を参照してください。

![adf-mlpipeline-stage](./media/how-to-use-event-grid/adf-mlpipeline-stage.png)

1. ロジック アプリの作成を開始します。 [Azure portal](https://portal.azure.com) にアクセスし、ロジック アプリを検索して、[作成] を選択します。

    ![search-logic-app](./media/how-to-use-event-grid/search-for-logic-app.png)

1. 必要な情報を入力します。 エクスペリエンスを単純化するには、Azure Data Factory パイプラインと Azure Machine Learning ワークスペースと同じサブスクリプションおよびリソース グループを使用します。

    ![set-up-logic-app-for-adf](./media/how-to-use-event-grid/set-up-logic-app-for-adf.png)

1. ロジック アプリを作成したら、 __[Event Grid のリソース イベントが発生するとき]__ を選択します。 

    ![select-event-grid-trigger](./media/how-to-use-event-grid/select-event-grid-trigger.png)

1. ログインし、イベントの詳細を入力します。 __[リソース名]__ をワークスペース名に設定します。 __[イベントの種類]__ を __[DatasetDriftDetected]__ に設定します。

    ![login-and-add-event](./media/how-to-use-event-grid/login-and-add-event.png)

1. 新しいステップを追加し、__Azure Data Factory__ を検索します。 __[パイプラインの実行の作成]__ を選択します。 

    ![create-adfpipeline-run](./media/how-to-use-event-grid/create-adfpipeline-run.png)

1. ログインし、実行する公開された Azure Data Factory パイプラインを指定します。

    ![specify-adf-pipeline](./media/how-to-use-event-grid/specify-adf-pipeline.png)

1. ページの左上にある **[保存]** ボタンを使用して、ロジック アプリを保存して作成します。 アプリを表示するには、[Azure portal](https://portal.azure.com) のワークスペースにアクセスし、 **[イベント]** をクリックします。

    ![show-logic-app-webhook](./media/how-to-use-event-grid/show-logic-app-webhook.png)

これで、ドリフトが発生したときに Data Factory パイプラインがトリガーされます。 [新しいワークスペース ポータル](https://ml.azure.com)で、データ ドリフトの実行と機械学習パイプラインの詳細を確認します。 

![view-in-workspace](./media/how-to-use-event-grid/view-in-workspace.png)

### <a name="use-azure-functions-to-deploy-a-model-based-on-tags"></a>Azure Functions を使用してタグに基づくモデルをデプロイする

Azure Machine Learning モデルオブジェクトには、モデルの名前、バージョン、タグ、プロパティなど、デプロイをピボットできるパラメーターが含まれています。 モデル登録イベントはエンドポイントをトリガーでき、Azure 関数を使用して、これらのパラメーターの値に基づいてモデルを配置できます。

例については、[https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid](https://github.com/Azure-Samples/MachineLearningSamples-NoCodeDeploymentTriggeredByEventGrid) リポジトリを参照し、**readme** ファイルの手順に従ってください。

## <a name="next-steps"></a>次のステップ

* 使用可能なイベントの詳細については、[Azure Machine Learning イベント スキーマ](/azure/event-grid/event-schema-machine-learning)に関する記事を参照してください。
