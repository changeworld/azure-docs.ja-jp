---
title: デザイナーのパイプラインのサンプル
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーのサンプルを使用することで、機械学習パイプラインをすぐに開始できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389343"
---
# <a name="designer-sample-pipelines"></a>デザイナーのサンプル パイプライン

Azure Machine Learning デザイナーの組み込みサンプルを使用すると、独自の機械学習パイプラインの構築をすぐに開始できます。 Azure Machine Learning デザイナーの [GitHub リポジトリ](https://github.com/Azure/MachineLearningDesigner)には、一般的な機械学習のシナリオを理解するのに役立つ詳細なドキュメントが含まれています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://aka.ms/AMLFree)を作成してください。
* Enterprise SKU の Azure Machine Learning ワークスペース。


## <a name="how-to-use-sample-pipelines"></a>サンプル パイプラインの使用方法

デザイナーによって、サンプル パイプラインのコピーが Studio ワークスペースに保存されます。 ニーズに合うようにパイプラインを編集し、独自のものとして保存できます。 これらをプロジェクトを開始するための出発点として使用します。

### <a name="open-a-sample-pipeline"></a>サンプル パイプラインを開く

1. <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> にサインインし、使用するワークスペースを選択します。

1. **[デザイナー]** を選択します。

1. **[新しいパイプライン]** セクションでサンプル パイプラインを選択します。

    サンプルの完全な一覧については、 **[Show more samples]\(その他のサンプルを表示する\)** を選択してください。

### <a name="submit-a-pipeline-run"></a>パイプラインの実行を送信する

パイプラインを実行するには、最初に、パイプラインを実行する既定のコンピューティング先を設定する必要があります。

1. キャンバスの右側にある **[設定]** ペインで、 **[コンピューティング先を選択]** を選択します。

1. 表示されたダイアログで、既存のコンピューティング先を選択するか、新しく作成します。 **[保存]** を選択します。

1. キャンバス上部の **[送信]** を選択して、パイプラインの実行を送信します。

サンプル パイプラインとコンピューティング設定によっては、実行が完了するまでに時間がかかることがあります。 既定のコンピューティング設定の最小ノード サイズは 0 です。これは、アイドル状態になった後に、デザイナーによってリソースが割り当てられる必要があることを意味します。 コンピューティング リソースが既に割り当てられているため、パイプラインの反復実行にかかる時間は短くなります。 さらにデザイナーでは、各モジュール用にキャッシュされた結果を使用して、効率を向上させます。


### <a name="review-the-results"></a>結果の確認

パイプラインの実行が完了したら、パイプラインをレビューし、各モジュールの出力を表示して詳細を確認できます。

モジュールの出力を表示するには、次の手順を使用します。

1. キャンバス内のモジュールを選択します。

1. キャンバスの右側にある [モジュールの詳細] ペインで **[Outputs + logs]\(出力 + ログ\)** を選択します。 グラフ アイコン (![可視化アイコン](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png)) を選択すると、各モジュールの結果が表示されます。 

このサンプルは、最も一般的な機械学習シナリオの一部の出発点として使用してください。

## <a name="regression-samples"></a>回帰のサンプル

組み込みの回帰のサンプルの詳細を確認します。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 1: 回帰 - 自動車価格の予測 (基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | 線形回帰を使用して自動車の価格を予測します。 |
| [サンプル 2: 回帰 - 自動車価格の予測 (詳細)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | デシジョン フォレストとブースト デシジョンツリー リグレッサーを使用して、自動車の価格を予測します。 モデルを比較して、最適なアルゴリズムを見つけてください。

## <a name="classification-samples"></a>分類のサンプル

組み込みの分類のサンプルの詳細を確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 3: 特徴の選択による二項分類 - 収入の予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | 2 クラスのブースト デシジョン ツリーを使用して、収入を高または低として予測します。 ピアソンの相関関係を使用して、特徴を選択します。
| [サンプル 4: カスタム Python スクリプトを使用した二項分類 - 信用リスクの予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | クレジット申込書を高リスクまたは低リスクとして分類します。 Python スクリプトの実行モジュールを使用して、データを重み付けします。
| [サンプル 5: 二項分類 - 顧客関係の予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | 2 クラス ブースト デシジョン ツリーを使用して、顧客離反を予測します。 偏りのあるデータをサンプリングするには、SMOTE を使用します。
| [サンプル 7: テキスト分類 - Wikipedia SP 500 データセット](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | 多クラス ロジスティック回帰を使用して、Wikipedia の記事から会社の種類を分類します。 |
| サンプル 12: 多クラス分類 - 文字認識 | 一連の二項分類器を作成して、手書きの文字を分類します。 |

## <a name="recommender-samples"></a>レコメンダーのサンプル

組み込みのレコメンダーのサンプルの詳細を確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| サンプル 10: レコメンデーション - 映画の評価ツイート | 映画のタイトルと評価から映画レコメンダー エンジンを作成します。 |

## <a name="utility-samples"></a>ユーティリティのサンプル

機械学習のユーティリティと機能を示すサンプルの詳細を確認します。 ドキュメント リンクのないサンプルの詳細については、代わりにサンプルを開いてモジュールのコメントをご確認ください。

| サンプル タイトル | 説明 | 
| --- | --- |
| [サンプル 6: カスタム R スクリプトの使用 - フライトの遅延予測](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| サンプル 8: 二項分類のクロス検証 - 成人の収入予測 | クロス検証を使用して、成人の収入予測のための二項分類器を作成します。
| サンプル 9: 順列の特徴量の重要度 | 順列の特徴量の重要度を使用して、テスト データセットの重要度スコアを計算します。 
| サンプル 11: 二項分類のパラメーターの調整 - 成人の収入予測 | モデルのハイパーパラメーターの調整を使用して、バイナリ分類器の作成に最適なハイパーパラメーターを見つけます。 |

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>次のステップ

次のチュートリアルを使用して、機械学習モデルを構築してデプロイする方法を確認します。[チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)
