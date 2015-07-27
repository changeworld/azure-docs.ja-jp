<properties
    pageTitle="Microsoft Azure Machine Learning とは | Microsoft Azure"
    description="ソリューションを作成、操作、収益化する際に使用するクラウド テクノロジーである完全に管理された Machine Learning のサービスの基本的な概念について説明します。"
	services="machine-learning"
    documentationCenter=""
    authors="cjgronlund"
    manager="neerajkh"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/23/2015"
    ms.author="cgronlun;tedway;olgali"/>


# Microsoft Azure での機械学習の概要

## 機械学習とは

機械学習では、コンピューターを使用して将来の動作、結果、傾向を予測するために既存のデータから学習する予測モデルを実行します。

機械学習からのこうした予想や予測によってアプリやデバイスの機能性を高めることができます。オンライン ショッピングでは、ユーザーが今までに購入した製品に基づいて他の商品をお勧めするのに機械学習が役立っています。クレジット カードが読み取られると、機械学習では、トランザクションをトランザクションのデータベースと比較し、銀行で不正が検出できるように支援します。

## Microsoft Azure Machine Learning とは

Azure Machine Learning は、クラウド ベースの強力な予測分析サービスであり、分析ソリューションとして予測モデルを迅速に作成し、デプロイします。

Azure Machine Learning ではをモデルの予測分析ツールを提供するだけでなく、予測モデルをすぐに使用できる Web サービスとして発行するために使用できる完全に管理されたサービスを提供します。また、クラウドで予測分析の完全なソリューションを作成するためのツールを提供します。予測モデルを迅速に作成、テストし、運用を実現して管理します。ハードウェアを購入したり、仮想マシンを手動で管理したりする必要はありません。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## 予測分析とは

予測分析ではさまざまな統計手法 (この場合は、機械学習) を使用して、収集したデータや最新データのパターンや傾向を分析し、将来のイベントを予測します。

Azure Machine Learning は非常に強力な方法で予測分析を行います。すぐに使用できるアルゴリズムのライブラリから作業し、設備やインフラストラクチャを別に購入せずにインターネットに接続された PC でモデルを作成して、予測ソリューションを迅速にデプロイできます。また、[Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) や [Machine Learning ギャラリー](http://gallery.azureml.net/)ではすぐに使用できる例やソリューションを見つけることができます。

## クラウドで完全な機械学習ソリューションを構築する

Azure Machine Learning には、大規模のアルゴリズム ライブラリから、モデル構築のスタジオや、モデルを Web サービスとしてデプロイする簡単な方法まで、クラウドで予測分析ソリューションを作成するために必要なすべてが揃っています。

### Machine Learning Studio: 予測モデルの作成

ブラウザー ベースのツール [Machine Learning Studio](machine-learning-what-is-ml-studio.md) で、モジュールをドラッグ アンド ドロップしたり、接続したりして予測モデルを作成します。

![予測分析とは: Azure Machine Learning Studio の予測分析実験の例](./media/machine-learning-what-is-machine-learning/AzureMLStudio.png)

* Machine Learning Studio にある [Machine Learning アルゴリズムとモジュール](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7)の大規模なライブラリを使用して、予測モデルをすぐに開始します。サンプル実験、R と Python のパッケージ、Xbox や Bing など Microsoft のビジネスで使われている最高クラスのアルゴリズムのライブラリから選択します。[R](machine-learning-r-quickstart.md) と [Python](machine-learning-execute-python-scripts.md) の独自のカスタム スクリプトで Studio モジュールを拡張できます。
* [Machine Learning Community Gallery](machine-learning-gallery-how-to-use-contribute-publish.md) は、ユーザーが Azure Machine Learning を始めるための効果的な方法であり、またコミュニティの他のユーザーから学ぶことができる場所でもあります。他のユーザーが作成した実験を実行したり、実験に関する質問やコメント投稿したり、独自の実験を発行できます。LinkedIn や Twitter などのソーシャル ネットワーク経由で実験へのリンクを共有することもできます。  

	![サンプルの予測実験を試すか、ご自身の実験を Azure Machine Learning ギャラリーに投稿します。](./media/machine-learning-what-is-machine-learning/AzureMLGallery.png)

### 予測分析ソリューションの運用を実現: Web サービスを購入するか、独自に発行

* 推奨事項、テキスト分析、異常検出などすぐに使用できる Web サービスを [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) から購入します。

* 予測分析モデルの運用を実現します。
    * [Web サービスを発行する](machine-learning-publish-a-machine-learning-web-service.md)
    * [API を通じてモデルをトレーニング/再トレーニングする](machine-learning-retrain-models-programmatically.md)
    * [Web サービスのエンドポイントを管理する](machine-learning-create-endpoint.md)
    * [Web サービスを拡張する](machine-learning-scaling-endpoints.md)
    * [Web サービスを使用する](machine-learning-consume-web-services.md)

## 機械学習における主な用語集と概念
### データの探索、説明的な分析、予測分析

**データの探索**は、大規模で多くの場合非構造化データ セットに関する情報を収集するプロセスで、焦点を絞った分析の特性を検索します。**データ マイニング**は自動化されたデータの探索を参照します。

**説明的な分析**は、変更内容を要約するためにデータ セットを分析するプロセスです。売り上げレポート、Web のメトリック、ソーシャル ネットワークの分析などほとんどのビジネス分析は、説明的な分析になります。

**予測分析**は、将来の結果を予測するために履歴データや最新データからモデルを構築するプロセスです。


### 教師あり学習と教師なし学習
 **教師あり学習**アルゴリズムはラベルが付けられたデータでのトレーニングです。つまりデータは必要な回答の例で構成されています。たとえば、クレジットカードの不正使用を識別するモデルは、既知の不正な料金や有効な料金がラベル付けされていることを示すデータ ポイントのデータ セットからのトレーニングを受けます。ほとんどの機械学習は教師ありになります。

 **教師なし学習**は、ラベルのないデータで使用されます。その目的はデータにおけるリレーションシップを検出することです。たとえば、似たような購入行動を持つ顧客の人口統計のグループを検索できます。

### モデルのトレーニングと評価
機械学習モデルとは、回答しようとしている質問の抽象化や、予測する結果になります。モデルは既存のデータからトレーニングされ、評価されます。

#### データからのトレーニング
Azure Machine Learning では、モデルはトレーニング データを処理するアルゴリズム モジュールとスコア付けモジュールなどの機能モジュールで構成されます。

教師あり学習では、不正検出モデルをトレーニングしている場合、不正か有効のいずれかでラベル付けされた一連のトランザクションを使用します。データ セットをランダムに分割し、その一部使用してモデルをトレーニングし、また別の一部を使用してモデルをテストしたり、評価したりします。

#### 評価データ
モデルをトレーニングしたら、残りのテスト データを使用してそのモデルを評価します。モデルが正確に予測できるかどうか確認できるように、結果がすでに分かっているデータを使用します。

### その他の一般的な機械学習用語

* **アルゴリズム**: 自己完結型の一連のルールで、データ処理、計算、自動推理を通じて問題を解決します。
* **カテゴリ データ**: カテゴリに分類して、グループに分けることができるデータです。たとえば自動車のカテゴリ データ セットでは、年式、製造元、モデル、価格が特定できます。
* **分類**: カテゴリのグループ化が既に分かっているデータ セットに基づいて、データ ポイントをカテゴリに整理するためのモデルです。
* **特徴エンジニアリング**: データ セットに関連する特徴を抽出、選択するプロセスで、データ セットを強化し、結果を向上させます。たとえば、航空運賃のデータは曜日や休日で強化できます。「[Azure Machine Learning での特徴エンジニアリングと特徴選択](machine-learning-feature-selection-and-engineering.md)」をご覧ください。
* **モジュール**: 小さなデータ セットを入力、編集できるデータ入力モジュールなど、Machine Learning Studio モデルの機能要素です。Machine Learning Studio ではアルゴリズムもモジュールの種類になります。
* **モデル**: 教師あり学習では、モデルは機械学習実験の産物で、トレーニング データ セット、アルゴリズム モジュール、スコア モデル モジュールなどの機能モジュールで構成されます。
* **数値データ**: 測定値 (継続的なデータ) や数値 (不連続データ) としての意味があるデータです。*定量的データ*とも呼ばれます。
* **パーティション**: データをサンプルに分割するメソッドです。詳細情報は「[パーティションとサンプル](https://msdn.microsoft.com/library/azure/dn905960.aspx)」をご覧ください。
* **予測**: 予測とは、機械学習モデルからの値の予測になります。用語「予測スコア」を目にすることもあると思いますが、予測スコアはモデルの最終出力ではありません。モデルの評価の後にスコアが続きます。
* **回帰**: 年や製造元に基づいて自動車の価格を予測するなど、独立変数に基づいて連続値を予測するモデルです。
* **スコア**: Machine Learning Studio の[スコア モデル モジュール](https://msdn.microsoft.com/library/azure/dn905995.aspx)を使用してトレーニング済みの分類や回帰モデルから生成された予測値です。分類モデルも、予測された値の確率のスコアを返します。モデルからスコアを生成したら、[モデルの評価モジュール](https://msdn.microsoft.com/library/azure/dn905915.aspx)を使用してモデルの精度を評価できます。
* **サンプル**: 全体を代表するために使用するデータ セットの一部です。サンプルはランダムに選択したり、データ セットの特定の機能に基づいて選択したりできます。



## 次のステップ
[ステップ バイ ステップ チュートリアル](machine-learning-create-experiment.md)を使用し、また[サンプル上に構築](machine-learning-sample-experiments.md)することによって予測分析と機械学習の基礎について学習できます。


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!---HONumber=July15_HO2-->