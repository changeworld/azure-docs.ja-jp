---
title: チュートリアル:SQL プールを使用したデータ分析の概要
description: このチュートリアルでは、NYC タクシーのサンプル データを使用して、SQL プールの分析機能を探索します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b1060bcc8603cb7f7395a50056424b3d6c0ebe5a
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015502"
---
# <a name="analyze-data-with-sql-pools"></a>SQL プールを使用したデータ分析

Azure Synapse Analytics には、SQL プールを使用してデータを分析する機能が用意されています。 このチュートリアルでは、NYC タクシーのサンプル データを使用して、SQL プールの分析機能を探索します。

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>NYC タクシー データを SQLDB1 に読み込む

1. Synapse Studio で **[開発]** ハブに移動し、新しい SQL スクリプトを作成します
1. 次のコードを入力します。
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. このスクリプトの実行には約 1 分かかります。 200 万行の NYC タクシー データを **dbo.Trip** というテーブルに読み込みます

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[SQLDB1]**  >  **[テーブル]** に移動します。 複数のテーブルが読み込まれていることがわかります。
1. **dbo.Trip** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\)**  >  **[Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
1. 新しい SQL スクリプトが作成されて実行されるまで待ちます。
1. SQL スクリプトの上部にある **[接続先]** が自動的に **SQLDB1** という SQL プールに注目します。
1. SQL スクリプトのテキストをこのコードで置き換えて実行します。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更して、結果を折れ線グラフとして視覚的に表示します。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Spark を使用して分析する](get-started-analyze-spark.md)

