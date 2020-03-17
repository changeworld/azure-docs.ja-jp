---
title: Azure Machine Learning デザイナーのサンプル データセットを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーに含まれているサンプル データセットについて説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037297"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning デザイナー (プレビュー) のサンプル データセットを使用する

Azure Machine Learning デザイナー (プレビュー) で新しいパイプラインを作成する場合、さまざまなサンプル データセットが既定で用意されています。 これらのサンプル データセットは、デザイナーのホームページのサンプル パイプラインで使用されます。 

サンプル データセットは、 **[データセット]** - **[サンプル]** カテゴリの下にあります。 これは、デザイナーのキャンバスの左側にあるモジュール パレットにあります。 これらのいずれのデータセットも、キャンバスにドラッグすることにより独自のパイプラインで使用できます。

## <a name="datasets"></a>データセット


| データセット名&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| データセットの説明 |
|-------------|:--------------------|
| 米国国勢調査局提供の、成人収入に関する二項分類データセット | 調整後の所得指数が 100 を超える就労成人男性 (16 歳以上) を対象とした、米国国勢調査局のデータベース (1994 年) のサブセットです。<br/>**[使用状況]** :人口統計データを使用して対象の人々を分類し、個人が 1 年間に 50,000 ドル以上の年収を得られるかどうかを予測します。<br/> **関連の研究**: Kohavi, R.、Becker, B. (1996 年)。 [UCI Machine Learning リポジトリ](https://archive.ics.uci.edu/ml)。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部|
|自動車価格データ (生データ)|メーカー/モデル別にまとめた自動車の情報です。価格、シリンダー数、燃費、保険リスク スコアなどの情報が含まれます。<br/> リスク スコアはまず車体価格に関連付けられます。 その後、アクチュアリー (保険数理士) の間でシンボリングと呼ばれるプロセスによって、実際のリスクに対して調整されます。 +3 は自動車のリスクが高く、-3 は高い安全性が見込まれることを示しています。<br/>**使用方法**:</b>回帰または多変量分類を使用して、機能別のリスク スコアを予測します。<br/>**関連の研究**:</b>Schlimmer, J.C. (1987 年)。 [UCI Machine Learning リポジトリ](https://archive.ics.uci.edu/ml)。 カリフォルニア州アーバイン:カリフォルニア大学、情報・コンピューター サイエンス学部。 |
| CRM 強い欲求ラベルの共有 |KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels))。|
|CRM 離反ラベルの共有|KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels))。|
|CRM データセットの共有 | このデータは、KDD Cup 2009 顧客関係の予測に関する課題から取得しています ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip))。 <br/>データセットには、French Telecom company Orange の顧客 50,000 人のデータが含まれます。 各顧客には匿名化された特徴が 230 あり、その中の 190 が数値で、40 がカテゴリです。 特徴はきわめて疎です。 |
|CRM アップセリング ラベルの共有|KDD Cup 2009 顧客間関係の予測に関する課題のラベル ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|フライト遅延データ|米国運輸省の TranStats データ コレクションから取得した旅客機の定時運航データ ([定時](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time))。<br/>データセットには、2013 年 4 月から 10 月までの期間のデータが含まれます。 デザイナーにアップロードする前に、データセットは次のように処理されました。 <br/>-    米国本土の混雑度が上位 70 までの空港を含めるように、データセットをフィルター処理しました <br/>- キャンセルされたフライトは 15 分超の遅延として分類しました <br/>- 迂回したフライトをフィルターで除外しました <br/>- 次の列が選択されました。Year、Month、DayofMonth、DayOfWeek、Carrier、OriginAirportID、DestAirportID、CRSDepTime、DepDelay、DepDel15、CRSArrTime、ArrDelay、ArrDel15、Cancelled|
|ドイツのクレジット カード UCI データセット|german.data ファイルを使用した、UCI Statlog (ドイツのクレジット カード) データセット ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)))。<br/>データセットは、低信用リスクまたは高信用リスクとして属性のセットで表現された人々を分類します。 サンプルはそれぞれ人を表します。 20 の特徴があり、数値とカテゴリの両方と、二項のラベル (信用リスク値) で構成されます。 高信用リスクのエントリにはラベル 2、低信用リスクのエントリにはラベル 1 が付きます。 低信用リスクのサンプルを高信用リスクとして誤って分類した場合のコストは 1 ですが、高信用リスクのサンプルを低信用リスクとして誤って分類した場合のコストは 5 です。|
|IMDB 映画のタイトル|このデータセットには、Twitter のツイートで評価された映画に関する情報として、IMDB 登録 ID、映画の名称、ジャンル、および制作年が含まれます。 約 17,000 件の映画の情報があります。 データセットは、S. Dooms, T. De Pessemier and L. Martens による論文 「MovieTweetings: a Movie Rating Dataset Collected From Twitter。 Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013」で紹介されました。|
|映画の評価|このデータセットは、ムービー ツイート データセットの拡張バージョンです。 Twitter で適切に構成されたツイートから抽出した、約 170,000 件の評価が含まれます。 各インスタンスは 1 つのツイートを表し、ユーザー ID、IMDB 登録 ID、評価、タイプスタンプ、このツイートのお気に入りの数、そして、このツイートのリツイートの数を組み合わせたタプルです。 データセットは、A. Said、S. Dooms、B. Loni、D. Tikk によって Recommender Systems Challenge 2014 のために提供されました。|
|天候データセット|NOAA の 1 時間ごとの陸上の気象観測 ([2013 年 4 月から 2013 年 10 月までのデータをマージ](https://az754797.vo.msecnd.net/data/WeatherDataset.csv))。<br/>気象データには、2013 年 4 月から 10 月までの期間の航空気象観測所での観測が含まれます。 デザイナーにアップロードする前に、データセットは次のように処理されました。    <br/> -    気象観測所 ID を、対応する空港 ID にマッピングしました    <br/> -    混雑度が上位 70 の空港に関連付けられていない気象観測所をフィルターで除外しました    <br/> -    Date 列を Year、Month、Day の各列に分割しました    <br/> - 次の列が選択されました。AirportID、Year、Month、Day、Time、TimeZone、SkyCondition、Visibility、WeatherType、DryBulbFarenheit、DryBulbCelsius、WetBulbFarenheit、WetBulbCelsius、DewPointFarenheit、DewPointCelsius、RelativeHumidity、WindSpeed、WindDirection、ValueForWindCharacter、StationPressure、PressureTendency、PressureChange、SeaLevelPressure、RecordType、HourlyPrecip、Altimeter|
|Wikipedia SP 500 データセット|データは、S&P 500 各企業に関する記事に基づいて Wikipedia (https://www.wikipedia.org/) から派生され、XML データとして格納されています。    <br/>デザイナーにアップロードする前に、データセットは次のように処理されました。    <br/> - 特定の各企業のテキスト コンテンツを抽出しました    <br/> -    Wiki の書式設定を削除しました    <br/> -    英数字以外の文字を削除しました    <br/> -    すべてのテキストを小文字に変換しました    <br/> -    既知の会社のカテゴリを追加しました    <br/>いくつかの企業の記事が見つからないため、レコード数は 500 未満であることに注意してください。|

## <a name="next-steps"></a>次のステップ

* 予測分析と機械学習の基本について学習する: [チュートリアル: デザイナーを使用して自動車の価格を予測する](tutorial-designer-automobile-price-train-score.md)

* 既存の[デザイナーのサンプル](samples-designer.md)を変更して、ニーズに合わせて調整する方法について説明します。
