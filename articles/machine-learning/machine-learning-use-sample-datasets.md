<properties 
	pageTitle="Machine Learning Studio におけるサンプル データセットの使用 | Microsoft Azure" 
	description="ML Studio に含まれるサンプル モデルで使用されるデータセットの説明です。実験には、これらのサンプル データセットを使用できます。" 
	keywords="data sets,datasets,ml studio,sample data sets"
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
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Azure Machine Learning Studio におけるサンプル データセットの使用

[top]: #machine-learning-sample-datasets

Azure Machine Learning で新しいワークスペースを作成する場合、既定でさまざまなサンプルのデータセットと実験が含まれています。こうしたサンプル データセットの多くは、[Azure Machine Learning Model Gallery](http://azure.microsoft.com/documentation/services/machine-learning/models/) のサンプル モデルで使用されます。そのほかにも Machine Learning で使用するさまざまな種類のデータの例が含まれています。

データセットの一部は、Azure BLOB ストレージで使用できます。次の表ではこれらのデータセットへの直接のリンクを提供します。データセットは[リーダー][reader] モジュールを利用して実験に使用できます。データセットにアクセスする方法の例を確認するには、データセットに関連するサンプル実験の[リーダー][reader] モジュールのプロパティを表示します。

ML Studio で新しい実験を開くか作成する場合、サンプル データセットの残りは、実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** に一覧表示されます。実験キャンバスにこれらのデータセットのいずれかをドラッグすると、独自の実験で使用できます。


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Machine Learning Gallery 内のモデルに関連付けられているサンプル データセット

次のデータセットは [Azure Machine Learning Model Gallery](http://azure.microsoft.com/documentation/services/machine-learning/models/) でサンプル モデルによって使用されます。モデルと関連する実験の詳細については、テーブルのリンクからモデルの詳細を確認してください。

<table>

<tr>
  <th align=left>サンプル モデル</th>
  <th align=left>関連付けられている実験</th>
  <th align=left>データセットの名前</th>
  <th align=left>データセットの説明</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">K 平均法クラスタリングを使用した色彩量子化</a></td>
  <td valign=top>サンプル実験 - K 平均法クラスタリングを使用した色ベースの画像圧縮 - 開発</td>
  <td valign=top>Bill Gates の RGB 画像</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">クレジット リスク予測</a></td>
  <td valign=top>サンプル実験 - ドイツのクレジット - 開発</td>
  <td valign=top>ドイツのクレジット カード UCI データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">CRM タスク</a></td>
  <td rowspan=4 valign=top>サンプル実験 - CRM - 開発</td>
  <td valign=top>CRM データセットの共有</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>CRM 強い欲求ラベルの共有</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>CRM 離反ラベルの共有</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>CRM アップセリング ラベルの共有</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">類似した会社の特定</a></td>
  <td valign=top>サンプル実験 - S &amp; P 500 会社のクラスタリング - 開発</td>
  <td valign=top>Wikipedia SP 500 データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">フライト遅延予測</a></td>
  <td rowspan=3 valign=top>サンプル実験 - フライト遅延予測 - 開発</td>
  <td valign=top>空港コードのデータセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>フライト遅延データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>天候データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">ネットワーク不正侵入検出</a></td>
  <td valign=top>サンプル実験 - ネットワーク不正侵入検出 - 開発</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">自転車のレンタル数の予測</a></td>
  <td valign=top>サンプル実験 - 自転車の需要予測</td>
  <td valign=top>自転車レンタルの UCI データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">学生の成績の予測</a></td>
  <td valign=top>サンプル実験 - 学生の成績 - 開発</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">センチメント分析</a></td>
  <td valign=top>サンプル実験 - センチメント分類 - 開発</td>
  <td valign=top>Amazon の書評</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Machine Learning Studio のサンプル実験で使用されるデータ セット

次のデータセットは、[Machine Learning Studio](https://studio.azureml.net/Home) に付属のサンプル実験で使用されます。

ML Studio で新しい実験を開くか作成する場合、実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** にデータセットが一覧表示されています。実験キャンバスにこれらのデータセットのいずれかをドラッグすると、独自の実験で使用できます。

ここに表示されるサンプル実験はすべて ML Studio の **[実験]** ページにある **[サンプル]** タブにあります。編集可能なサンプル実験のコピーを作成するには、ML Studio で実験を開き、**[名前を付けて保存]** をクリックします。


<table>

<tr>
  <th align=left>データセットの名前</th>
  <th align=left>サンプル実験</th>
  <th align=left>データセットの説明</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>乳がん特徴
  <td valign=top>サンプル実験 - 乳がん - 開発</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>乳がん情報</td>
  <td valign=top><i>同じ</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>サンプル実験 - ダイレクト マーケティング - 開発 - トレーニング</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>IMDB 映画のタイトル</td>
  <td valign=top>サンプル実験 - ムービー リコメンデーション - 開発</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>ムービーのツイート</td>
  <td valign=top>サンプル実験 - 将来的な人気に従った映画のツイートのランク付け - 開発</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>サンプル実験 - ニュースのカテゴリ化 - 開発</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>同じ</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>同じ</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Machine Learning Studio の各種サンプル データセット

次のデータセットは [Machine Learning Studio](https://studio.azureml.net/Home) に含まれており、Machine Learning で使用されるさまざまな種類のデータの例を提供します。ML Studio で新しい実験を開くか作成する場合、実験キャンバスの左側にあるモジュール パレットの **[保存されたデータセット]** に一覧表示されます。実験キャンバスにこれらのデータセットのいずれかをドラッグすると、独自の実験で使用できます。

<table>
<tr>
  <th align=left>データセットの名前</th>
  <th align=left>説明</th>
</tr>

<tr>
  <td valign=top>米国国勢調査局提供の、成人収入に関する二項分類データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>自動車価格データ (生データ)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>献血データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>乳がんデータ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>エネルギー効率回帰データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>定刻フライト実績 (生データ)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>森林火災データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>あやめの 2 クラス データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>さまざまな自動車の燃費データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>ピマ族の糖尿病に関する二項分類データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>レストラン顧客データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>レストラン特徴データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>レストランの評価</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>鉄鋼のアニール処理に関するマルチクラス データセット</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>望遠鏡データ</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=July15_HO1-->