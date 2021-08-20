---
title: MVAD データ スキーマ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 3be9d5d50d34eee35de38b7b28dc1d42217ace24
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292635"
---
### <a name="input-data-schema"></a>入力データ スキーマ

MVAD は、メトリックのグループからの異常を検出します。各メトリックを **変数** または時系列と呼びます。

* Microsoft ([https://aka.ms/AnomalyDetector/MVADSampleData](https://aka.ms/AnomalyDetector/MVADSampleData)) からサンプル データ ファイルをダウンロードして、承認されたスキーマを確認することができます。
* 各変数には、`timestamp` と `value` の 2 つのフィールドだけが必要で、コンマ区切り値 (CSV) ファイルに格納する必要があります。
* CSV ファイルの列名は、大文字と小文字が区別されるため、正確に `timestamp` と `value` にする必要があります。
* `timestamp` 値は、ISO 8601 に準拠している必要があります。`value` は、整数または小数点以下の桁数が任意の小数にすることができます。
    CSV ファイルの内容の良い例を次に示します。

    |timestamp | value|
    |-------|-------|
    |2019-04-01T00:00:00Z| 5|
    |2019-04-01T00:01:00Z| 3.6|
    |2019-04-01T00:02:00Z| 4|
    |...| ...|

    > [!NOTE]
    > timestamp に時間、分、秒が含まれている場合は、API を呼び出す前にこれらが適切に切り上げられていることを確認します。
    >
    > たとえば、データ頻度が 30 秒ごとに 1 つのデータ ポイントと想定されていても、"12:00:01" や "12:00:28" のような timestamp が表示されている場合、それは timestamp を "12:00:00" や "12:00:30" のような新しい値に事前に処理する必要があるという強力なシグナルです。
    >
    > 詳細については、ベスト プラクティスのドキュメントの「[timestamp の切り上げ](../concepts/best-practices-multivariate.md#timestamp-round-up)」セクションを参照してください。
* CSV ファイルの名前は変数名として使用されるため、一意である必要があります。 たとえば、"temperature.csv" や "humidity.csv" などです。
* トレーニング用の変数と推論用の変数は、一致している必要があります。 たとえば、トレーニングに `series_1`、`series_2`、`series_3`、`series_4`、`series_5` を使用する場合は、推論に対してまったく同じ変数を指定する必要があります。
* CSV ファイルは ZIP ファイルに圧縮し、Azure BLOB コンテナーにアップロードする必要があります。 ZIP ファイルには任意の名前を付けることができます。

#### <a name="folder-structure"></a>フォルダー構造

データ準備でよくある間違いは、ZIP ファイル内の余分なフォルダーです。 たとえば、ZIP ファイルの名前が `series.zip` だとします。 次に、このファイルを新しいフォルダー `./series` に展開すると、CSV ファイルへの **正しい** パスは `./series/series_1.csv` で、**間違った** パスは `./series/foo/bar/series_1.csv` になる可能性があります。

Windows で ZIP ファイルを展開した後のディレクトリ ツリーの正しい例

```bash
.
└── series
    ├── series_1.csv
    ├── series_2.csv
    ├── series_3.csv
    ├── series_4.csv
    └── series_5.csv
```

Windows で ZIP ファイルを展開した後のディレクトリ ツリーの間違った例

```bash
.
└── series
    └── series
        ├── series_1.csv
        ├── series_2.csv
        ├── series_3.csv
        ├── series_4.csv
        └── series_5.csv
```