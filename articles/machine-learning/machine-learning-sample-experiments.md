<properties
    pageTitle="サンプル実験を使用した新しい実験の作成 | Microsoft Azure"
    description="サンプル実験やコミュニティで共有されている実験から、新しい Azure Machine Learning の実験を作成します。"
    services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/28/2016"
    ms.author="chhavib;olgali"/>

# サンプル実験を使用した新しい実験の作成
実験を作成する場合は、[Cortana Intelligence ギャラリー](http://gallery.cortanaintelligence.com/)の例を参照してください。Machine Learning チームによって投稿されたサンプル実験だけでなく、より広範な Machine Learning コミュニティによって共有されているサンプル実験も見つけられます。また、実験について質問したり、コメントを投稿したりすることができます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Cortana Intelligence ギャラリーにアクセスする
ギャラリーでどのような実験を利用できるかを確認するには、[ギャラリー](http://gallery.cortanaintelligence.com/)に移動して、ページの上部の **[Experiments]** (実験) をクリックします。このページでは、**最近追加された**実験、**人気のある実験**、**人気のある Microsoft の実験**の最新バージョンを表示することができます。

すべての実験を参照するには、次の手順を実行します。

1. ページの上部にある **[Browse all]** (すべて参照) をクリックします。
2. **[Refine by]** (絞り込み条件) で **[Experiment]** (実験) を選択すると、ギャラリー内のすべての実験が表示されます。
3. 一覧を絞り込むには、左側のフィルターを選択します。たとえば、PCA ベースの異常検出アルゴリズムを使用する実験を参照するには、**[Categories]** (カテゴリ) で **[Experiment]** (実験) を選択し、**[Algorithms Used]** (使用されたアルゴリズム) で **[PCA-Based Anomaly Detection]** (PCA ベースの異常検出) を選択します (このアルゴリズムが表示されない場合は、一覧の末尾にある **[Show all]** (すべて表示) をクリックしてください)。<br></br> ![](./media/machine-learning-sample-experiments/refine-the-view.png) 
4. 検索ボックスを使用して、さらに選択肢を絞ることができます。たとえば、Microsoft によって投稿された、2 クラス サポート ベクター マシン アルゴリズムを使用する数字認識に関連する実験を検索するには、検索ボックスで「digit recognition」と入力し、**[Experiment]** (実験)、**[Microsoft content only]** (Microsoft のコンテンツのみ)、および **[Two-Class Support Vector Machine]** (2 クラス サポート ベクター マシン) を選択します。![](./media/machine-learning-sample-experiments/search-for-experiments.png) 
5. 実験をクリックして詳細を表示します。実験を実行したり変更したりするには、実験のページで **[Open in Studio]** (Studio で開く) をクリックします。

> [AZURE.NOTE] Machine Learning Studio で実験を開くには、Microsoft アカウントの資格情報でサインインする必要があります。Machine Learning ワークスペースをまだ持っていない場合は、無料評価版のワークスペースが作成されます。[Machine Learning 無料試用版に含まれる内容を確認してください](https://azure.microsoft.com/pricing/details/machine-learning/)。

![](./media/machine-learning-sample-experiments/example-experiment.png)


## Machine Learning Studio でのテンプレートの使用

ギャラリーのサンプルをテンプレートとして、Machine Learning Studio で新しい実験を作成することもできます。

1. Microsoft アカウントの資格情報で [Studio](https://studio.azureml.net) にサインインし、**[新規]*** をクリックして新しい実験を作成します。
2. サンプルのコンテンツを参照し、いずれかをクリックします。

サンプル実験をテンプレートとして、ワークスペースに新しい実験が作成されます。

## 次のステップ
- [データを準備する](machine-learning-data-science-import-data.md)
- [実験で R の使用を試す](machine-learning-r-quickstart.md)
- [サンプルの R 実験を確認する](machine-learning-r-csharp-web-service-examples.md)
- [Web サービス API を作成する](machine-learning-publish-a-machine-learning-web-service.md)
- [すぐに使用できるアプリケーションを参照する](https://datamarket.azure.com/browse?query=machine+learning)

<!---HONumber=AcomDC_0504_2016-->