---
title: データ アナリスト チュートリアル - SQL オンデマンド (プレビュー) を使用して Azure Synapse Studio (プレビュー) で Azure Open Datasets を分析する
description: このチュートリアルでは、SQL オンデマンド (プレビュー) を使用してさまざまな Azure Open Datasets を組み合わせた探索的データ分析を簡単に実行し、Azure Synapse Studio で結果を視覚化する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b2fe4dea27564b96c5ef1734dc16ca4525011d17
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745641"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>SQL オンデマンド (プレビュー) を使用した Azure Open Datasets の分析と Azure Synapse Studio (プレビュー) での結果の視覚化

このチュートリアルでは、SQL オンデマンドを使用してさまざまな Azure Open Datasets を組み合わせることで探索的データ分析を実行し、Azure Synapse Studio で結果を視覚化する方法について説明します。

特に、[ニューヨーク市 (NYC) のタクシーのデータセット](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)を分析します。このデータセットには、乗車と降車の日時、乗車と降車の場所、移動距離、料金明細、料金の種類、支払いの種類、運転手が報告する乗車人数が含まれています。

分析の主眼は、時系列でのタクシー乗車数の変化の傾向を発見することです。 タクシー乗車の外れ値を把握するために、他の 2 つの Azure Open Datasets ([休日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)および[気象データ](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)) を分析します。

## <a name="create-data-source"></a>データ ソースの作成

データ ソース オブジェクトは、データを分析する必要がある Azure ストレージ アカウントを参照するために使用されます。 パブリックに使用できるストレージには、ストレージにアクセスするための資格情報は不要です。

```sql
-- There is no credential in data surce. We are using public storage account which doesn't need a credential.
CREATE EXTERNAL DATA SOURCE AzureOpenData
WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/')
```

## <a name="automatic-schema-inference"></a>自動スキーマ推論

データは Parquet ファイル形式で格納されるため、自動スキーマ推論を使用できます。そのため、ファイル内のすべての列のデータ型を一覧表示する必要なく、データにクエリを簡単に実行できます。 さらに、仮想列のメカニズムと filepath 関数を利用して、ファイルの特定のサブセットを除外することもできます。

まず、NYC のタクシー データについて理解するために、次のクエリを実行します。

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
```

NYC のタクシー データの結果のスニペットを次に示します。

![結果のスニペット](./media/tutorial-data-analyst/1.png)

同様に、次のクエリを使用して休日のデータセットにクエリを実行できます。

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'holidaydatacontainer/Processed/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [holidays]
```

休日のデータセットの結果のスニペットを次に示します。

![結果のスニペット 2](./media/tutorial-data-analyst/2.png)

最後に、次のクエリを使用して、気象データセットにもクエリを実行できます。

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
```

気象データセットの結果のスニペットを次に示します。

![結果のスニペット 3](./media/tutorial-data-analyst/3.png)

個々の列の意味の詳細については、[NYC タクシー](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)、[休日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)、および[気象データ](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)のデータセットに関する説明を参照してください。

## <a name="time-series-seasonality-and-outlier-analysis"></a>時系列、季節性、および外れ値の分析

次のクエリを使用して、毎年のタクシー乗車数を簡単にまとめることができます。

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

毎年のタクシー乗車数の結果のスニペットを次に示します。

![結果のスニペット 4](./media/tutorial-data-analyst/4.png)

データは Synapse Studio でテーブル ビューからグラフ ビューに切り替えることによって視覚化できます。 さまざまな種類のグラフ (面グラフ、横棒グラフ、縦棒グラフ、折れ線グラフ、円グラフ、および散布図) から選択できます。 この例で、カテゴリ列を "current_year" に設定した縦棒グラフをプロットしてみましょう。

![結果の視覚化 5](./media/tutorial-data-analyst/5.png)

この視覚化から、乗車数が年々減少している傾向がはっきりと見て取れます。これはおそらく、近年のライドシェア企業の人気の高まりが原因であると思われます。

> [!NOTE]
> このチュートリアルの執筆時点では、2019 年のデータが不完全であるため、その年の乗車数が著しく低下しています。

次に、単年度の分析に重点を置いてみましょう。たとえば 2016 年に注目します。 次のクエリでは、その年の毎日の乗車数が返されます。

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

このクエリの結果のスニペットを次に示します。

![結果のスニペット 6](./media/tutorial-data-analyst/6.png)

ここでも、カテゴリ列を "current_day" にして、凡例 (系列) 列を "rides_per_day" にした縦棒グラフをプロットすることで、データを簡単に視覚化できます。

![結果の視覚化 7](./media/tutorial-data-analyst/7.png)

プロットから、土曜日をピークとする週単位のパターンがあることが確認できます。 夏期は休暇期間になるため、毎月のタクシー乗車数が減少します。 しかし、時期および理由の観点から明確なパターンがないのにタクシー乗車数が大幅に減少することもあります。

次に、NYC タクシー乗車数を休日のデータセットと組み合わせることによって、これらの減少が休日と潜在的に関連しているかどうかを見てみましょう。

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'holidaydatacontainer/Processed/*.parquet',
            DATA_SOURCE = 'AzureOpenData',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![結果の視覚化 8](./media/tutorial-data-analyst/8.png)

今度は、休日のタクシー乗車数を強調する必要があります。 そのために、カテゴリ列に "none"、凡例 (系列) 列に "rides_per_day"、および "holiday" を選択します。

![結果の視覚化 9](./media/tutorial-data-analyst/9.png)

このプロットから、休日はタクシー乗車数が少なくなることがはっきりとわかります。 しかし、1 月 23 日の予期しない大幅な減少については、まだ説明がついていません。 では、気象データセットにクエリを実行して、その日のニューヨーク市の天気を確認してみましょう。

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        DATA_SOURCE = 'AzureOpenData',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![結果の視覚化 10](./media/tutorial-data-analyst/10.png)

このクエリの結果から、タクシー乗車数の減少は次のことが原因であることが示されます。

- ニューヨーク市に吹雪が発生して大雪だった (約 30 cm)
- 寒かった (気温が摂氏 0 度を下回った)
- 強風だった (約 10m/s)

このチュートリアルでは、データ アナリストが探索的データ分析を迅速に実行し、SQL オンデマンドを使用してさまざまなデータセットを簡単に結合し、Azure Synapse Studio を使用して結果を視覚化する方法について説明しました。

## <a name="next-steps"></a>次のステップ

[SQL オンデマンドの Power BI Desktop への接続とレポートの作成](tutorial-connect-power-bi-desktop.md)に関する記事を参照して、SQL オンデマンドを Power BI Desktop に接続してレポートを作成する方法を確認します。
 
