<properties 
	pageTitle="Azure Machine Learning でモデル結果を解釈する方法 | Azure" 
	description="モデルのスコア付け出力を使用して視覚化し、アルゴリズムに最適なパラメーター セットを選択する方法。" 
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
	ms.date="04/21/2015" 
	ms.author="bradsev" />


# Azure Machine Learning でモデル結果を解釈する方法 
 
**「モデルのスコア付け」出力の理解と視覚化** このトピックでは、Azure Machine Learning Studio で予測結果を視覚化し、解釈する方法について説明します。モデルのトレーニングと予測が完了したら (「モデルのスコア付け」)、得られた予測結果を理解し、解釈する必要があります。

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 

Azure Machine Learning には、主に次の 4 種類の機械学習モデルがあります。

* 分類
* クラスタリング
* 回帰
* レコメンダー システム

これらのモジュールに加えて、予測に使用される次のモジュールは「スコア付け」をし、テスト データが与えられます。

* [モデルのスコア付け][score-model]モジュールは分類と回帰に使用します。 
* [クラスターへの割り当て][assign-to-clusters]モジュールはクラスタ リングに使用します。 
* [スコア マッチ ボックス レコメンダー][score-matchbox-recommender]は推奨システムに使用します。 
 
このドキュメントでは、これらのモジュールの各予測結果を解釈する方法について説明します。これらのモデルの概要については、「[Azure Machine Learning でアルゴリズムを最適化するためにパラメーターを選択する方法](machine-learning-algorithm-parameters-optimize.md)」をご覧ください。

このトピックでは、モデルの評価ではなく、予測の解釈について説明します。モデルを評価する方法の詳細については、「[Azure Machine Learning でモデルのパフォーマンスを評価する方法](machine-learning-evaluate-model-performance.md)」をご覧ください。

Azure Machine Learning を初めて使用する場合は、単純な実験を作成して開始する方法について、Azure Machine Learning Studio で「[Azure Machine Learning Studio での簡単な実験の作成](machine-learning-create-experiment.md)」をご覧ください。

##分類
分類問題には 2 つのサブ カテゴリがあります。

* 2 つのクラス (2 クラスまたは二項分類) のみに関する問題 
* 3 つ以上のクラス (多クラス分類) に関する問題 

Azure Machine Learning には、これらの種類の分類をそれぞれ処理する各種モジュールがあります。予測結果を解釈する方法はどれもよく似ています。最初に 2 クラス分類問題について説明してから、多クラス分類問題について説明します。

###2 クラス分類
**実験例**

2 クラス分類問題の例では、あやめの分類を使用します。ここでのタスクは、特徴に基づいてあやめを分類することです。Azure Machine Learning に用意されているあやめのデータセットは、最もよく利用されている「[あやめデータセット](http://en.wikipedia.org/wiki/Iris_flower_data_set)」です。これに含まれているインスタンスは、2 種類の花の品種 (0 ～ 1 クラス) のみです。それぞれの花には 4 種類の特徴 (がくの長さ、がくの幅、花弁の長さ、花弁の幅) があります。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1.png)

図 1: あやめの 2 クラス分類問題の実験

この問題を解決するために、図 1 に示すとおり実験が実行されました。2 クラスのブースト デシジョン ツリー モデルがトレーニングされ、スコア付けされました。これで、[モデルのスコア付け][score-model]モジュールの出力ポートをクリックし、表示されたメニューで **[視覚化]** をクリックすると、[モデルのスコア付け][score-model]モジュールから予測結果を視覚化できるようになります。図 2 に示すように、スコア付けの結果が表示されます。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/2.png)

図 2: 2 クラス分類でモデルのスコア付けの結果を視覚化する

**結果の解釈**

結果テーブルには、6 つの列があります。左の 4 つの列は、4 種類の特徴です。右側の 2 つの列、「Scored Labels (スコア付けラベル)」と「Scored Probabilities (スコア付け確率)」は予測結果です。「Scored Probabilities」列は、花が正のクラス (クラス 1) に属する確率を示しています。たとえば、列の最初の数字 0.028571 は、最初の花がクラス 1 に属する確率が 0.028571 であることを意味します。「Scored Labels」列は、各花の予測クラスを示しています。これは、「Scored Probabilities」列に基づいています。花のスコア付け確率が 0.5 よりも高い場合は、クラス 1 として予測され、それ以外の場合はクラス 0 として予測されます。

**Web サービスの発行**

予測結果を理解し、結果の妥当性が確認できたら、実験を Web サービスとして発行できます。発行すると、さまざまなアプリケーションで展開して呼び出し、新しいあやめにおけるクラスの予測を取得できます。トレーニング実験をスコア付け実験に変換し、Web サービスとして発行する方法の手順については、「[Azure Machine Learning Web サービスを発行する](machine-learning-walkthrough-5-publish-web-service.md)」をご覧ください。この手順に従うと、図 3 で示すスコア付けの実験を実行できます。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/3.png)

図 3: あやめの 2 クラス分類問題のスコア付け実験

今度は、Web サービスの入力と出力を設定する必要があります。入力は、[モデルのスコア付け][score-model]の右の入力ポートであることは明らかです。これは、あやめの特徴の入力です。出力の選択は、予測されたクラス (スコア付けラベル)、スコア付け確率、その両方のどれを使用するかによって異なります。ここでは両方を使用します。目的の出力列を選ぶには、[プロジェクト列][project-columns]モジュールを使用する必要があります。[プロジェクト列][project-columns]モジュールをクリックし、右パネルで **[列セレクターの起動]** をクリックしたら、**[Scored Labels]** と **[Scored Probabilities]** を選択します。[プロジェクト列][project-columns]モジュールの出力ポートを設定し、もう一度実行したら、下部にある **[PUBLISH WEB SERVICE]** ボタンをクリックすれば、スコア付け実験を Web サービスとして発行する準備が完了します。最終的な実験は、図 4 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/4.png)

図 4: あやめの 2 クラス分類問題の最終スコア付け実験

Web サービスを実行し、テスト インスタンスの特徴の値を入力すると、得られた結果から 2 つの数値が返されます。最初の数値はスコア付けラベルで、2 つ目の数値はスコア付け確率です。この花は 0.9655 の確率を持つクラス 1 として予測されました。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/5.png)

図 5: あやめの花の 2 クラス分類の Web サービス結果

###多クラス分類
**実験例**

この実験では、多クラス分類の例として、文字認識タスクを実行します。分類器は、手書きのイメージから抽出されたいくつかの手書き属性値に基づいて、特定の文字 (クラス) を予測しようとします。トレーニング データには、手書き文字イメージから抽出された特徴が 16 個あります。アルファベット 26 文字によって、26 クラスが形成されます。図 6 に示すように、文字認識用に多クラス分類モデルをトレーニングし、テスト データセットと同じ特徴のセットを予測するように実験が設定されています。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/6.png)

図 6: 文字認識の多クラス分類問題の実験

[モデルのスコア付け][score-model]モジュールの出力ポートを右/左クリックしてから **[視覚化]** をクリックし、[モデルのスコア付け][score-model]モジュールから得られた結果を視覚化すると、図 7 に示すウィンドウが表示されます。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/7.png)

図 7: 多クラス分類でモデルのスコア付けの結果を視覚化する

**結果の解釈**

左の 16 列は、テスト セットの特徴の値を示します。「Scored Probabilities for Class "XX"」という名前の列は、2 クラスの場合における「Scored Probabilities」列と同じです。これらの列は、対応するエントリが特定のクラスに分類される確率を示しています。たとえば、最初のエントリが "A" である確率は 0.003571 であり、"B" である確率は 0.000451 であることを示しています。最後の「Scored Labels」列は、2 クラスの場合の「Scored Labels」と同じです。対応するエントリの予測クラスとして、最も高いスコア付け確率を持つクラスを選択します。たとえば最初のエントリでは、最も高い確率 "F" (0.916995) を持つ 2 つ目のラベルが "F" になります。

**Web サービスの発行**

この例では、[プロジェクト列][project-columns]で Web サービスの出力としていくつかの列を選ぶのではなく、各エントリのスコア付けラベルと、スコア付けラベルの確率を取得します。基本的なロジックは、すべてのスコア付け確率の中から最も高い確率を見つけることです。そのためには、[R スクリプトの実行][execute-r-script]モジュールを使用する必要があります。図 8 に R コードを、図 9 に実験を示します。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/8.png)

図 8: スコア付けラベルとそのラベルに関連付けられた確率を抽出する R コード
  
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9.png)

図 9: 文字認識の多クラス分類問題の最終スコア付け実験

Web サービスを発行して実行し、一部の入力特徴値を入力した後に返される結果は、図 10 のようになります。抽出された 16 の特徴を持つこの手書き文字は、0.9715 の確率を持つ "T" であると予測されます。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/10.png)

図 10: あやめの 2 クラス分類の Web サービス結果

##回帰

回帰問題は分類問題とは異なります。分類問題では、あやめが属するクラスなどの個別のクラスを予測します。回帰問題では、次の例に示すような、自動車価格などの連続変数を予測します。

**実験例**

回帰の例として、自動車価格の予測を使用します。製造、燃料の種類、車体タイプ、駆動輪などの特徴に基づいて、自動車価格を予測します。図 11 に実験を示します。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/11.png)

図 11: 自動車価格の回帰問題の実験

[モデルのスコア付け][score-model]モジュールを視覚化すると、結果は図 12 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/12.png)

図 12: 自動車価格の予測問題のスコア付け結果を視覚化する

**結果の解釈**

このスコア付け結果では、「Scored Labels」が結果の列になります。数値は、各車に対して予測された価格です。

**Web サービスの発行**

回帰実験を Web サービスに発行し、2 クラス分類で使用した場合と同じ方法で自動車価格の予測を呼び出すことができます。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13.png)

図 13: 自動車価格の回帰問題のスコア付け実験

Web サービスを実行して返された結果は、図 14 のようになります。この車の予測価格は 15085.52 です。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/14.png)

図 14: 自動車価格の回帰問題の Web サービス結果

##クラスタリング

**実験例**

あやめデータセットもう一度使用して、クラスタリングの実験をビルドします。ここでは、データセットでクラス ラベルを除外し、クラスタリングで使用可能な特徴のみが含まれるようにします。このあやめの使用例では、トレーニング プロセス中のクラスター数を 2 に設定することで、花を 2 つのクラスにクラスタリングします。図 15 に実験を示します。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/15.png)

図 15: あやめのクラスタリング問題の実験

クラスタリングは、トレーニング データセットがグランドトルースのラベルを単独で持たない点で、分類とは異なります。代わりに、ここではトレーニング データセットのインスタンスを個別のクラスターにグループ化する方法を使用します。トレーニング プロセス中に、モデルは特徴ごとの違いを学習してエントリにラベルを付けます。その後、トレーニング済みモデルを使用して、後続のエントリをさらに分類できます。クラスタリング問題で使用する結果には 2 つのパートがあります。最初のパートは、トレーニング データセットにラベルを付ける方法、2 つ目のパートは、トレーニング済みモデルを使用して新しいデータセットを分類する方法です。

結果の最初のパートは、[トレーニング クラスタリング モデル][train-clustering-model] モジュールの左側の出力ポートをクリックし、**[視覚化]** をクリックすると視覚化できます。図 16 は、視覚化ウィンドウを示します。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/16.png)

図 16: トレーニング データセットのクラスタリング結果を視覚化する

図 17 には、2 つ目のパートの結果である、トレーニング済みクラスタリング モデルによる新しいエントリのクラスタリングを示します。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/17.png)

図 17: 新しいデータセットでクラスタリング結果を視覚化する

**結果の解釈**

2 つのパートの結果は別の実験ステージから生じていますが、外観はまったく同じであり、同じ方法で解釈されます。最初の 4 つの列が特徴です。最後の「Assignments」列は、予測結果です。同じ番号が割り当てられているエントリは、同じクラスターにあると予測されます。すなわち、何らかの方法 (この実験では既定のユークリッド距離メトリックを使用) で類似点を共有します。前の手順で、クラスターの数を 2 に指定しました。このため、「Assignments」列のエントリは、0 または 1 とラベル付けされます。

**Web サービスの発行**

クラスタリング実験を Web サービスに発行し、2 クラス分類で使用した場合と同じ方法でクラスタリング予測用に呼び出すことができます。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18.png)

図 18: あやめのクラスタリング問題のスコア付け実験

Web サービスを実行して返された結果は、図 19 のようになります。この花は、クラスター 0 に属していると予測されます。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19.png)

図 19: あやめの 2 クラス分類の Web サービス結果

##レコメンダー システム
**実験例**

レコメンダー システムでは、顧客の評価履歴に基づいて顧客にレストランを勧める、レストランの推奨問題を例として使用します。入力データは、3 つのパートで構成されます。

* 顧客によるレストランの評価 
* 顧客の特徴データ 
* レストランの特徴データ 

Azure Machine Learning の組み込み[マッチボックス レコメンダーのトレーニング][train-matchbox-recommender] モジュールを使用し、次のようなことを処理できます。

- 特定のユーザーと項目の評価を予測する
- 特定のユーザーに項目を推奨する
- 特定のユーザーに関連するユーザーを検索する
- 特定の項目に関連する項目を検索する

右側のパネルの **[Recommender prediction kind] **メニューにある 4 つのオプションから実行内容を選択できます。ここでは、4 つのすべてのシナリオについて説明します。レコメンダー システム用の一般的な Azure Machine Learning の実験は、図 20 のようになります。これらのレコメンダー システム モジュールの使用方法については、ヘルプ ページの「[マッチボックス レコメンダーのトレーニング][train-matchbox-recommender]」と「[スコア マッチボックス レコメンダー][score-matchbox-recommender]」をご覧ください。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/20.png)

図 20: レコメンダー システムの実験

**結果の解釈**

*特定のユーザーと項目の評価を予測する*

**[Recommender prediction kind]** メニューで評価の予測を選択し、レコメンダー システムで特定のユーザーと項目に対する評価を予測するよう指定します。[スコア マッチボックス レコメンダー][score-matchbox-recommender]出力の視覚化は、図 21 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/21.png)

図 21: レコメンダー システムのスコア結果を視覚化する - 評価予測

3 つの列があります。最初の 2 つの列は、入力データによって提供されるユーザーと項目のペアです。3 つ目の列は、特定の項目に対して予測されるユーザーの評価です。たとえば最初の行では、顧客 U1048 がレストラン 135026 を 2 と評価することが予測されます。

*特定のユーザーに項目を推奨する*

**[Recommender prediction kind]** メニューで **[Item Recommendation]** を選択し、レコメンダー システムで特定のユーザーに対して項目を勧めるよう指定します。このシナリオでは、Recommended item selection パラメーターも選ぶ必要があります。オプション **[From Rated Items (for model evaluation)]** は主に、トレーニング プロセス中のモデルの評価で使用します。この予測ステージでは、**[From All Items]** を選択します。[スコア マッチボックス レコメンダー][score-matchbox-recommender]出力の視覚化は、図 22 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/22.png)

図 22: レコメンダー システムのスコア結果を視覚化する - 項目の推奨

6 つの列があります。最初の列は、入力データによって提供される、推奨項目に対する特定のユーザー ID を示します。残りの 5 つの列は、ユーザーに勧める項目を、関連性が高い順に並べ替えて示します。たとえば最初の行では、顧客 U1048 に対して最も推奨されるレストランが 134986 であり、その後に 135018、134975、135021、132862 が続いています。

*特定のユーザーに関連するユーザーを検索する*

[Recommender prediction kind] メニューで関連するユーザーを選択し、レコメンダー システムで特定のユーザーに関連するユーザーを検索するよう指定します。関連するユーザーとは、類似する嗜好を持つユーザーのことです。このシナリオでは、Related user selection パラメーターも選ぶ必要があります。オプション [From Users That Rated Items (for model evaluation)] は主に、トレーニング プロセス中のモデルの評価で使用します。この予測ステージでは、[From All Users] を選択します。[スコア マッチボックス レコメンダー][score-matchbox-recommender]出力の視覚化は、図 23 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/23.png)

図 23: レコメンダー システムのスコア結果を視覚化する - 関連ユーザー

6 つの列があります。最初の列は、入力データによって提供される、関連ユーザーの検索に使用される特定のユーザー ID です。残りの 5 つの列は、ユーザーに関連することが予測されるユーザーを、関連性の高い順に並べ替えて表示します。たとえば最初の行では、顧客 U1048 に最も関連性が高い顧客は U1051 であり、その後に U1066、U1044、U1017、U1072 が続いています。

**特定のユーザーに関連する項目を検索する**

**[Recommender prediction kind]** メニューで **[Related Items]** を選択し、レコメンダー システムで特定の項目に関連する項目を検索するよう指定します。関連アイテムとは、同じユーザーが満足する可能性が最も高い項目です。このシナリオでは、Related item selection パラメーターも選ぶ必要があります。オプション **[From Rated Items (for model evaluation)]** は主に、トレーニング プロセス中のモデルの評価で使用します。この予測ステージでは、**[From All Items]** を選択します。[スコア マッチボックス レコメンダー][score-matchbox-recommender]出力の視覚化は、図 24 のようになります。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/24.png)

図 24: レコメンダー システムのスコア結果を視覚化する - 関連項目

6 つの列があります。最初の列は、入力データによって提供される、関連する項目の検索に使用される特定の項目 ID を表示します。残りの 5 つの列は、項目に関連することが予測される項目を、関連性が高い順に並べ替えて表示します。たとえば、最初の行では、項目 135026 に最も関連する項目は 135074 であり、その後に 135035、132875、135055、134992 が続きます。Web サービスの発行

これらの実験を Web サービスとして発行し、予測を得るプロセスは、4 つのシナリオで類似しています。ここでは、2 つ目のシナリオである、特定のユーザーへの推奨項目を例として使用します。その他の 3 つのシナリオでも、同じ手順を実行できます。

トレーニング済みモデルとしてトレーニング済みリコメンダー システムを保存し、入力データを指定した 1 人のユーザー ID 列にフィルター処理します。図 25 のように実験を組み立て、Web サービスとして発行できます。

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25.png)
 
図 25: レストランの推奨問題のスコア付け実験

Web サービスを実行して返された結果は、図 14 のようになります。ユーザー U1048 に対して推奨される 5 つのレストランは、134986、135018、134975、135021、132862 です。
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/26.png)

図 26: レストランの推奨問題の Web サービス結果


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/

<!--HONumber=54-->