---
title: Azure Machine Learning デザイナーを使用してモデルを再トレーニングする (プレビュー)
titleSuffix: Azure Machine Learning
description: 発行されたパイプラインを使用して Azure Machine Learning デザイナー (プレビュー) でモデルを再トレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 02/24/2020
ms.openlocfilehash: 8e7874ec2a0ea160d29f8755ca8680c4dfbeec1d
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268497"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナーを使用してモデルを再トレーニングする (プレビュー)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

この攻略ガイドでは、Azure Machine Learning デザイナーを使用して機械学習モデルを再トレーニングする方法について説明します。 発行されたパイプラインを使用して、再トレーニング用の機械学習ワークフローを自動化する方法を確認してください。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * 機械学習モデルをトレーニングする。
> * パイプライン パラメーターを作成する。
> * トレーニング パイプラインを発行する。
> * モデルを再トレーニングする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。

* Enterprise SKU の Azure Machine Learning ワークスペース。

この攻略ガイドは、デザイナーでパイプラインを構築することの基本的知識を持っていることが前提となっています。 デザイナーのガイド付き説明については、[こちら](tutorial-designer-automobile-price-train-score.md)のチュートリアルを完了してください。 

### <a name="sample-pipeline"></a>サンプル パイプライン

この記事で使用されるパイプラインは、[サンプル 3 - 収入予測](how-to-designer-sample-classification-predict-income.md)で取り上げたパイプラインの変更されたバージョンです。 サンプル データセットではなく[データのインポート](algorithm-module-reference/import-data.md) モジュールを使用して、独自のデータを利用してモデルをトレーニングする方法を示します。

![データのインポート モジュールが強調表示されている、変更されたサンプル パイプラインを示すスクリーンショット](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>機械学習モデルのトレーニング

モデルを再トレーニングするには、初期モデルが必要です。 このセクションでは、デザイナーを使用してモデルをトレーニングし、保存されたモデルにアクセスする方法を説明します。

1. **[データのインポート]** モジュールを選択します。
1. プロパティ ウィンドウでデータ ストアを指定します。

    ![データのインポート モジュールのサンプル構成を示すスクリーンショット](./media/how-to-retrain-designer/import-data-settings.png)

    この例では、データは [Azure データストア](how-to-access-data.md)に格納されます。 まだデータストアを作成していない場合は、 **[新しいデータストア]** を選択し、ここでデータストアを作成できます。

1. お使いのデータへのパスを指定します。 **[パスの参照]** を選択してデータストアを視覚的に移動することもできます。 

1. キャンバス上部の **[実行]** を選択します。
    
    > [!NOTE]
    > このパイプライン ドラフトに既定のコンピューティングを既に設定している場合、パイプラインは自動的に実行されます。 それ以外の場合は、表示される設定ウィンドウの指示に従って、ここで設定可能です。

### <a name="locate-your-trained-model"></a>トレーニング済みモデルを見つける

デザイナーでは、トレーニング済みのモデルを含むすべてのパイプライン出力が、既定のストレージ アカウントに保存されます。 デザイナーで直接、トレーニング済みのモデルにアクセスすることもできます。

1. パイプラインの実行が完了するまで待機します。

1. **Train Model** (モデルのトレーニング) モジュールを選択します。

1. 設定ウィンドウで **[Outputs+logs]\(出力とログ\)** を選択します。

1. **[View output]\(出力の表示\)** アイコンをクリックし、ポップアップ ウィンドウの指示に従って、トレーニングされたモデルを見つけます。

![トレーニング済みモデルのダウンロード方法を示すスクリーンショット](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>パイプライン パラメーターを作成する

パイプライン パラメーターを追加して実行時に変数を動的に設定します。 このパイプラインについては、新しいデータセットに関してモデルを再トレーニングできるように、トレーニング データ パスのパラメーターを追加します。

1. **[データのインポート]** モジュールを選択します。
1. 設定ウィンドウで、 **[パス]** フィールドの上にある省略記号を選択します。
1. **[Add to pipeline parameter] (パイプライン パラメーターに追加する)** を選択します。
1. パラメーター名と既定値を指定します。

    > [!NOTE]
    > パイプライン ドラフトのタイトルの横にある **[設定] 歯車アイコン**を選択すると、パイプライン パラメーターを調べて編集することができます。 

![パイプライン パラメーターの作成方法を示すスクリーンショット](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>トレーニング パイプラインを発行する

パイプラインを発行すると、パイプライン エンドポイントが作成されます。 パイプライン エンドポイントを使用すると、再現性と自動化のためにパイプラインの再利用と管理を行えます。 この例では、再トレーニング用にパイプラインを設定します。

1. デザイナー キャンバス上の **[Publish] (発行)** を選択します。
1. パイプライン エンドポイントを選択するか新規作成します。

    > [!NOTE]
    > 複数のパイプラインを単一のエンドポイントに発行できます。 エンドポイント内の各パイプラインにはバージョン番号が付けられます。パイプライン エンドポイントを呼び出すときに、この番号を指定できます。

1. **[発行]** を選択します。

## <a name="retrain-your-model"></a>モデルを再トレーニングする

これでトレーニング パイプラインが発行されたので、それを利用し、新しいデータを使用してモデルを再トレーニングできます。 ポータルまたはプログラムのいずれかから、パイプライン エンドポイントからの実行を送信できます。

### <a name="submit-runs-with-the-designer"></a>デザイナーを使用して実行を送信する

以下の手順を使用して、デザイナーからパイプライン エンドポイントの実行を送信します。

1. **[エンドポイント]** ページに移動します。

1. **[パイプライン エンドポイント]** タブを選択します。

1. パイプライン エンドポイントを選択します。

1. **[Published pipelines] (公開済みパイプライン)** タブを選択します。

1. 実行するパイプラインを選択します。

1. **[Submit]\(送信\)** をクリックします。

1. 設定ダイアログでは、新しいデータセットを指し示す、新しい入力データ パスの値を指定できます。

![パラメーター化されたパイプラインの実行をデザイナーで設定する方法を示すスクリーンショット](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-with-code"></a>コードを使用して実行を送信する

概要パネルには、発行されたパイプラインの REST エンドポイントが表示されます。 エンドポイントを呼び出すことにより、発行されたパイプラインを再トレーニングできます。

REST 呼び出しを行うには、OAuth 2.0 ベアラー型認証ヘッダーが必要です。 ワークスペースの認証を設定し、パラメーター化された REST を呼び出す方法については、次の[チュートリアル セクション](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)をご覧ください。

## <a name="next-steps"></a>次のステップ

デザイナー [チュートリアル](tutorial-designer-automobile-price-train-score.md)に従い、回帰モデルをトレーニングし、デプロイします。
