<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Machine Learning サンプル: 信用リスク予測 | Azure" description="A sample Azure Machine Learning experiment to develop a binary classification model that predicts if an applicant is a low credit risk or a high credit risk." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Machine Learning サンプル: 信用リスク予測 

>[AZURE.NOTE]
>ML Studio には、このモデルに対応した[サンプル実験]と[サンプル データセット]が用意されています。詳細については、以下を参照してください。
[サンプル実験]: #sample-experiment
[サンプル データセット]: #sample-dataset

*この実験の簡略版を作成して使用する方法の詳細なチュートリアルについては、「[Azure Machine Learning を使用した予測ソリューションの開発](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/)」を参照してください。*

この実験の目的は、信用貸付の申請書に記入された情報を基に、個人の信用リスクを予測することです。予測は、2 つの値、低リスクと高リスクをとります。 


<!-- Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment â€" for experimenting with different models
- Training Experiment â€" to train the one chosen model
- Scoring Experiment â€" to set up a web service using the trained model
-->

<!-- Removed because we added a section at the bottom describing the dataset
##Dataset Description

The experiment uses the UCI Statlog (German Credit Card) dataset which can be found here: 
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>. 
この Web サイトからの german.data ファイルを使用しています。

データセットは、低信用リスクまたは高信用リスクとして属性のセットで表現された人々を分類します。サンプルはそれぞれ人を表します。20 の特徴があり、数値とカテゴリの両方と、二項のラベル (信用リスク値) で構成されます。高信用リスクのエントリのラベルは 2、低信用リスクのエントリのラベルは 1 です。低リスクを高リスクに誤って分類した場合のコストが 1 であるのに対して、高リスクを低リスクに誤って分類した場合のコストは 5 です。
-->

##実験の開発

元のデータセットは、空白で区切られた形式を使用しています。このデータセットを CSV 形式に変換し、ML Studio にアップロードしました。この変換処理は、次のように、PowerShell を使用して実行できます。 

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

または、Unix の sed コマンドを使用して実行することもできます。

	sed 's/ /,/g' german.data > german.csv

まず、**メタデータ エディター**を使用して列名を追加し、データセットの既定の列名を、UCI のサイトでデータセットの説明から取得した意味のある名前に置き換えます。新しい列名は、**メタデータ エディター**の **[新しい列名]** 内にあり、コンマで区切られています。

次に、リスク予測モデルの開発に使用されるトレーニング セットとテスト セットを生成します。**[1 番目の出力行数の入力率]** を 0.5 に設定して**分割**モジュールを使用し、元のデータセットを同じサイズのトレーニング セットとテスト セットに分割します。
 
高リスクのサンプルを低リスクに誤って分類したときのコストは、低リスクのサンプルを高リスクに誤って分類したときのコストの 5 倍大きいため、この費用関数を反映した新しいデータセットを生成します。新しいデータセットでは、高リスクのサンプルを 5 回レプリケートし、低リスクのサンプルはそのままの状態にします。同じサンプルがレーニングとテストのデータセットの両方に存在することを避けるため、このレプリケーションの前に、トレーニングとテストのデータセットの分割を実行します。 

このレプリケーションは、**R スクリプトの実行**モジュールを使用して次の R コードで実行します。

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

実験では、モデルを生成するための 2 つのアプローチを比較します。元のデータセットに対するトレーニングと、レプリケートしたデータセットに対するトレーニングです。両方のアプローチで、問題の費用関数と連携するために、レプリケートしたテスト セットでテストします。分割とレプリケーションの最終的なワークフローは、下のようになります。このワークフローでは、**分割**モジュールの左側の出力内容がトレーニング セットで、右側の出力内容がテスト セットです。トレーニング セットはその後、**R スクリプトの実行**の有無の両方、つまりレプリケーションの有無の両方の場合に使用されます。

![Splitting training and test data][screen1]
 
トレーニング セットのサンプルのレプリケーションの効果の確認だけでなく、2 つのアルゴリズムのパフォーマンスも比較します。そのアルゴリズムとは、サポート ベクター マシン (SVM) とブースト デシジョン ツリーです。次のように、実質的に 4 つのモデルを生成します。

- SVM、元のデータでトレーニング済み
- SVM、レプリケートしたデータでトレーニング済み
- ブースト デシジョン ツリー、元のデータでトレーニング済み
- ブースト デシジョン ツリー、レプリケートしたデータでトレーニング済み

ブースト デシジョン ツリーはあらゆる種類の特徴で問題なく使うことができます。ただし、SVM モジュールは線形分類器を生成するため、生成されたモデルでは、すべての特徴が同じスケールのときにテストの誤差が最良になります。すべての特徴を同じスケールに変換するために、**スケーリングによるデータ変換**モジュールと Tanh 変換を使用します。この変換によって、すべての数値特徴が [0,1] の範囲に変換されます。文字列特徴は SVM モジュールによってカテゴリ特徴に変換され、バイナリ 0/1 特徴に変換されます。このため文字列特徴を手動で変換する必要はありません。 

**2 クラス サポート ベクター マシン** モジュールまたは **2 クラス ブースト デシジョン ツリー** モジュールを使用して学習アルゴリズムを初期化し、次に**モデルのトレーニング** モジュールを使用して実際のモデルを作成します。これらのモデルは**モデルのスコア付け**モジュールで使用され、テスト サンプルのモデルのスコアを作成します。これらのモジュールを結び付け、SVM とレプリケートしたトレーニング セットを使用するサンプル ワークフローは次のようになります。**モデルのトレーニング**はトレーニング セットに接続されますが、**モデルのスコア付け**はテスト セットに接続されます。

![Training and scoring a model][screen2]

実験の評価段階で、上の 4 つの各モデルの精度を計算します。そのために、**モデル評価**モジュールを使用します。このモジュールは精度の計算のみを実行し、すべてのサンプルに同じ誤分類のコストが発生します。ただし、あらかじめ正のサンプルがレプリケートされているため、**モデル評価**によって計算された精度は、費用の影響を受けます。 

![Accuracy computation][formula]

ここでは、*n+* と *n-* は元のデータセットの正と負のサンプルの数であり、*e+* と *e-* は元のデータセットの誤分類された正と負のサンプルの数です。

**モデル評価**モジュールはスコアが付けられた 2 つのモデルを比較するため、1 つの**モデル評価**モジュールを使用して 2 つの SVM モデルを比較し、1 つのモジュールで 2 つのブースト デシジョン ツリー モデルを比較します。これらを組み合わせてテーブルに 4 つの結果すべてを表示します。**モデル評価**によって、さまざまなメトリックを含む単一行を持つテーブルが作成されます。**行追加**モジュールを使用してすべての結果を 1 つのテーブルに結合します。**R スクリプトの実行**モジュールで R スクリプトを使用して、4 つのモジュールの精度によってテーブルに注釈を付けます。ここで、最後のテーブルの行の名前を手動で入力します。最後に、**プロジェクト列**モジュールを使用して、関連しないメトリックの列を削除します。 

実験の最終結果は、**プロジェクト列**の **[結果データセット]** の出力を右クリックすると取得できます。内容は次のとおりです。

![Results][results] 

ここで、1 番目の列はモデルを生成するために使用された機械学習アルゴリズムの名前であり、2 番目の列はトレーニング セットの種類を示し、3 番目の列は費用の影響を受ける精度です。この実験では、SVM モデルがレプリケーション済みのトレーニング データセットを処理して、最も高い精度を記録しました。

<!-- Removed until the Training and Scoring parts are fixed
##Training Experiment

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model. 

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is â€œdatascienceâ€ and the dataset file â€œgerman.csvâ€ is placed in container â€œsampleexperimentsâ€. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.

![Azure storage parameters][screen3] 

##Scoring Experiment

The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (â€œCredit Risk modelâ€) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model. 

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**. 

After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.

When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.

![Web service ready for production][screen4] 
-->

## サンプル実験

ML Studio には、このモデルに対応した次のサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。

> **サンプル実験 - ドイツのクレジット - 開発**


## サンプル データセット

ML Studio には、この実験で使用される次のサンプル データセットが用意されています。モジュール パレットの **[保存されたデータセット]** にあります。

###ドイツのクレジット カード UCI データセット
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]



[screen1]:./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[screen2]:./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[formula]:./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[results]:./media/machine-learning-sample-credit-risk-prediction/results.jpg
[screen3]:./media/machine-learning-sample-credit-risk-prediction/screen3.jpg
[screen4]:./media/machine-learning-sample-credit-risk-prediction/screen4.jpg
