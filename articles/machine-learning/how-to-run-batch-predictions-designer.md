---
title: Azure Machine Learning デザイナーを使用してバッチ予測を実行する (プレビュー)
titleSuffix: Azure Machine Learning
description: デザイナーを使用し、モデルをトレーニングしてバッチ予測を設定する方法について説明します。 HTTP ライブラリからトリガーできる、パラメーター化された Web サービスとしてパイプラインをデプロイします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 11/19/2019
ms.custom: Ignite2019
ms.openlocfilehash: 8d80282044adfa723940aa6f68efc1e719e713c0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75532055"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer"></a>Azure Machine Learning デザイナーを使用してバッチ予測を実行する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このガイドでは、デザイナーを使用してモデルをトレーニングし、バッチ予測パイプラインと Web サービスを設定する方法について説明します。 バッチ予測では、大規模なデータ セットでトレーニング済みモデルに継続的に、また、オンデマンドでスコアを付けることができます。これは任意で HTTP ライブラリからトリガーできる Web サービスとして構成されます。 

SDK を使用してバッチ スコアリング サービスを設定する方法については、それに付随する[ガイド](how-to-run-batch-predictions.md)を参照してください。

このガイドでは、以下のタスクについて学習します。

> [!div class="checklist"]
> * パイプラインで基本的な ML 実験を作成する
> * パラメーター化されたバッチ推論パイプラインを作成する
> * パイプラインを手動または REST エンドポイントから管理し、実行する

## <a name="prerequisites"></a>前提条件

1. Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

1. [ワークスペース](tutorial-1st-experiment-sdk-setup.md)を作成します。

1. [Azure Machine Learning Studio](https://ml.azure.com/) にサインインします。

このガイドでは、デザイナーで単純なパイプラインを構築するための基本的知識を前提としています。 デザイナーのガイド付き説明については、[こちら](tutorial-designer-automobile-price-train-score.md)のチュートリアルを完了してください。 

## <a name="create-a-pipeline"></a>パイプラインを作成する

バッチ推論パイプラインを作成するには、まず、機械学習実験が必要です。 新しいパイプラインは、ワークスペースの **[デザイナー]** タブに移動し、 **[Easy-to-use prebuilt modules]\(あらかじめ構築された使いやすいモジュール\)** オプションを選択して作成します。

![デザイナー ホーム](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-1.png)

次は実演目的の単純な機械学習モデルです。 データは、Azure Open Datasets 糖尿病データから作成された登録済みデータセットです。 Azure Open Datasets からデータセットを登録する方法については、[ガイド セクション](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)を参照してください。 データはトレーニング セットと検証セットに分割され、増幅されたデシジョン ツリーがトレーニングされ、スコアが付けられます。 推論パイプラインを作成するには、パイプラインを少なくとも 1 回実行する必要があります。 パイプラインを実行するには、 **[実行]** ボタンをクリックします。

![簡単な実験を作成する](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-2.png)

## <a name="create-a-batch-inference-pipeline"></a>バッチ推論パイプラインを作成する

パイプラインが実行されたので、 **[実行]** と **[公開]** の横で **[Create inference pipeline]\(推論パイプラインの作成\)** という新しいオプションが利用できるようになりました。 ドロップダウンをクリックし、 **[Batch inference pipeline]\(バッチ推論パイプライン\)** を選択します。

![バッチ推論パイプラインを作成する](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-5.png)

結果的に既定のバッチ推論パイプラインが与えられます。 これには、元のパイプライン実験セットアップのノード、スコア付けのための生データのノード、元のパイプラインに対して生データにスコアを付けるためのノードが含まれます。

![既定のバッチ推論パイプライン](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-6.png)

他のノードを追加し、バッチ推論プロセスの動作を変更できます。 この例では、スコア付け前に入力データからランダムでサンプルを抽出するためのノードを追加します。 **[Partition and Sample]** ノードを作成し、それを生データとスコア付けノードの間に置きます。 次に **[Partition and Sample]** ノードをクリックし、設定とパラメーターにアクセスできるようにします。

![新しいノード](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-7.png)

*[Rate of sampling]\(サンプリング率\)* パラメーターでは、元のデータ セットからランダムにサンプルを抽出するパーセントが制御されます。 これは頻繁に調整する目的に便利なパラメーターであり、パイプライン パラメーターとして有効にします。 パイプライン パラメーターは実行時に変更できて、REST エンドポイントからパイプラインを再実行するとき、ペイロード オブジェクトに指定できます。 

パイプライン パラメーターとしてこのフィールドを有効にするには、フィールドの上にある省略記号をクリックし、 **[Add to pipeline parameter]\(パイプライン パラメーターに追加する\)** をクリックします。 

![サンプル設定](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-8.png)

次に、パラメーターに名前を付け、既定値を指定します。 この名前は、パラメーターを識別し、REST 呼び出しに指定する目的で使用されます。

![パイプライン パラメーター](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-9.png)

## <a name="deploy-batch-inferencing-pipeline"></a>バッチ推論パイプラインをデプロイする

以上でパイプラインをデプロイする準備が整いました。 **[デプロイ]** ボタンをクリックします。エンドポイントを設定するためのインターフェイスが開きます。 ドロップダウンをクリックし、 **[New PipelineEndpoint]\(新しいパイプライン エンドポイント\)** を選択します。

![パイプライン デプロイ](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-10.png)

エンドポイントに名前と任意で説明を指定します。 下部の近くに、既定値 0.8 で構成した `sample-rate` パラメーターが表示されます。 準備ができたら、 **[デプロイ]** をクリックします。

![エンドポイントを設定する](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-11.png)

## <a name="manage-endpoints"></a>エンドポイントを管理する 

デプロイが完了したら、 **[エンドポイント]** タブに移動し、先ほど作成したエンドポイントの名前をクリックします。

![エンドポイント リンク](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-12.png)

この画面には、特定のエンドポイントの下で公開されたすべてのパイプラインが表示されます。 推論パイプラインをクリックします。

![推論パイプライン](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-13.png)

パイプライン詳細ページには、パイプラインの詳細な実行履歴と接続文字列情報が表示されます。 **[実行]** ボタンをクリックし、パイプラインの手動実行を作成します。

![パイプラインの詳細](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-14.png)

実行セットアップでは、実行の説明を入力したり、パイプライン パラメーターの値を変更したりできます。 今回は、サンプル レート 0.9 で推論パイプラインを再実行します。 **[実行]** をクリックし、パイプラインを実行します。

![パイプラインの実行](./media/how-to-run-batch-predictions-designer/designer-batch-scoring-15.png)

**[使用]** タブには、パイプラインを再実行するための REST エンドポイントが含まれています。 REST を呼び出すには、OAuth 2.0 ベアラー型認証ヘッダーが必要です。 ワークスペースの認証を設定し、パラメーター化された REST を呼び出す方法については、次の[チュートリアル セクション](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)をご覧ください。

## <a name="next-steps"></a>次のステップ

デザイナー [チュートリアル](tutorial-designer-automobile-price-train-score.md)に従い、回帰モデルをトレーニングし、デプロイします。
