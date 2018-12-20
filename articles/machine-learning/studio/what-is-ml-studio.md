---
title: 概要 - Azure Machine Learning Studio | Microsoft Docs
description: そのまま使うことのできるアルゴリズムやモジュールを含んだライブラリから、ドラッグ アンド ドロップでモデルをすばやく構築できるツール Azure ML Studio の概要です。
keywords: azure machine learning、azure ml、ml studio
services: machine-learning
documentationcenter: ''
author: garyericson
ms.custom: seodec18
ms.author: garye
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/28/2018
ms.openlocfilehash: b1c0d3da6dba4bf56baaf583d89c58fdc13ef83f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250520"
---
# <a name="what-is-azure-machine-learning-studio"></a>Azure Machine Learning Studio とは
Microsoft Azure Machine Learning Studio は、データを活用した予測分析ソリューションの構築、テスト、デプロイをドラッグ アンド ドロップで行うことができる、コラボレーションに対応したツールです。 Machine Learning Studio でモデルを Web サービスとして公開すれば、カスタム アプリや BI ツール (Excel など) からそのモデルを簡単に利用することができます。

Machine Learning Studio があれば、最新のデータ サイエンスとクラウド リソースを活用して、独自に所有するデータを使った予測分析を実現することができます。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-machine-learning-studio-interactive-workspace"></a>Machine Learning Studio の対話型ワークスペース
一般的な予測分析モデルの作成では、1 種類以上のソースからデータを入手し、さまざまなデータ操作と統計的特徴を適用してデータを変換することにより、一連の結果を生成します。 このようなモデルの作成プロセスは対話型プロセスになります。 十分にトレーニングされた有効なモデルが作成されるまで、さまざまな特徴とパラメーターを繰り返し調整します。

**Azure Machine Learning Studio** では、予測分析モデルの作成、テスト、反復作業を支援する、視覚的操作に対応した対話型ワークスペースが提供されます。 "***データセット***" と分析 "***モジュール***" を対話型のキャンバスにドラッグ アンド ドロップし、それらを相互に接続して "***実験***" を完成させ、Machine Learning Studio で実行できます。 モデルのさまざまな設計を試す際は、実験を編集して必要に応じて保存し、再度実行できます。 準備が整ったら、"***トレーニング実験***" を "***予測実験***" に変換します。さらに、"***Web サービス***" として発行し、第三者が利用できるようにそのモデルを公開します。

データセットとモジュールを視覚的に接続すれば予測分析モデルが完成するため、プログラミングは必要ありません。

> [!TIP]
> Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](studio-overview-diagram.md)」ご覧ください。
> 
> 

![Azure ML Studio の図:実験の作成、各種ソースのデータの読み取り、スコア付けされたデータの書き込み、モデルの書き込み][ml-studio-overview]

## <a name="get-started-with-machine-learning-studio"></a>Machine Learning Studio の概要
[Machine Learning Studio](https://studio.azureml.net) を初めて起動したときには、 **ホーム** ページが表示されます。 そこからドキュメントやビデオ、ウェビナーなど、有益なリソースにアクセスすることができます。

左上のメニュー  ![メニュー](./media/what-is-ml-studio/menu.png)  をクリックすると、いくつかのオプションが表示されます。

### <a name="cortana-intelligence"></a>Cortana Intelligence
**[Cortana Intelligence]** をクリックすると、[Cortana Intelligence Suite](https://www.microsoft.com/cloud-platform/cortana-intelligence-suite) のホーム ページが表示されます。 Cortana Intelligence Suite は、データをインテリジェントなアクションに変換するための、フル マネージドのビッグ データおよび高度な分析スイートです。 顧客事例を含む完全なドキュメントについては、Cortana Intelligence Suite のホーム ページを参照してください。

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio
ここには、**[ホーム]** (最初のページ) と **[Studio]** の 2 つのオプションがあります。

**[Studio]** をクリックすると、**Azure Machine Learning Studio** に移動します。 最初に、Microsoft アカウントを使用するか職場または学校アカウントを使用してサインインするように求められます。 サインインすると、次のタブが左側に表示されます。

* **[PROJECTS (プロジェクト)]** - 単一のプロジェクトを表す実験、データセット、ノートブック、およびその他のリソースがまとめられています。
* **[EXPERIMENTS (実験)]** - 作成済み、実行済み、ドラフトとして保存済みの実験です。
* **[Web サービス]** - 実験からデプロイした Web サービスです。
* **[ノートブック]** - 作成済みの Jupyter ノートブックです。
* **[データセット]** - Studio にアップロード済みのデータセット。
* **[トレーニング済みのモデル]** - 実験でトレーニングし、Studio で保存したモデルです。
* **設定** - アカウントとリソースを構成するための各種設定がまとめられています。

### <a name="gallery"></a>[ギャラリー]
**[ギャラリー]** をクリックすると、**[Azure AI ギャラリー](http://gallery.cortanaintelligence.com/)** に移動します。 ギャラリーは、データ サイエンティストや開発者のコミュニティが、Cortana Intelligence Suite のコンポーネントを使用して作成したソリューションを共有する場です。

ギャラリーの詳細については、[Azure AI ギャラリーでのソリューションの共有と発見](gallery-how-to-use-contribute-publish.md)に関するページを参照してください。

## <a name="components-of-an-experiment"></a>実験の構成要素
実験にはデータセットが含まれます。データセットからデータが分析モジュールに提供され、分析モジュールを接続することで予測分析モデルが完成します。 有効な実験に求められる具体的な条件を以下に示します。

* 実験には少なくとも 1 つのデータセットと 1 つのモジュールがある。
* データセットはモジュールにのみ接続できる。
* モジュールはデータセットにも別のモジュールにも接続できる。
* モジュールのすべての入力ポートが、何らかの形でデータ フローに接続されている。
* モジュールの必須パラメーターがすべて設定されている。

実験を最初から作成するか、既にあるサンプル実験をテンプレートとして使用してください。 詳細については、「[サンプル実験をコピーして新しい機械学習実験を作成する](sample-experiments.md)」を参照してください。

簡単な実験を作成する例については、「 [Azure Machine Learning Studio での簡単な実験の作成](create-experiment.md)」をご覧ください。

予測分析ソリューションのより詳しいチュートリアルについては、「 [Azure Machine Learning を使用した予測ソリューションの開発](walkthrough-develop-predictive-solution.md)」をご覧ください。

### <a name="datasets"></a>データセット
データセットを Machine Learning Studio にアップロードすることで、これらのデータセットをモデル作成プロセスで使用できるようになります。 Machine Learning Studio には数多くのサンプル データセットが既に含まれているため、これらを実験で試すことができます。また、必要に応じてさらにデータセットをアップロードできます。 提供されるデータセットには以下のようなものがあります。

* **さまざまな自動車の燃費データ** - シリンダー数や馬力などによって分類された、自動車の燃費値 (MPG) です。
* **乳がんデータ** - 乳がんの診断データです。
* **森林火災データ** - ポルトガル北東地域を対象とする森林火災の規模データです。

利用可能なデータセットは、実験を作成するときにキャンバスの左側の一覧から選択できます。

Machine Learning Studio に含まれている一連のサンプル データセットについては、 [Azure Machine Learning Studio でのサンプル データセットの使用](use-sample-datasets.md)に関するページを参照してください。

### <a name="modules"></a>モジュール
モジュールとは、データに対して実行できるアルゴリズムのことです。 Machine Learning Studio には、データの受信機能や、データのトレーニング、スコア付け、検証などに対応したさまざまなモジュールが用意されています。 提供されるモジュールには以下のようなものがあります。

* [ARFF への変換][convert-to-arff] - .NET でシリアル化されたデータセットを属性関係ファイル フォーマット (ARFF) に変換します。
* [基本統計値の計算][elementary-statistics] - 平均や標準偏差などの基本的な統計値を計算します。
* [線形回帰][linear-regression] - オンライン傾斜降下に基づく線形回帰モデルを作成します。
* [モデルのスコア付け][score-model] - トレーニングされた分類か回帰モデルをスコア付けします。

利用可能なモジュールは、実験を作成するときにキャンバスの左側の一覧から選択できます。  

モジュールに一連のパラメーターが含まれている場合、これらを使用してモジュールの内部アルゴリズムを構成することができます。 モジュールのパラメーターは、キャンバスでモジュールを選択するとキャンバス右側の **[プロパティ]** ウィンドウに表示されます。 このウィンドウでパラメーターを変更することにより、モデルを微調整できます。

大規模な機械学習アルゴリズムのライブラリを利用する際のヒントについては、 [Microsoft Azure Machine Learning のアルゴリズムの選択方法](algorithm-choice.md)に関するページを参照してください。

## <a name="deploying-a-predictive-analytics-web-service"></a>予測分析 Web サービスのデプロイ
予測分析モデルの準備が整ったら、それを Machine Learning Studio から Web サービスとしてデプロイすることができます。 このプロセスの詳細については、「 [Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」を参照してください。

[ml-studio-overview]:./media/what-is-ml-studio/azure-ml-studio-diagram.jpg



## <a name="key-machine-learning-terms-and-concepts"></a>機械学習における主な用語と概念
機械学習の用語はわかりにくい場合があります。 ここでは、役に立つ主な用語の定義を紹介します。 これ以外にも定義をご希望の用語があれば、後のコメントを利用してお知らせください。

### <a name="data-exploration-descriptive-analytics-and-predictive-analytics"></a>データの探索、説明的な分析、予測分析

**データの探索** は、大規模で、多くの場合構造化されていないデータ セットに関する情報を収集するプロセスであり、焦点を絞った分析のための特性を見つけ出すために行われます。

**データ マイニング** は自動化されたデータの探索を参照します。

**説明的な分析** は、変更内容を要約するためにデータ セットを分析するプロセスです。 売り上げレポート、Web のメトリック、ソーシャル ネットワークの分析などほとんどのビジネス分析は、説明的な分析になります。

**予測分析** は、将来の結果を予測するために履歴データや最新データからモデルを構築するプロセスです。

### <a name="supervised-and-unsupervised-learning"></a>教師あり学習と教師なし学習
 **教師あり学習**アルゴリズムはラベルが付けられたデータでのトレーニングです。つまりデータは必要な回答の例で構成されています。 たとえば、クレジットカードの不正使用を識別するモデルのトレーニングには、既知の不正な課金と問題のない課金がラベル付けされたデータ ポイントを含むデータ セットが使用されます。 ほとんどの機械学習は教師ありになります。

 **教師なし学習**は、ラベルのないデータで使用されます。その目的はデータにおけるリレーションシップを検出することです。 たとえば、似たような購入行動を持つ顧客の人口統計のグループを検索できます。

### <a name="model-training-and-evaluation"></a>モデルのトレーニングと評価
機械学習モデルとは、回答しようとしている質問の抽象化や、予測する結果になります。 モデルは既存のデータからトレーニングされ、評価されます。

#### <a name="training-data"></a>トレーニング データ
データからモデルをトレーニングするときは、既知のデータ セットを使用し、データの特性に基づいてモデルの調整を行って、最も正確な答えを得られるようにします。 Azure Machine Learning では、モデルはトレーニング データを処理するアルゴリズム モジュールとスコア付けモジュールなどの機能モジュールで構成されます。

教師あり学習で不正検出モデルをトレーニングしている場合、不正と有効のどちらかでラベル付けされた一連のトランザクションを使用します。 データ セットをランダムに分割し、その一部を使用してモデルをトレーニングし、別の一部を使用してモデルをテストしたり、評価したりします。

#### <a name="evaluation-data"></a>評価データ
モデルをトレーニングしたら、残りのテスト データを使用してそのモデルを評価します。 モデルが正確に予測できるかどうか確認できるように、結果がすでに分かっているデータを使用します。

## <a name="other-common-machine-learning-terms"></a>その他の一般的な機械学習用語
* **アルゴリズム**:自己完結型の一連のルールで、データ処理、演算、自動推理を通じて問題を解決するために使用されます。
* **異常検出**:異常なイベントや値にフラグを設定し、問題の検出を支援するモデルです。 たとえばクレジットカードの不正使用の検出では、異常な購入を検出します。
* **カテゴリ データ**:カテゴリに分類して、グループに分けることができるデータです。 たとえば自動車のカテゴリ データ セットでは、年式、製造元、モデル、価格が特定できます。
* **分類**:カテゴリのグループ化が既に分かっているデータ セットに基づいて、データ ポイントをカテゴリに整理するためのモデルです。
* **特徴エンジニアリング**:データ セットに関連する特徴を抽出、選択するプロセスで、データ セットを強化し、結果を向上させます。 たとえば、航空運賃のデータは曜日や休日で強化できます。 「 [Azure Machine Learning での特徴エンジニアリングと特徴選択](../team-data-science-process/create-features.md)」をご覧ください。
* **モジュール**:小規模なデータ セットを入力、編集できるデータ入力モジュールなど、Machine Learning Studio モデルの機能部分です。 Machine Learning Studio ではアルゴリズムもモジュールの種類になります。
* **モデル**:教師あり学習モデルは、機械学習実験の産物で、トレーニング データ セット、アルゴリズム モジュール、機能モジュール (スコア モデル モジュールなど) で構成されます。
* **数値データ**:測定値 (継続的なデータ) や数値 (不連続データ) としての意味があるデータです。 *定量的データ*とも呼ばれます。
* **パーティション**:データをサンプルに分割するメソッドです。 詳細情報は「 [パーティションとサンプル](https://msdn.microsoft.com/library/azure/dn905960.aspx) 」をご覧ください。
* **予測**:予測とは、機械学習モデルからの値の予測になります。 "予測スコア" という用語を目にすることもあると思いますが、 予測スコアはモデルの最終出力ではありません。 モデルの評価の後にスコアが続きます。
* **回帰**:年や製造元に基づいて自動車の価格を予測するなど、独立変数に基づいて値を予測するためのモデルです。
* **スコア**:Machine Learning Studio の[スコア モデル モジュール](https://msdn.microsoft.com/library/azure/dn905995.aspx)を使用してトレーニング済みの分類や回帰モデルから生成された予測値です。 分類モデルも、予測された値の確率のスコアを返します。 モデルからスコアを生成したら、 [モデルの評価モジュール](https://msdn.microsoft.com/library/azure/dn905915.aspx)を使用してモデルの精度を評価できます。
* **サンプル**:全体を代表するために使用するデータ セットの一部です。 サンプルはランダムに選択したり、データ セットの特定の機能に基づいて選択したりできます。

## <a name="next-steps"></a>次の手順
[ステップ バイ ステップ チュートリアル](create-experiment.md)を使用し、また[サンプル上に構築](sample-experiments.md)することによって予測分析と機械学習の基礎について学習できます。  


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
