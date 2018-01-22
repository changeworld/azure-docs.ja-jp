---
title: "Azure での Team Data Science Process を使用した単語埋め込みによる Twitter センチメントの予測 | Microsoft Docs"
description: "データ サイエンス プロジェクトを実行するために必要な手順について説明します。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: df1124ddb436f0cfeec8f4ed9728fa15278b9325
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Team Data Science Process を使用した単語埋め込みによる Twitter センチメントの予測

この記事では、[Azure Machine Learning](../preview/index.yml) で _Word2Vec_ 単語埋め込みアルゴリズムと _SSWE (Sentiment-Specific Word Embedding: セマンティック固有単語埋め込み)_ アルゴリズムを使用して、Twitter センチメントの予測を効率的にコラボレーションする方法を示します。 Twitter センチメントの極性に関する予測の詳細については、GitHub の [MachineLearningSamples-TwitterSentimentPrediction リポジトリ](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction)を参照してください。 データ サイエンス プロジェクトでのチームの効果的なコラボレーションを促進する鍵は、プロジェクトの構造とドキュメントを、確立されたデータ サイエンス ライフサイクルで標準化することです。 [Team Data Science Process (TDSP)](overview.md) は、この種の構造化された[ライフ サイクル](lifecycle.md)を提供します。 

"_TDSP テンプレート_" を使用してデータ サイエンス プロジェクトを作成すると、Azure Machine Learning プロジェクト用の標準化されたフレームワークが用意されます。 これまで、TDSP チームは、[TDSP プロジェクトの構造とテンプレート用 GitHub リポジトリ](https://github.com/Azure/Azure-TDSP-ProjectTemplate)をリリースしていました。 現在、[Azure Machine Learning の TDSP テンプレート](https://github.com/amlsamples/tdsp)を使用してインスタンス化されている Machine Learning プロジェクトが有効になっています。 手順については、Azure Machine Learning の [TDSP テンプレートによる TDSP 構造プロジェクト](../preview/how-to-use-tdsp-in-azure-ml.md)の使用方法を参照してください。 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter センチメント極性サンプル

この記事では、サンプルを使用して、Machine Learning プロジェクトをインスタンス化して実行する方法を示します。 このサンプルでは、Azure Machine Learning Workbench で TDSP 構造とテンプレートを使用します。 完全なサンプルは、[こちらのチュートリアル](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md)に用意されています。 モデリング タスクは、ツイートのテキストを使用してセンチメント極性 (肯定または否定) を予測します。 この記事では、チュートリアルで説明しているデータ モデリング タスクについて説明します。 チュートリアルに含まれるタスクは次のとおりです。

- ユース ケースの概要で説明されている予測問題に対処する機械学習モデルのデータ探索、トレーニング、および配置。 これらのタスクで、[Twitter センチメント データ](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)が使用されます。
- このプロジェクト用の TDSP テンプレートの使用による Azure Machine Learning からのプロジェクトの実行。 プロジェクトの実行とレポートでは、TDSP ライフサイクルが使用されます。
- Azure Container Service の Azure Machine Learning からの直接的なソリューションの運用化。

プロジェクトは、Azure Machine Learning の次の機能に注目しています。

- TDSP 構造のインスタンス化と使用。
- Azure Machine Learning Workbench でのコードの実行。
- Docker と Kubernetes の使用によるコンテナー サービスでの容易な運用化。

## <a name="team-data-science-process"></a>Team Data Science Process
このサンプルを実行するには、Azure Machine Learning Workbench で TDSP プロジェクト構造とドキュメント テンプレートを使用します。 サンプルは、次の図に示すように、[TDSP ライフ サイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)を実装します。

![TDSP ライフサイクル](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

TDSP プロジェクトは、次の図に示すように、[こちらの手順](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)に従って Azure Machine Learning Workbench で作成されます。

![Azure Machine Learning Workbench で TDSP を作成する](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[データの取得と準備](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
このサンプルの最初の手順は、sentiment140 データセットをダウンロードし、データをトレーニング データセットとテスト データセットに分割することです。 sentiment140 データセットには、ツイートの実際の内容が含まれています (絵文字は削除されています)。 データセットには、各ツイートの極性 (否定 = 0、肯定 = 4) も含まれ、どちらでもないツイートは削除されています。 データ分割後、トレーニング データには 1,300,000 行のデータが、テスト データには 320,000 行のデータがあります。


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[モデルの開発](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

サンプルでの次の手順は、データのモデルを開発することです。 モデリング タスクは、3 つの部分に分かれます。

- 特徴エンジニアリング: 異なる単語埋め込みアルゴリズムを使用して、モデルの特徴を生成します。 
- モデルの作成: 異なるモデルをトレーニングして、入力テキストのセンチメントを予測します。 これらのモデルの例として、"_ロジスティック回帰_" と "_勾配ブースティング_" があります。
- モデルの評価: トレーニング モデルをテスト データで評価します。


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[特徴エンジニアリング](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec アルゴリズムと SSWE アルゴリズムを使用して、単語の埋め込みを生成します。 


#### <a name="word2vec-algorithm"></a>Word2Vec アルゴリズム

Word2Vec アルゴリズムは、Skip-Gram モデルで使用されます。 このモデルは、Tomas Mikolov 他が 2013 年に執筆した論文 "[Distributed Representations of Words and Phrases and their Compositionality.Advances in neural information processing systems.](https://arxiv.org/abs/1310.4546)" (単語および句の分散表現および合成。ニューラル情報処理システムにおける進化) で 説明されています。

Skip-Gram モデルは、浅いニューラル ネットワークです。 入力は one-hot ベクトルとしてエンコードされたターゲット 単語であり、これを使用して近接する単語が予測されます。 **V** がボキャブラリのサイズであれば、出力層のサイズは、__C * V__ になります (C は文脈ウィンドウのサイズです)。 次の図は、Skip-Gram モデルに基づくアーキテクチャを示しています。

![Skip-Gram モデル](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Word2Vec アルゴリズムと Skip-Gram モデルの詳細なしくみについては、このサンプルの範囲を超えています。 詳細については、次のリファレンスを参照してください。

- [02_A_Word2Vec.py code with referenced TensorFlow examples](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
- [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>SSWE (Sentiment-Specific Word Embedding) アルゴリズム
SSWE アルゴリズムは、文脈は似ているが極性は反対である単語が同じような単語ベクトルになる可能性がある Word2Vec アルゴリズムの弱点を克服しようとしています。 類似性が原因で、Word2Vec アルゴリズムでは、感情分析などのタスクが正確に実行されない可能性があります。 SSWE アルゴリズムは、文の極性と単語の文脈の両方を損失関数に組み込むことで、この弱点を処理しようとしています。

サンプルでは、SSWE アルゴリズムの異形を次のように使用しています。

- 元の _ngram_ と破損した _ngram_ の両方が入力として使用される。
- シンタックス損失とセマンティック損失の両方で、ランキング型のヒンジ損失関数が使用される。
- 最終的な損失関数は、シンタックス損失とセマンティック損失の両方の重みづけされた組み合わせである。
- 単純化するために、セマンティック クロス エントロピーのみが損失関数として使用される。

サンプルでは、単純な損失関数であっても、Word2Vec 埋め込みよりも SSWE 埋め込みのほうが高いパフォーマンスを示しています。 次の図は、センチメント固有単語埋め込みを生成するために使用される畳み込みモデルを示しています。

![SSWE に触発されたニューラル ネットワーク モデル](./media/predict-twitter-sentiment/embedding-model-2.PNG)

トレーニング処理が完了すると、モデリング ステージ用の 2 つの埋め込みファイルがタブ区切り値 (TSV) 形式で生成されます。

SSWE アルゴリズムの詳細については、Duyu Tang 他が 2014 年に執筆した論文 "[Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)" (Twitter センチメント分類のためのセマンティック固有単語埋め込みについて)  Association for Computational Linguistics (1)  を参照してください。


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[モデルの作成](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
SSWE または Word2Vec アルゴリズムを使用して単語ベクトルが生成された後、実際のセンチメント極性を予測するために分類モデルがトレーニングされます。 2 種類の特徴 (Word2Vec と SSWE) が、2 つのモデル (勾配ブースティング モデルとロジスティック回帰モデル) に適用されます。 その結果、4 つの異なるモデルがトレーニングされます。


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[モデルの評価](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
モデルがトレーニングされた後、それらのモデルを使用して、Twitter テキスト データのテストと各モデルのパフォーマンス評価が実行されます。 サンプルでは、次の 4 つのモデルを評価します。

- SSWE 埋め込みの勾配ブースティング。
- SSWE 埋め込みのロジスティック回帰。
- Word2Vec 埋め込みの勾配ブースティング。
- Word2Vec 埋め込みのロジスティック回帰。

次の表は、4 つのモデルのパフォーマンスの比較を示しています。

![受信者操作特性 (ROC) モデルの比較](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

曲線下面積 (AUC) メトリックを使用してモデルを比較した場合、SSWE 特徴を使用する勾配ブースティング モデルが最高のパフォーマンスを示しています。


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[デプロイ](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

最後の手順は、トレーニングされたセンチメント予測モデルを Azure Container Service 内のクラスターの Web サービスに配置することです。 サンプルでは、トレーニング モデルとして SSWE 埋め込みアルゴリズムを使用した勾配ブースティング モデルを使用しています。 運用化環境では、次の図に示すように、Docker と Kubernetes をクラスターにプロビジョニングして、Web サービスのデプロイを管理します。 

![Kubernetes ダッシュボード](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

運用化プロセスの詳細については、「[Azure Machine Learning モデルを Web サービスとしてデプロイする](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy)」を参照してください。

## <a name="conclusion"></a>まとめ

この記事では、Word2Vec アルゴリズムと SSWE アルゴリズムを使用して、単語埋め込みモデルをトレーニングする方法について説明しました。 抽出された埋め込みを特徴として使用して、Twitter テキスト データのセンチメント スコアを予測するさまざまなモデルをトレーニングしました。 勾配ブースティング モデルで使用された SSWE 特徴が、最高のパフォーマンスを示しました。 このモデルが、Azure Machine Learning Workbench を使用して、リアルタイム Web サービスとして Container Service に配置されました。


## <a name="references"></a>参照

* [Team Data Science Process とは](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Azure Machine Learning での Team Data Science Process (TDSP) の使用](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning の TDSP プロジェクト テンプレート](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [UCI ML リポジトリからの米国収入データセット](https://archive.ics.uci.edu/ml/datasets/adult)
* [TDSP テンプレートを使った生体エンティティ認識](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas 他 "Distributed Representations of Words and Phrases and their Compositionality.Advances in neural information processing systems." 2013 年](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu 他。"Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification." ACL (1) 2014 年](http://www.aclweb.org/anthology/P14-1146)
