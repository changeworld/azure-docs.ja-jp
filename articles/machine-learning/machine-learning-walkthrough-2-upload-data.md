<properties
	pageTitle="手順 2: Machine Learning の実験にデータをアップロードする | Microsoft Azure"
	description="予測ソリューションの開発に関するチュートリアルの中の、保存されているパブリック データを Azure Machine Learning Studio にアップロードするステップの 2 番目の手順です。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016" 
	ms.author="garye"/>


# チュートリアル手順 2: Azure Machine Learning の実験に既存のデータをアップロードする

これは、「[チュートリアル: 信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](machine-learning-walkthrough-develop-predictive-solution.md)」の 2 番目の手順です。


1.	[Machine Learning ワークスペースの作成](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	**既存のデータをアップロードする**
3.	[新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[モデルをトレーニングして評価する](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Web サービスをデプロイする](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Web サービスにアクセスする](machine-learning-walkthrough-6-access-web-service.md)

----------

信用リスクの予測モデルを作成するには、トレーニングとその後のモデルのテストに使用できるデータが必要です。このチュートリアルでは、UCI Machine Learning Repository の "UCI Statlog (German Credit Data) Data Set" (UCI Statlog (ドイツの信用貸付データ) データ セット) を使用します。詳細についてはこちらをご覧ください。<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

**german.data** という名前のファイルを使用します。このファイルをローカル ハード ドライブにダウンロードします。

このデータセットの行には、信用貸付のための 1,000 人の過去の申請者に関する 20 の変数が含まれています。これらの 20 の変数は、データセットの特徴ベクトルを表し、各クレジット貸付申請者を識別する特徴を提供します。各行の追加の列は、申請者について計算された信用リスクを表し、700 人の申請者が低リスクとして、300 人が高リスクとして識別されています。

UCI の Web サイトでは、財務情報、信用貸付の履歴、雇用状況、および個人情報を含む特徴ベクトルの属性について説明されています。各申請者について、申請者の信用リスクの高低を示す二項評価が与えられています。

予測分析モデルをトレーニングするために、このデータを使用します。トレーニングが終了すると、このモデルで新しい個人情報を受け入れて信用リスクの高低を予測できるようになります。

興味深い展開を次に示します。データセットの説明から、実際は信用リスクの高い個人が誤って低く分類された場合、低い信用リスクを誤って高く分類したときと比較して、金融機関は 5 倍のコストを強いられることがわかります。実験でこれを考慮するための 1 つの簡単な方法に、高い信用リスクを示す個人のエントリを重複 (5 回) させる方法があります。このとき、このモデルが、高い信用リスクを誤って低く分類すると、重複エントリごとに 1 回、合計 5 回分類を誤ることになります。これにより、トレーニング結果でこのエラーのコストが増加します。

##データセットの形式の変換
元のデータセットは、空白で区切られた形式を使用しています。Machine Learning Studio で使用するにはコンマ区切り値 (CSV) ファイルの方が適しているため、空白をコンマに置き換えてデータセットを変換します。

このデータを変換する方法は多数存在します。1 つは、次の Windows PowerShell コマンドを使用する方法です。

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

他に、次の Unix の sed コマンドを使用する方法もあります。

	sed 's/ /,/g' german.data > german.csv  

どちらを実行した場合も、コンマ区切りに変換されたデータが、**german.csv** という名前のファイルに作成されます。このファイルは、以降の実験で使用します。

##Machine Learning Studio にデータセットをアップロードする

データを CSV 形式に変換したら、それを Machine Learning Studio にアップロードする必要があります。Machine Learning Studio の概要の詳細については、「[Microsoft Azure Machine Learning Studio ホーム](https://studio.azureml.net/)」をご覧ください。

1.	Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)) を開きます。サインインを求められたら、ワークスペースの所有者として指定したアカウントを使用します。
1.  ページの上部にある **[Studio]** タブをクリックします。
1.	ウィンドウの下部にある **[+新規]** をクリックします。
1.	**[データセット]** を選択します。
1.	**[ローカル ファイルから]** を選択します。
1.	**[新しいデータセットをアップロードする]** ダイアログで、**[参照]** をクリックし、作成した **german.csv** ファイルを検索します。
1.	データセットの名前を入力します。このチュートリアルでは、「UCI German Credit Card Data」 としています。
1.	データ型として、**[ヘッダーなしの汎用 CSV ファイル (.nh.csv)]** を選択します。
1.	必要に応じて説明を追加します。
1.	**[OK]** をクリックします。

![データセットのアップロード][1]


これにより、データが、実験で使用できるデータセット モジュールにアップロードされます。

> [AZURE.TIP] Studio にアップロードしたデータセットを管理するには、Studio ウィンドウの左側にある **[データセット]** タブをクリックします。

さまざまな種類のデータを実験にインポートする方法の詳細については、「[Azure Machine Learning Studio への学習データのインポート](machine-learning-data-science-import-data.md)」をご覧ください。

**次: [新しい実験を作成する](machine-learning-walkthrough-3-create-new-experiment.md)**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!---HONumber=AcomDC_0921_2016-->