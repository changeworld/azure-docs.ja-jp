<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="Machine Learning Sample: Flight delay prediction | Azure" description="A sample Azure Machine Learning experiment to develop a model that predicts whether a scheduled passenger flight will be delayed by more than 15 minutes." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning サンプル: フライト遅延予測

*ML Studio には、このモデルに対応したサンプル実験が用意されています。**[サンプル]** タブの **[実験]** セクションにあります。実験名は次のとおりです。*

    Sample Experiment - Flight Delay Prediction - Development

## 問題の説明

過去の定時運航データと気象データを使用して、スケジュール済みの旅客機の到着が 15 分を超えて遅延するかどうかを予測します。

## データ

**米国運輸省の TranStats データ コレクションから取得した旅客機の定時運航データ。** [][]<http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time></a>

データセットの範囲は、2013 年 4 月から 10 月です。AzureML Studio にアップロードする前に、次の処理を実行します。

-   米国本土の混雑度が上位 70 位までの空港を含めるように、データセットをフィルター処理します。
-   次の列を選択します。'Year'、'Month'、'DayofMonth'、'DayOfWeek'、'Carrier'、'OriginAirportID'、'DestAirportID'、'CRSDepTime'、'DepDelay'、'DepDel15'、'CRSArrTime'、'ArrDelay'、'ArrDel15'、'Cancelled'
-   キャンセルされたフライトは 15 分超の遅延として分類します。
-   迂回したフライトはフィルターで外されます。

**NOAA (海洋大気局) の場所別の時間別気象観測データ。** <http://cdo.ncdc.noaa.gov/qclcd_ascii/>

気象データは、各空港に関連付けられた気象観測所の 2013 年 4 月から 10 月の観測データをカバーします。ML Studio にアップロードする前に、次の処理を実行します。

-   気象観測所 ID を対応する空港 ID にマッピングします。
-   混雑度が上位 70 位の空港に関連付けられていない気象観測所をフィルターで外します。
-   Date 列を年、月、日の列に分離します。
-   次の列を選択します。'AirportID'、'Year'、'Month'、'Day'、'Time'、'TimeZone'、'SkyCondition'、'Visibility'、'WeatherType'、'DryBulbFarenheit'、'DryBulbCelsius'、'WetBulbFarenheit'、'WetBulbCelsius'、'DewPointFarenheit'、'DewPointCelsius'、'RelativeHumidity'、'WindSpeed'、'WindDirection'、'ValueForWindCharacter'、'StationPressure'、'PressureTendency'、'PressureChange'、'SeaLevelPressure'、'RecordType'、'HourlyPrecip'、'Altimeter'

## モデル

**フライト データ処理**

まず、ターゲットから漏れる可能性がある列 'DepDelay'、'DepDel15'、'ArrDelay'、'Cancelled' をデータセットから除外します。

列 'DayOfWeek'、'OriginAirportID'、'DestAirportID' は、カテゴリの属性を表します。ただし、これらは整数値であり、最初に連続する数値として解析されるため、メタデータ エディターを使用してカテゴリに変換する必要があります。

結合キーの 1 つとしてスケジュール済みの出発時刻を使用して、フライト レコードを毎時の気象レコードに結合します。そのために、CSRDepTime 列を直前の正時の値に切り下げます。

**気象データ処理**

まず、大部分が不足値で構成される列を除外します。次の列が対象です。

-   値が文字列の列すべて
-   ValueForWindCharacter、WetBulbFarenheit、WetBulbCelsius、PressureTendency、PressureChange、SeaLevelPressure、StationPressure

次に、不足値スクラブを残りの列に適用します。

気象観測の時刻は直後の正時へと切り上げられるため、フライト スケジュール済みの出発時刻と等価結合できます。スケジュール済みのフライト時刻と気象観測時刻は反対の方向に丸められることに注意してください。これは、フライト時刻よりも過去の気象観測のみがモデルで使用されるようにするためです。

**データセットの結合**

フライト レコードを、フライトの出発地とフライトの目的地の気象データと結合します。

気象データは現地時間で記録されていますが、出発地と目的地のタイム ゾーンが異なる可能性があります。そのため、出発時刻と気象観測時刻からタイム ゾーン列を減算して、タイム ゾーンの差を調整します。

**トレーニングと検証のサンプルの準備**

データをトレーニング用の 4 月から 9 月のレコードと検証用の 10 月のレコードに分けて、トレーニングのサンプルと検証のサンプルを作成します。その後、年と月の列はデータセットから削除します。

さらに、トレーニング データを同じ高さのビニングで量子化し、同じビニングを検証データに適用します。

**モデルのトレーニングと検証**

2 つのクラスのブースト デシジョン ツリー モデルをトレーニング サンプルを使用してトレーニングします。10 倍のランダム パラメーター スイープを使用して、AUC が最良になるようにモデルを最適化します。比較のために、2 つのクラスのロジスティック回帰モデルを同じ方法で最適化します。

トレーニング済みのモデルは検証セットに対してスコアが付けられ、お互いにモデルの品質を分析するために、"モデル評価" モジュールが使用されます。

**事後処理**

分析を容易にするために、空港 ID に人間による判読が可能な空港名と場所を結合します。

## 結果

ブースト デシジョン ツリー モデルでは検証セットに対して AUC が 0.697 となり、AUC が 0.675 のロジスティック回帰モデルよりもわずかに良い結果が出ています。

  []: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
