---
title: Azure Machine Learning (AML) package for text analytics (AMLPTA) と Team Data Science Process (TDSP) を使用した Twitter のセンチメント分類 | Microsoft Docs
description: TDSP (Team Data Science Process) と AMLPTA のセンチメント分類での使用について説明します
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 9e5018bc4c7b90897f7f8c91169410284217b172
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577009"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Azure Machine Learning (AML) package for text analytics (AMLPTA) と Team Data Science Process (TDSP) を使用した Twitter のセンチメント分類

## <a name="introduction"></a>はじめに
規定された[データ サイエンス ライフサイクル](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)に基づいたデータ サイエンス プロジェクトの構造とドキュメントの標準化は、データ サイエンス チームの効果的なコラボレーションを促進する鍵です。

以前にも、[TDSP プロジェクトの構造とテンプレートの GitHub リポジトリ](https://github.com/Azure/Azure-TDSP-ProjectTemplate)はリリースされていました。 [Azure Machine Learning ための TDSP 構造とドキュメント テンプレート](https://github.com/amlsamples/tdsp)を使ってインスタンス化された Azure Machine Learning プロジェクト を作成できるようになりました。 Azure Machine Learning で TDSP 構造とテンプレートを使用する方法については[ここ](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml)をご覧ください。 

このサンプルでは、Azure Machine Learning Package for Text Analytics と TDSP を使用して、Twitter のセンチメント分類の予測モデルを開発して展開する方法について説明します。


## <a name="use-case"></a>ユース ケース
### <a name="twitter-sentiment-polarity-sample"></a>Twitter センチメント極性サンプル
この記事では、サンプルを使用して、Machine Learning プロジェクトをインスタンス化して実行する方法を示します。 このサンプルでは、Azure Machine Learning Workbench で TDSP 構造とテンプレートを使用します。 完全なサンプルは、このチュートリアルに用意されています。 モデリング タスクは、ツイートのテキストを使用してセンチメント極性 (肯定または否定) を予測します。 この記事では、チュートリアルで説明しているデータ モデリング タスクについて説明します。 チュートリアルに含まれるタスクは次のとおりです。

1. ユース ケースの概要で説明されている予測問題に対処する機械学習モデルのデータ探索、トレーニング、および配置。 これらのタスクでは、Twitter センチメント データが使用されます。
2. このプロジェクト用の TDSP テンプレートの使用による Azure Machine Learning からのプロジェクトの実行。 プロジェクトの実行とレポートでは、TDSP ライフサイクルが使用されます。
3. Azure Container Service の Azure Machine Learning からの直接的なソリューションの運用化。

このプロジェクトでは、Azure Machine Learning に Text Analytics Package を使用する方法を取り上げています。


## <a name="link-to-github-repository"></a>GitHub リポジトリへのリンク
GitHub リポジトリへのリンクは[こちら](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)です。 

### <a name="purpose"></a>目的
このサンプルの主な目的は、Team Data Science Process (TDSP) 構造と Azure Machine Learning Work Bench のテンプレートを使用して、機械学習プロジェクトをインスタンス化し実行する方法について説明することです。 この目的で、[Twitter センチメント データ](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip)を使用します。 モデリング タスクとは、ツイートのテキストを使用してセンチメント極性 (肯定または否定) を予測することです。

### <a name="scope"></a>Scope (スコープ)
- ユース ケースの概要で説明されている予測問題に対処する機械学習モデルのデータ探索、トレーニング、および配置。
- このプロジェクトのために、Azure Machine Learning から Team Data Science Process (TDSP) テンプレートを使用して Azure Machine Learning でプロジェクトを実行します。 プロジェクトの実行とレポートには、TDSP ライフサイクルを使用します。
- ソリューションは、Azure Container Services の Azure Machine Learning から直接、運用化します。

プロジェクトでは、TDSP 構造のインスタンス化と使用などの Azure Machine Learning の機能、Azure Machine Learning Work Bench でのコードの実行、および Docker と Kubernetes を使用した Azure Container Services の簡単な運用化について説明します。

## <a name="team-data-science-process-tds"></a>Team Data Science Process (TDS)
このサンプルを実行するには、TDSP プロジェクト構造とドキュメント テンプレートを使用します。 このサンプルは [TDSP ライフサイクル](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle)に従います。 このプロジェクトは、[こちら](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md)の手順に従って作成されています。


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>ユース ケースの概要
このタスクは、Twitter テキストから抽出された単語の埋め込み機能を使用して、各 Twitter のセンチメント バイナリの極性を予測することです。 詳細については、こちらの[リポジトリ](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction)を参照してください。

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[データの取得と理解](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
このサンプルの最初の手順は、sentiment140 データセットをダウンロードし、それをトレーニング データセットとテスト データセットに分割することです。 Sentiment140 データセットには、ツイートの実際の内容 (顔文字を削除したもの) と、各ツイートの極性 (否定 = 0、肯定 = 4) が含まれています。 結果のトレーニング データには 130 万行、テスト データには 32 万行あります。

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[モデリング](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
サンプルのこの部分はさらに 3 つの部分に分かれます。 
- **特徴エンジニアリング**。異なる単語埋め込みアルゴリズム (Word2Vec など) を使用する特徴の生成に対応します。 
- **モデルの作成**。入力テキストのセンチメントを予測するために、_ロジスティック回帰_や_勾配ブースティング_などのさまざまなモデルのトレーニングを扱います。 
- **モデルの評価**。テスト データにトレーニング モデルを適用します。

#### <a name="feature-engineering"></a>特徴エンジニアリング
単語の埋め込みの生成には、<b>Word2Vec</b> を使用しています。 まず、Skip-gram モードで Word2Vec アルゴリズムを使用します。このとき、Tomas Mikolov などが執筆した論文「[Distributed representations of words and phrases and their compositionality.Advances in neural information processing systems.2013 年](https://arxiv.org/abs/1310.4546) ) に従って単語の埋め込みを生成します。

Skip-gram は、入力に 1 つのホット ベクターとしてエンコードされたターゲット単語を取得し、それを使用して近い単語を予測する浅いニューラル ネットワークです。 V がボキャブラリのサイズであれば、出力層のサイズは、__C*V__ になります (C は文脈ウィンドウのサイズです)。 Skip-gram ベースのアーキテクチャは、次の図のようになります。
 
<table class="image" align="center">
<caption align="bottom">Skip-gram モデル</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Word2Vec アルゴリズムと Skip-gram モデルの詳細については、このサンプルで扱いません。興味がある場合、詳細については、次のリンクを参照してください。 コード 02_A_Word2Vec.py で参照される [tensorflow の例](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Vector Representation of Words](https://www.tensorflow.org/tutorials/word2vec)
* [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

トレーニング プロセスが完了すると、モデリング段階のために TSV 形式の 2 つの埋め込みファイルが生成されます。

#### <a name="model-training"></a>モデル トレーニング
単語ベクターを SSWE または Word2vec アルゴリズムのいずれかを使用して生成したら、次の手順は、実際のセンチメントの極性を予測するために分類モデルをトレーニングすることです。 ここでは、Word2Vec と SSWE の 2 種類の特徴をロジスティック回帰と畳み込みニューラル ネットワーク (CNN) の 2 つのモデルに適用します。 

#### <a name="model-evaluation"></a>モデルの評価
ここでは、テスト データ セットに対して複数のトレーニングされたモデルを読み込んで評価する方法に関するコードを用意しています。


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Deployment](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
このパートでは、Azure Container Service (AKS) のクラスター上で Web サービスに対するトレーニング済みのセンチメント予測モデルを運用化する方法について手順の指針を示します。 運用化環境では、クラスターで Docker と Kubernetes をプロビジョニングして、Web サービスのデプロイを管理します。 運用化のプロセスの詳細については、[こちら](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy)を参照してください。

## <a name="conclusion"></a>まとめ
この記事では、Word2Vec を使用して単語の埋め込みモデルをトレーニングし、抽出された埋め込みを 2 つの異なるモデルをトレーニングするための特徴として使用して、Twitter テキスト データのセンチメント スコアを予測する方法について詳しく説明しました。 これらのモデルの 1 つは Azure Container Services (AKS) に導入されています。 

## <a name="next-steps"></a>次の手順
まず [Azure Machine Learning Package for Text Analytics (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) と [Team Data Science Process (TDSP)](https://aka.ms/tdsp) に関するその他のドキュメントを参照してください。

## <a name="references"></a>参照
* [Team Data Science Process とは](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Azure Machine Learning での Team Data Science Process (TDSP) の使用](https://aka.ms/how-to-use-tdsp-in-aml)
* [Azure Machine Learning の TDSP プロジェクト テンプレート](https://aka.ms/tdspamlgithubrepo)
* [Azure ML Work Bench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas 他 Distributed representations of words and phrases and their compositionality.Advances in neural information processing systems.2013 年](https://arxiv.org/abs/1310.4546)
