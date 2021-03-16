---
title: チュートリアル:専用 SQL プールを使用したデータ分析の概要
description: このチュートリアルでは、NYC タクシーのサンプル データを使用して、SQL プールの分析機能を探索します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 54b650d598cf19e061465b3a4fa18d50808e7f29
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426163"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>専用の SQL プールを使用してデータを分析する

Azure Synapse Analytics には、専用 SQL プールを使用してデータを分析する機能が用意されています。 このチュートリアルでは、NYC タクシーのデータを使用して、専用 SQL プールの機能を探索します。

## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>NYC タクシー データを SQLPOOL1 に読み込む

1. Synapse Studio で **[開発]** ハブに移動し、新しいリソースを追加するための **+** ボタンをクリックして、新しい SQL スクリプトを作成します。
1. スクリプトの上にある [接続先] ドロップダウン リストで、"SQLPOOL1" プール (このチュートリアルの[ステップ 1](./get-started-create-workspace.md) で作成したプール) を選択します。
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
1. [実行] ボタンをクリックして、スクリプトを実行します。
1. このスクリプトは 60 秒以内に終了します。 200 万行の NYC タクシー データを **dbo.Trip** というテーブルに読み込みます。

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>専用 SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **SQLPOOL1** という名前のデータベースが表示されます。 表示されない場合は、 **[最新の情報に更新]** をクリックします。
1. **[SQLPOOL1]**  >  **[テーブル]** の順に移動します。 
3. **dbo.Trip** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\)**  >  **[Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
4. 新しい SQL スクリプトが作成されて実行されるまで待ちます。
5. SQL スクリプトの上部の **Connect to** が自動的に **SQLPOOL1** という SQL プールに設定されることに注意してください。
6. SQL スクリプトのテキストをこのコードで置き換えて実行します。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更して、結果を折れ線グラフとして視覚的に表示します。
    
    > [!NOTE]
    > ワークスペース対応の専用 SQL プール (以前の SQL DW) は、データ ハブ内のツール ヒントを介して識別できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Spark を使用して分析する](get-started-analyze-spark.md)
