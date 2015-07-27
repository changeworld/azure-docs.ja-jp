<properties 
	pageTitle="What is Azure Machine Learning Studio とは|Microsoft Azure" 
	description="そのまま使うことのできるアルゴリズムやモジュールを含んだライブラリから、ドラッグ アンド ドロップでモデルをすばやく構築できるツール Azure ML Studio の概要です。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="garye"/>

# Azure Machine Learning Studio とは

Microsoft Azure Machine Learning Studio は、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。

Machine Learning Studio (Azure ML Studio と呼ばれることもあります) があれば、最新のデータ サイエンスとクラウド リソースを活用して、独自に所有するデータを使った予測分析を実現することができます。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Machine Learning Studio の対話型ワークスペース

一般的な予測分析モデルの作成では、1 種類以上のソースからデータを入手し、さまざまなデータ操作と統計的特徴を適用してデータを変換することにより、一連の結果を生成します。このようなモデルの作成プロセスは対話型プロセスになります。十分にトレーニングされた有効なモデルが作成されるまで、さまざまな特徴とパラメーターを繰り返し調整します。

**Azure Machine Learning Studio** では、予測分析モデルの作成、テスト、反復作業を支援する、視覚的操作に対応した対話型ワークスペースが提供されます。***データセット***と分析***モジュール***を対話型の***キャンバス***にドラッグ アンド ドロップし、それらを相互に接続して***実験***を完成させ、Machine Learning Studio で***実行***￼できます。モデルのさまざまな設計を試す際は、実験を***編集***して必要に応じて***保存***し、再度実行できます。モデルが完成したら実験を ***Web サービス***として***発行***して、他のユーザーがモデルにアクセスできるようにすることができます。

データセットとモジュールを視覚的に接続すれば予測分析モデルが完成するため、プログラミングは必要ありません。

![Azure ML Studio の図: 実験の作成、各種ソースのデータの読み取り、スコア付けされたデータの書き込み、モデルの書き込み][ml-studio-overview]

## Machine Learning Studio の概要

Machine Learning Studio を初めて起動したときには、以下のタブが左側に表示されます。

- **Studio ホーム** - ドキュメントとその他のリソースへのリンクのセットがまとめられています。
- **実験** - 作成済み、実行済み、ドラフトとして保存済みの実験です。 
- **Web サービス** - 発行済みの実験の一覧です。 
- **設定** - アカウントとリソースを構成するための各種設定がまとめられています。 

>[AZURE.NOTE]実験を作成する際は、利用可能なデータセットとモジュールの作業用一覧がキャンバスの左側に表示されます。モデルの作成には、この一覧に表示されているコンポーネントを使用します。

## 実験の構成要素

実験にはデータセットが含まれます。データセットからデータが分析モジュールに提供され、分析モジュールを接続することで予測分析モデルが完成します。有効な実験に求められる具体的な条件を以下に示します。

- 実験には少なくとも 1 つのデータセットと 1 つのモジュールがある。 
- データセットはモジュールにのみ接続できる。 
- モジュールはデータセットにも別のモジュールにも接続できる。 
- モジュールのすべての入力ポートが、何らかの形でデータ フローに接続されている。 
- モジュールの必須パラメーターがすべて設定されている。 

簡単な実験を作成する例については、「[Azure Machine Learning Studio での簡単な実験の作成](machine-learning-create-experiment.md)」をご覧ください。予測分析ソリューションのより詳しいチュートリアルについては、「[Azure Machine Learning を使用した予測ソリューションの開発](machine-learning-walkthrough-develop-predictive-solution.md)」をご覧ください。

### データセット

データセットを Machine Learning Studio にアップロードすることで、これらのデータセットをモデル作成プロセスで使用できるようになります。Machine Learning Studio には数多くのサンプル データセットが既に含まれているため、これらを実験で試すことができます。また、必要に応じてさらにデータセットをアップロードできます。提供されるデータセットには以下のようなものがあります。

- **さまざまな自動車の燃費データ** - シリンダー数や馬力などによって分類された、自動車の燃費値 (MPG) です。 
- **乳がんデータ** - 乳がんの診断データです。 
- **森林火災データ** - ポルトガル北東地域を対象とする森林火災の規模データです。 

利用可能な作業用データセットの一覧は、実験を作成する際にキャンバスの左側に表示されます。

### モジュール

モジュールとは、データに対して実行できるアルゴリズムのことです。Machine Learning Studio には、データの受信機能や、データのトレーニング、スコア付け、検証などに対応したさまざまなモジュールが用意されています。提供されるモジュールには以下のようなものがあります。

- [ARFF への変換][convert-to-arff] - .NET でシリアル化されたデータセットを属性関係ファイル フォーマット (ARFF) に変換します。 
- [基本統計値][elementary-statistics] - 平均や標準偏差などの基本的な統計値を計算します。 
- [線形回帰][linear-regression] - オンライン傾斜降下に基づく線形回帰モデルを作成します。 
- [モデルのスコア付け][score-model] - トレーニングされた分類か回帰モデルをスコア付けします。 

利用可能な作業用モジュールの一覧は、実験を作成する際にキャンバスの左側に表示されます。

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。モジュールのパラメーターは、キャンバスでモジュールを選択するとキャンバス右側のウィンドウに表示されます。このウィンドウでパラメーターを変更することにより、モデルを微調整できます。


[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
 

<!---HONumber=July15_HO3-->