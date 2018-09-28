---
title: Azure Machine Learning Data Prep SDK でデータを書き込む - Python
description: Azure Machine Learning Data Prep SDK を使用してデータを書き込む方法について説明します。 データ フローの任意の時点でデータを書き込むことができます。また、サポートされている任意の場所 (ローカル ファイル システム、Azure Blob Storage、Azure Data Lake Storage) のファイルに書き込むことができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: da5823473b7f69fa0a6c65d5ea7319bfd2e92720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946767"
---
# <a name="write-data-with-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK でデータを書き込む
データ フローの任意の時点でデータを書き込むことができます。 これらの書き込みは、結果のデータ フローへのステップとして追加され、データ フローがあるたびに実行されます。 パイプライン内に存在する書き込みのステップ数に制限はないため、トラブルシューティングのために中間結果を書き出したり、他のパイプラインから選択したりする処理が簡単です。 各書き込みステップを実行すると、データ フロー内の全データのプルが実行される点に注意することが重要です。 たとえば、3 つの書き込みステップがあるデータ フローの場合、データ セット内の各レコードの読み取りと処理が 3 回行われます。

## <a name="writing-to-files"></a>ファイルへの書き込み
[Azure Machine Learning Data Prep SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep?view=azure-dataprep-py) を使用すると、サポートされている任意の場所 (ローカル ファイル システム、Azure Blob Storage、Azure Data Lake Storage) のファイルにデータを書き込むことができます。 データは複数のパーティション ファイルに書き込まれ、並列書き込みが可能です。 書き込みが完了すると、SUCCESS という名前のセンチネル ファイルも生成されます。 これにより、パイプライン全体が完了するのを待つことなく、中間書き込みが完了したときを特定できます。

Spark でデータ フローを実行する場合は、空のフォルダーに書き込む必要があります。 既存フォルダーへの書き込みを実行しようとすると失敗します。 ターゲット フォルダーが空であることを確認するか、実行ごとに別のターゲットの場所を使用してください。そうしないと、書き込みは失敗します。

Azure Machine Learning Data Prep SDK は、次のファイル形式をサポートしています。
-   区切りファイル (CSV や TSV など)
-   Parquet ファイル

この例では、まずデータ フローにデータを読み込みます。 このデータをさまざまな形式で再利用します。

```

import azureml.dataprep as dprep
t = dprep.smart_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```   

|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   なし|       NO|     NO  |   ENRS    |NaN    |   NaN |   NaN|    
|   1|      10003.0 |   99999.0 |   なし|       NO|     NO  |   ENSO|       NaN|        NaN |NaN|   
|   2|  10010.0|    99999.0|    なし|   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    なし|   NO| NO| |   NaN|    NaN|    NaN|
|4| 10014.0|    99999.0|    なし|   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    なし|   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   なし|   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    なし|   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    なし|   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    なし|   NO| SV|     |77000.0|   15500.0|    120.0|

## <a name="delimited-files"></a>区切りファイル
以下の行で、書き込みステップがある新しいデータ フローが作成されますが、実際の書き込みはまだ実行されていません。 データ フローが実行されると、書き込みも実行されます。

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))
```
それではデータ フローを実行しましょう。これで書き込み操作が実行されます。
```
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   ERROR |       NO|     NO  |   ENRS    |ERROR    |   ERROR |   ERROR|    
|   1|      10003.0 |   99999.0 |   ERROR |       NO|     NO  |   ENSO|       ERROR|        ERROR |ERROR|   
|   2|  10010.0|    99999.0|    ERROR |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    ERROR |   NO| NO| |   ERROR|    ERROR|    ERROR|
|4| 10014.0|    99999.0|    ERROR |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    ERROR |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   ERROR |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    ERROR |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    ERROR |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    ERROR |   NO| SV|     |77000.0|   15500.0|    120.0|

正しく解析されなかった数値があるため、書き込まれたデータの数値列にはいくつかのエラーがあります。 CSV に書き込まれると、既定でこれらの null 値は "ERROR" という文字列に置き換えられます。 書き込みの呼び出しの一部としてパラメーターを追加し、null 値を表すために使用する文字列を指定することができます。

```
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NO|     NO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NO|     NO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NO| NO| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NO| SV|     |77000.0|   15500.0|    120.0|
## <a name="parquet-files"></a>Parquet ファイル

 前述の `write_to_csv` 関数と同様に、`write_to_parquet` は、データ フローの実行時に実行される書き込みの Parquet ステップを含む新しいデータ フローを返します。

```
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```
 それではデータ フローを実行しましょう。これで書き込み操作が実行されます。

```
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NO| SV|     |77000.0|   15500.0|    120.0|
