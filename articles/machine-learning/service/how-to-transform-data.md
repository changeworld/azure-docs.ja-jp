---
title: '変換: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning データ準備 SDK を利用したデータの変換と整理について説明します。 変換メソッドを使用して､列の追加や不要な行または列の除去､欠損値対処を行います｡
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d32244cd49ebd42192b2388215f79a64cacb3caa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53186142"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning データ準備 SDK を使ってデータを変換する

この記事では、[Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) を使用してデータを読み込むさまざまな方法について説明します。 SDK で提供されている関数を使って、列の追加や不要な行または列の除去、欠損値対処を簡単に行うことができます。

現在は次のタスク用の関数があります。

- [式による列の追加](#column)
- [欠損値の補完](#impute-missing-values)
- [例による列の派生](#derive-column-by-example)
- [Filtering](#filtering)
- [カスタム Python 変換](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>式による列の追加

Azure Machine Learning データ準備 SDK には `substring`式があり､これらの式を使用して、既存の列から値を算出して､その値を新しい列に挿入することができます。 この例では、データを読み込み、その入力データに列を加えます。

```python
import azureml.dataprep as dprep

# loading data
dataflow = dprep.read_csv(path=r'data\crime0-10.csv')
dataflow.head(3)
```

||ID|事件番号|日付|ブロック|IUCR|プライマリ タイプ|説明|場所の説明|逮捕|国内|...|区|コミュニティ エリア|FBI コード|X 座標|Y 座標|年|更新日|Latitude|Longitude|場所|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


`substring(start, length)` 式を使用して事件番号の列からプレフィックスを抽出し、新しい列 `Case Category` にその文字列を挿入します。 `substring_expression` 変数を `expression` パラメーターに渡すと、各レコードに対して式を実行する新しい計算列が作成されます。

```python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dataflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|事件番号|事件分類|日付|ブロック|IUCR|プライマリ タイプ|説明|場所の説明|逮捕|...|区|コミュニティ エリア|FBI コード|X 座標|Y 座標|年|更新日|Latitude|Longitude|場所|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|



`substring(start)` 式を使用して、Case Number 列から数値のみを抽出し、新しい列を作成します。 `to_number()` 関数を使用してそれを数値データ型に変換し、文字列の列名をパラメーターとして渡します。

```python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dataflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
case_id.head(3)
```

||ID|事件番号|事件 ID|日付|ブロック|IUCR|プライマリ タイプ|説明|場所の説明|逮捕|...|区|コミュニティ エリア|FBI コード|X 座標|Y 座標|年|更新日|Latitude|Longitude|場所|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|329907.0|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 |12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|329265.0|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|329253.0|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|

## <a name="impute-missing-values"></a>欠損値の補完

SDK では、指定した列の欠損値を補完できます。 この例では、緯度と経度値を読み込み、入力データ中の欠損値を補完します。

```python
import azureml.dataprep as dprep

# loading input data
df = dprep.read_csv(r'data\crime0-10.csv')
df = df.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
df = df.to_number(['Latitude', 'Longitude'])
df.head(5)
```

||ID|逮捕|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|(NaN)|(NaN)|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

3 番目のレコードは、緯度と経度の値が欠けています｡ これらの欠損値を補完するには、`ImputeMissingValuesBuilder` を使用して、修正された式を学習します。 これにより､算出された `MIN`、`MAX`、`MEAN` 値､または `CUSTOM` 値で、列を補完できます。 `group_by_columns` を指定すると、グループ別に算出された `MIN`、 `MAX`、および `MEAN` でグループごとに欠損値が補完されます｡

`summarize()` 関数を使って、緯度の列の `MEAN` 値を確認します。 この関数は、集計レベルを指定するための `group_by_columns` パラメーターで、列の配列を受け取ります。 `summary_columns` パラメーターは、`SummaryColumnsValue` の呼び出しを受け入れます。 この関数の呼び出しでは、現在の列名、新しい計算フィールド名、実行する `SummaryFunction` を指定します。

```python
df_mean = df.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
df_mean = df_mean.filter(dprep.col('Arrest') == 'false')
df_mean.head(1)
```

||逮捕|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

緯度の `MEAN` 値は正しいように見え、`ImputeColumnArguments` 関数を使ってそれを補完します。 この関数は、`column_id` の文字列と、補完の種類を指定する `ReplaceValueFunction` を受け取ります。 経度の欠損値の場合は、外部のナレッジに基づき 42 で補完します。

ビルダー関数 `impute_missing_values()` を使って、補完の手順を `ImputeMissingValuesBuilder` オブジェクトに連結できます。 `impute_columns` パラメーターは、`ImputeColumnArguments` オブジェクトの配列を受け取ります。 `learn()` 関数を呼び出して補完の手順を格納した後、`to_dataflow()` を使ってデータフロー オブジェクトに適用します。

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = df.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
# call learn() to learn a fixed program to impute missing values
impute_builder.learn()
# call to_dataflow() to get a data flow with impute step added
df_imputed = impute_builder.to_dataflow()

# check impute result
df_imputed.head(5)
```

||ID|逮捕|Latitude|Longitude|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|
|3|10139885|false|41.902152|-87.754883|
|4|10140379|false|41.885610|-87.657009|

上記の結果に見られるように､欠損緯度は `Arrest=='false'` グループの`MEAN` 値で補完されています｡ 欠損経度は 42 で補完されます｡

```python
imputed_longitude = df_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>列の派生の例

Azure Machine Learning Data Prep SDK にある高度なツールの 1 つは、目的の結果の例を使用して列を派生させる機能です。 これにより、SDK に列を提供して、目的の変換を実現するコードを生成できます。

```python
import azureml.dataprep as dprep
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dataflow.head(10)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|
|4|1/1/2015 3:54|FM-15|24|46|13|
|5|1/1/2015 4:00|FM-12|24|46|13|
|6|1/1/2015 4:54|FM-15|22|52|15|
|7|1/1/2015 5:54|FM-15|23|48|17|
|8|1/1/2015 6:54|FM-15|23|50|14|
|9|1/1/2015 7:00|FM-12|23|50|14|

このファイルは、日付と時刻の形式が "Mar 10, 2018 | 2AM-4AM" のデータセットと結合する必要があります。

```python
builder = dataflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=df.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview() 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|
|5|1/1/2015 4:00|Jan 1, 2015 4AM-6AM|
|6|1/1/2015 4:54|Jan 1, 2015 4AM-6AM|
|7|1/1/2015 5:54|Jan 1, 2015 4AM-6AM|
|8|1/1/2015 6:54|Jan 1, 2015 6AM-8AM|
|9|1/1/2015 7:00|Jan 1, 2015 6AM-8AM|

上記のコードでは、最初に派生列に対するビルダーを作成しています｡ 対象となる一連の元の列 (`DATE`) と追加する新しい列の名前を指定しています。 最初の例として、2 行目 (index 1) を渡し､導出列の期待値を指定しています。

最後に、`builder.preview()` を呼び出しています。元の列の横に派生列があります。 形式は正しそうですが、同じ日付 "Jan 1, 2015" に対する値があるだけです。

さて､先頭から `skip` を行いたい行数を渡すと､下方向に行が表示されます｡

```
builder.preview(skip=30)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|34|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|
|35|1/2/2015 1:54|Feb 1, 2015 12AM-2AM|
|36|1/2/2015 2:54|Feb 1, 2015 2AM-4AM|
|37|1/2/2015 3:54|Feb 1, 2015 2AM-4AM|
|38|1/2/2015 4:00|Feb 1, 2015 4AM-6AM|
|39|1/2/2015 4:54|Feb 1, 2015 4AM-6AM|

ここで、生成されたプログラムに問題があることが分かります。 上記で指定した 1 つの例だけに基づいて、派生プログラムは日付を "日/月/年" の形式で解析しています。これは、このケースで望んでいる形式ではありません。 この問題を修正するには、`builder` 変数で `add_example()` 関数を使って、別の例を提供します。

```python
builder.add_example(source_data=preview_df.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|30|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|31|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|32|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|33|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|34|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|35|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|36|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|37|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|38|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|39|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

これで、行は "1/2/2015" を "Jan 2, 2015" として正しく処理するようになりましたが、派生列を下に辿っていくと、派生列の最後の値が何もないことが分かります。 これを解決するには、行 66 に別の例を指定する必要があります。

```python
builder.add_example(source_data=preview_df.iloc[66], example_value='Jan 29, 2015 8PM-10PM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|
|5|1/2/2015 1:54|Jan 2, 2015 12AM-2AM|
|6|1/2/2015 2:54|Jan 2, 2015 2AM-4AM|
|7|1/2/2015 3:54|Jan 2, 2015 2AM-4AM|
|8|1/2/2015 4:00|Jan 2, 2015 4AM-6AM|
|9|1/2/2015 4:54|Jan 2, 2015 4AM-6AM|

日付と時刻を "|" で分離するには、別の例を追加します。 今回は､プレビューから行を渡さずに､`source_data` パラメーターの値に対する列名辞書を構築します｡。

```python
builder.add_example(source_data={'DATE': '11/11/2015 0:54'}, example_value='Nov 11, 2015 | 12AM-2AM')
builder.preview(count=10)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|なし|
|1|1/1/2015 23:54|なし|
|2|1/1/2015 23:59|なし|
|3|1/2/2015 0:54|なし|
|4|1/2/2015 1:00|なし|
|5|1/2/2015 1:54|なし|
|6|1/2/2015 2:54|なし|
|7|1/2/2015 3:54|なし|
|8|1/2/2015 4:00|なし|
|9|1/2/2015 4:54|なし|

これがマイナスの働きをしていることは明らかです｡これだと､派生列に値がある行だけが指定した例と完全に一致する行になります｡ builder オブジェクトで `list_examples()` を呼び出して、現在の例の派生の一覧を表示します。

```python
examples = builder.list_examples()
```

| |DATE|例|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|
|3|11/11/2015 0:54|Nov 11, 2015 \| 12AM-2AM|-4|

この場合は、一貫性のない例が提供されています。 この問題を解決するには、最初の 3 つの例を正しい例 (日付と時刻の間の "|" など) に置き換えます。

正しくない例を削除し (pandas DataFrame から `example_row` で渡すか､`example_id` 値で渡すことにより)､変更した新しい例を追加し直すことで、一貫性のない例を修正します。

```python
builder.delete_example(example_id=-1)
builder.delete_example(example_row=examples.iloc[1])
builder.delete_example(example_row=examples.iloc[2])
builder.add_example(examples.iloc[0], 'Jan 1, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[1], 'Jan 2, 2015 | 12AM-2AM')
builder.add_example(examples.iloc[2], 'Jan 29, 2015 | 8PM-10PM')
builder.preview()
```

| | DATE | date_timerange |
| -------- | -------- | -------- |
| 0 | 1/1/2015 0:54 | Jan 1, 2015 \| 12AM-2AM |
| 1 | 1/1/2015 1:00 | Jan 1, 2015 \| 12AM-2AM |
| 2 | 1/1/2015 1:54 | Jan 1, 2015 \| 12AM-2AM |
| 3 | 1/1/2015 2:54 | Jan 1, 2015 \| 2AM-4AM |
| 4 | 1/1/2015 3:54 | Jan 1, 2015 \| 2AM-4AM |
| 5 | 1/1/2015 4:00 | Jan 1, 2015 \| 4AM-6AM|
| 6 | 1/1/2015 4:54 | Jan 1, 2015 \| 4AM-6AM|
| 7 | 1/1/2015 5:54 | Jan 1, 2015 \| 4AM-6AM|
| 8 | 1/1/2015 6:54 | Jan 1, 2015 \| 6AM-8AM|
| 9 | 1/1/2015 7:00 | Jan 1, 2015 \| 6AM-8AM|

データは正しそうなので、builder で `to_dataflow()` を呼び出します。これは、追加された目的の派生列でデータ フローを返します。

```python
dataflow = builder.to_dataflow()
df = dataflow.to_pandas_dataframe()
```

## <a name="filtering"></a>フィルター処理

SDK には、列または行をフィルター処理して除外する`Dataflow.drop_columns` および `Dataflow.filter` のメソッドが含まれています｡

### <a name="initial-setup"></a>初期セットアップ

```python
import azureml.dataprep as dprep
from datetime import datetime
dataflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>列のフィルター処理

列をフィルター処理するには､`Dataflow.drop_columns` を利用します｡ このメソッドは、除外する列のリスト､またはもっと複雑な､`ColumnSelector` という名前の引数を受け取ります｡

#### <a name="filtering-columns-with-list-of-strings"></a>文字列のリストによる列のフィルター処理

この例では､`drop_columns` は文字列のリストを受け取ります｡ 各文字列は、除外する列と正確に一致している必要があります。

```python
dataflow = dataflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|なし|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|0|0|0|0|1|.00|0|0|3.25|

#### <a name="filtering-columns-with-regex"></a>regex 正規表現による列のフィルター処理

もう 1 つの方法としては、`ColumnSelector` 式を使って、正規表現に一致する列を除外します。 この例では、表現 `Column*|.*longitude|.*latitude` に一致するすべての列を除外します。

```python
dataflow = dataflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|なし|なし|なし|なし|なし|なし|なし|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|1|.00|0|0|3.25|

## <a name="filtering-rows"></a>行のフィルター処理

行をフィルター処理するには､`DataFlow.filter` を利用します。 このメソッドは、引数として Azure Machine Learning データ準備 SDK の式を受け取り、式が True と評価された行で新しいデータ フローを返します。 式は、式ビルダー (`col`、 `f_not`、 `f_and`、 `f_or`) と通常の演算子 (>、<、>、=、< =、= =、! =) を使用して構築します。

### <a name="filtering-rows-with-simple-expressions"></a>単純式による行のフィルター処理

式ビルダー `col` を使用し、文字列引数 `col('column_name')` として列名を指定します。 この式を標準の演算子 >、<、>=、<=、==、!= のいずれかと組み合わせて使用し、`col('Tip_amount') > 0` のような式を作成します。 最後に、`Dataflow.filter` 関数にビルド式を渡します｡

この例では､`dataflow.filter(col('Tip_amount') > 0)` は `Tip_amount` の値が 0 より大きい行で新しいデータ フローを返します｡

> [!NOTE] 
> `Tip_amount` は先ず数値に変換されます｡これで､その値と他の数値とを比較する式を構築できます｡

```python
dataflow = dataflow.to_number(['Tip_amount'])
dataflow = dataflow.filter(dprep.col('Tip_amount') > 0)
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0.30|0|3.8|
|2|2013-08-05 14:11:42|2013-08-05 14:12:47|1|.00|1.05|0|4.55|
|3|2013-08-05 14:15:56|2013-08-05 14:18:04|5|.00|2.22|0|5.72|
|4|2013-08-05 14:42:14|2013-08-05 14:42:38|1|.00|0.88|0|4.38|

### <a name="filtering-rows-with-complex-expressions"></a>複雑式による行のフィルター処理

複雑式を使ってフィルター処理するには､1 つまたは複数の単純式と式ビルダー `f_not`、 `f_and`、または`f_or` とを組み合わせます｡

この例では､`Dataflow.filter` は､ `'Passenger_count'`が 5 より小さく、`'Tolls_amount'` が 0 より大きい行で新しいデータ フローを返します｡。

```python
dataflow = dataflow.to_number(['Passenger_count', 'Tolls_amount'])
dataflow = dataflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2.25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|
|2|2013-08-12 19:48:12|2013-08-12 20:03:42|1.0|5.50|1.00|10.66|30.66|
|3|2013-08-13 06:11:06|2013-08-13 06:30:28|1.0|9.57|7.47|5.33|44.8|
|4|2013-08-16 20:33:50|2013-08-16 20:48:50|1.0|5.63|3.00|5.33|27.83|

複数の式ビルダーを組み合わせて入れ子式にする行をフィルター処理することもできます。

> [!NOTE]
> `lpep_pickup_datetime` および `Lpep_dropoff_datetime` は先ず datetime 値に変換されます｡これで他の datetime 値とその値を比較する式を構築できます｡

```python
dataflow = dataflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dataflow = dataflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dataflow = dataflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dataflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8.22|8.08|5.33|40.41|
|2|2013-08-25 09:12:52+00:00|2013-08-25 09:34:34+00:00|1.0|8.80|8.33|5.33|41.66|
|3|2013-08-25 16:46:51+00:00|2013-08-25 17:13:55+00:00|2.0|9.66|7.37|5.33|44.20|
|4|2013-08-25 17:42:11+00:00|2013-08-25 18:02:57+00:00|1.0|9.60|6.87|5.33|41.20|

## <a name="custom-python-transforms"></a>カスタム Python 変換

変換を行う最も簡単なオプションは独自のスクリプトを記述する場合であるというシナリオが常に存在します。 SDK では、カスタム Python スクリプト用に使用できる 3 つの拡張ポイントが提供されています。

- 新しいスクリプト列
- 新しいスクリプト フィルター
- パーティションの変換

どの拡張も、スケール アップおよびスケール アウトの両方のランタイムでサポートされています。 これらの拡張ポイントを利用する主な利点は、データ フレームを作成するために必ずしもすべてのデータをプルする必要がなくなることです｡ カスタム Python コードは他の変換と同様に大規模､パーティションごと､あるいは通常の並列動作をします｡

### <a name="initial-data-preparation"></a>初期データの準備

最初に Azure Blob からデータを読み込みます｡

```python
import azureml.dataprep as dprep
col = dprep.col

df = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
df.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |
|2|ALABAMA|1|101710|Hale County|10171002156| |
|3|ALABAMA|1|101710|Hale County|10171000588|2|
|4|ALABAMA|1|101710|Hale County|10171000589| |

データセットを縮小したり、いくつかの基本的な変換を行えます。

```python
df = df.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
df = df.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
df = df.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
df.head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|なし|
|1|ALABAMA|Hale County|1.017100e + 10|なし|
|2|ALABAMA|Hale County|1.017100e + 10|なし|
|3|ALABAMA|Hale County|1.017100e + 10|2|
|4|ALABAMA|Hale County|1.017100e + 10|なし|

次のフィルターを使用して null 値を探します。

```python
df.filter(col('MAM_MTH00numvalid_1011').is_null()).head(5)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|なし|
|1|ALABAMA|Hale County|1.017100e + 10|なし|
|2|ALABAMA|Hale County|1.017100e + 10|なし|
|3|ALABAMA|Hale County|1.017100e + 10|なし|
|4|ALABAMA|Hale County|1.017100e + 10|なし|

### <a name="transform-partition"></a>パーティションの変換

Pandas 関数を使用して、すべての null 値を 0 に置き換えます。 このコードは、一度にデータセット全体に対してではなく、パーティションごとに実行されます。 これは、大規模なデータセットでは､ランタイムがパーティションごとにデータを処理しながら、このコードは並列動作できることを意味します。

Python スクリプトでは、2 つの引数 `df` と `index` を受け取る `transform()` という名前の関数を定義する必要があります。 `df` 引数はパーティションのデータを含む pandas データフレームであり、`index` 引数はパーティションの一意識別子です。 transform 関数では、渡されたデータフレームを完全に編集できますが、データフレームを返す必要があります。 Python スクリプトでインポートされるすべてのライブラリが、データフローが実行される環境に存在する必要があります。

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(5)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|1.017100e + 10|0.0|
|2|ALABAMA|Hale County|1.017100e + 10|0.0|
|3|ALABAMA|Hale County|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|1.017100e + 10|0.0|

### <a name="new-script-column"></a>新しいスクリプト列

Python コードで､郡 (county) 名と州 (state) 名からなる新しい列を作成し、州名を頭大文字かすることもできます｡ このためには、データ フローに対して `new_script_column()` メソッドを使用します｡

Python スクリプトでは、1 つの引数 `row` を受け取る `newvalue()` という名前の関数を定義する必要があります。 `row` 引数はディクショナリ (`key`: 列名、`val`: 現在の値) であり、データ セットの行ごとにこの関数に渡されます。 この関数は、新しい列で使われる値を返す必要があります。 Python スクリプトでインポートされるすべてのライブラリが、データフローが実行される環境に存在する必要があります。

```python
df = df.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|2|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|
|3|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|2.0|
|4|ALABAMA|Hale County|Hale County, Alabama|1.017100e + 10|0.0|

### <a name="new-script-filter"></a>新しいスクリプト フィルター

データ セットをフィルター処理して、新しい `county_state` 列に "Hale" が含まれていない行だけにする Python 式を作成します。 この式は行を残す場合は `True`、行を除外する場合は `False` を返します｡

```python
df = df.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
df.head(5)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|2|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|3|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
|4|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e + 10|0.0|
