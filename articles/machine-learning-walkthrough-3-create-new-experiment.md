<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="手順 3.Machine Learning の新しい実験を作成する | Azure" description="手順 3.Azure Machine Learning Studio で新しいトレーニング実験を作成する" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/02/2014" ms.author="garye" />


これは、チュートリアル「[Azure Machine Learning を使用した予測ソリューションの開発][develop]」の 3 番目の手順です。

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[ML ワークスペースを作成する][create-workspace]
2.	[既存のデータをアップロードする][upload-data]
3.	**新しい実験を作成する**
4.	[モデルをトレーニングして評価する][train-models]
5.	[Web サービスを発行する][publish]
6.	[Web サービスにアクセスする][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# 手順 3.Azure Machine Learning の新しい実験を作成する

アップロードしたデータセットを使用する実験を ML Studio で作成する必要があります。  

1.	ML Studio で、ウィンドウの下部にある **[+新規]** をクリックします。
2.	**[実験]** を選択します。
3.	実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** を展開します。
4.	作成したデータセットを見つけ、キャンバスにドラッグします。パレットの上にある **[検索]** ボックスに名前を入力してデータセットを見つけることもできます。  

##データを準備する
データセットの出力ポートを右クリックして **[視覚化]** を選択すると、データの最初の 100 行とデータセット全体の統計情報を表示できます。各列のデータ型は、ML Studio によってこの時点で特定されています。また、データ ファイルには列見出しが含まれていないため、汎用の見出しも追加されています。  

列見出しは必須ではありませんが、モデルのデータを操作する際に便利です。また、最終的にモデルを Web サービスに公開する際に、見出しが設定されていることによってサービスのユーザーが列を見つけやすくなります。  

列見出しを追加するには、**メタデータ エディター** モジュールを使用します。  

1.	モジュール パレットの **[検索]** ボックスに「メタデータ」と入力します。モジュールの一覧に**メタデータ エディター**が表示されます。
2.	**メタデータ エディター** モジュールをクリックしてキャンバスにドラッグし、データセットの下にドロップします。
3.	データセットを**メタデータ エディター**に接続します。データセットの出力ポートをクリックして**メタデータ エディター**の入力ポートにドラッグし、マウス ボタンを離します。これで、マウスをキャンバス上でどこに移動しても、データセットとモジュールが接続されたままになります。
4.	キャンバスの右側にある **[プロパティ]** ウィンドウで **[列セレクターの起動]** をクリックします。
5.	**[が次の値で始まる]** フィールドで [すべての列] が選択されていることを確認し、すべての行を削除してから、**[OK]** をクリックします。これによって**メタデータ エディター**の作業がデータのすべての列に対して実行されるようになります。
6.	**[新しい列名]** パラメーターに、コンマ区切りで列の順番どおりに、データセットの 21 列分の名前を入力します。列名は、UCI Web サイトのデータセットに関するドキュメントから入手できます。または、以下に用意された名前をコピーして貼り付けることができます。  

	Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

[プロパティ] ウィンドウは以下のようになります。

![Properties for Metadata Editor][1] 

>ヒント - 列見出しを検証する場合は、実験を実行 (実験キャンバスの下にある **[実行]** をクリック) し、**メタデータ エディター** モジュールの出力ポートを右クリックして **[視覚化]** を選択します。すべてのモジュールで、この方法によって実験データの進捗状況を確認することができます。

実験は以下のようになっているはずです。  

![Adding Metadata Editor][2]
 
##トレーニング用およびテスト用のデータセットを作成する
実験の次のステップでは、モデルのトレーニングとテストに使用するデータセットをそれぞれ生成します。これには**分割**モジュールを使用します。  

1.	**分割**モジュールを見つけてキャンバスにドラッグし、最後に使用した**メタデータ エディター** モジュールに接続します。
2.	既定では、分割比が 0.5 で、**[ランダム分割]** パラメーターが設定されます。これは、データの半分がランダムに抽出されて**分割**モジュールの 1 つのポートから出力され、残りの半分がもう 1 つのポートから出力されるという意味です。比率は調整できます。また、**[ランダム シード]** パラメーターを使用することによって、トレーニング データとスコア付けデータ間の比率を変更できます。この例では設定はそのままにしておきます。
	>ヒント - 分割比とは、左側にある出力ポートから出力されるデータ量を決定するためのものです。たとえば、比率を 0.7 に設定すると、データの 70% が左側のポートから、30% が右側のポートから出力されます。  
	
これらの**分割**モジュールの出力の用途は自由ですが、ここでは左側の出力をトレーニング データ、右側の出力をスコア付けデータとして使用します。  

UCI での説明のとおり、高い信用リスクを誤って低リスクと分類すると、低い信用リスクを誤って高リスクと分類した場合の 5 倍のコストが発生します。これに対応するため、このコストの特徴を反映した新しいデータセットを生成します。この新しいデータセットでは、高リスクの例を 5 回重複させ、低リスクの例は重複させません。   

この重複は、R コードを使用して以下の手順で実行します。  

1.	**R スクリプトの実行**モジュールを見つけて実験キャンバスにドラッグし、**分割**モジュールの左側の出力ポートに接続します。
2.	**[プロパティ]** ウィンドウの **[分割]** パラメーターの既定の文字列を削除し、以下のスクリプトを入力します。 

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


**分割**モジュールの各出力について同様の重複操作を設定する必要があります。これによって、トレーニング データとスコア付けデータのコスト調整が等しくなります。

1.	**R スクリプトの実行**モジュールを右クリックして **[コピー]** を選択します。
2.	実験キャンバスを右クリックして **[貼り付け]** を選択します。
3.	この **R スクリプトの実行モジュール**を、**分割**モジュールの右側の出力ポートに接続します。  

>ヒント - R スクリプトの実行モジュールのコピーには、元のモジュールと同じスクリプトが格納されています。モジュールをコピーしてキャンバスに貼り付けた場合、モジュールのコピーには元のモジュールのすべてのプロパティが保持されています。  
>
現在、実験は以下のようになっているはずです。
 
![Adding Split module and R scripts][3]

**次の手順: [モデルをトレーニングして評価する][train-models]**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
