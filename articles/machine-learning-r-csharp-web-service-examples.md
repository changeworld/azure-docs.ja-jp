<properties title="Sample Web Services Built with R on Azure ML and Published to Marketplace" pageTitle="Azure ML で R を使用して構築され、Marketplace に発行されたサンプル Web サービス | Azure" description="Azure ML で R を使用して構築され、Marketplace に発行されたサンプル Web サービス" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/13/2014" ms.author="jaymathe" /> 


#Azure ML で R を使用して構築され、Marketplace に発行されたサンプル Web サービス




Azure Machine Learning Studio では、ユーザーが R コードを記述し、ボタンを数回クリックするだけで、そのコードを Web サービスとして発行でき、世界中の他のユーザーがさまざまなデバイスで使用できるようになります。たとえば、統計機能を提供する簡単な計算の生成からカスタム テキスト マイニングのセンチメント分析の予測まで、Azure ML のユーザーが R コードを操作できることは、未経験の R ユーザーと経験のある R ユーザーの両方にとってメリットがあります。これらの Web サービスは、モバイル アプリまたは Web サイトからユーザーが使用できますが、これらの Web サービスには、Azure ML が分析目的でどのように R スクリプトを操作し、R コードでどのように Web サービスを作成するかの例を示すという目的もあります。

このページでは、Azure ML を使用して作成され、Microsoft Azure Marketplace に発行された広範な Web サービスの例を参照できます。各 Web サービスには、サービスをテストするためのサンプル データ セットが添付され、同様のサービスをユーザーが作成するための方法を説明した包括的なドキュメントが用意されています。 

![Workflow][1]

次のシナリオで考えてみましょう。

####シナリオ 1: 汎用モデル 
ユーザーは、時系列データの基本的な予測、高度な分析を行うカスタム ビルドの R メソッドなど、新しいユーザーのデータに適用できる汎用モデルを使用できます。このユーザーは、他のユーザーが自分のデータで使用できるように Web サービスとしてモデルを公開します。

* [バイナリ分類器](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [クラスター モデル](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [多変量線形回帰](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [予測指数平滑法](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [ETS + STL 予測](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [予測 - 自己回帰統合の移動平均 (ARIMA)](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-arima/)
* [生存分析](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####シナリオ 2: トレーニング済みのモデル - 特定のデータ 
ユーザーは、ユーザーの性格のタイプを予測するための k 平均法アルゴリズムからクラスター化した多数の性格アンケートの例、個々の肺がんリスクを予測する健康調査データなど、R コードによる有用な予測を提供するデータを持っています。ユーザーは、新しいユーザーの結果を予測する Web サービスを介してデータを公開します。

####シナリオ 3: トレーニング済みのモデル - 汎用データ 
ユーザーには、さまざまな使用例やシナリオに適用できる Web サービスを構築できる汎用データ (テキスト コーパスなど) があります。

* [辞書ベースのセンチメント分析](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####シナリオ 4: 高度な計算 
ユーザーは、任意のトレーニング済みのモデルやユーザー データへのモデルの適合を必要としない、高度な計算またはシミュレーションを提供します。

* [比率検定での相違](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [正規分布スイート](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [二項分布スイート](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##FAQ
Web サービスの使用、または Marketplace への発行に関するよく寄せられる質問については、[ここ](http://azure.microsoft.com/ja-jp/documentation/articles/machine-learning-marketplace-faq)を参照してください。

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png


