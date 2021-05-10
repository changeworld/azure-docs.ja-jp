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
ms.date: 03/24/2021
ms.openlocfilehash: 0def1f957842417c3936e3f1c7bb5bc023109818
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536323"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>専用の SQL プールを使用してデータを分析する

このチュートリアルでは、NYC タクシーのデータを使用して、専用 SQL プールの機能を探索します。

## <a name="create-a-dedicated-sql-pool"></a>専用 SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択します。
1. **[SQL プール名]** で **[SQLPOOL1]** を選択します。
1. **[パフォーマンス レベル]** で **[DW100C]** を選択します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 専用 SQL プールの準備は数分で完了します。 

専用 SQL プールは、**SQLPOOL1** とも呼ばれる SQL データベースに関連付けられます。
1. **[データ]**  >  **[ワークスペース]** の順に移動します。
1. **SQLPOOL1** という名前のデータベースが表示されます。 表示されない場合は、 **[最新の情報に更新]** をクリックします。

専用 SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

> [!NOTE] 
> ワークスペースに新しい専用 SQL プール (以前の SQL DW) を作成すると、専用の SQL プールのプロビジョニング ページが開きます。 プロビジョニングは、論理 SQL サーバー上で行われます。
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>NYC タクシー データを SQLPOOL1 に読み込む

1. Synapse Studio で **[開発]** ハブに移動し、新しいリソースを追加するための **+** ボタンをクリックして、新しい SQL スクリプトを作成します。
1. スクリプトの上にある [接続先] ドロップダウン リストで、"SQLPOOL1" プール (このチュートリアルの[ステップ 1](./get-started-create-workspace.md) で作成したプール) を選択します。
1. 次のコードを入力します。
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. [実行] ボタンをクリックして、スクリプトを実行します。
1. このスクリプトは 60 秒以内に終了します。 200 万行の NYC タクシー データを **dbo.Trip** というテーブルに読み込みます。

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>専用 SQL プール内の NYC タクシーのデータを探索する

1. Synapse Studio で、 **[データ]** ハブに移動します。
1. **[SQLPOOL1]**  >  **[テーブル]** の順に移動します。 
3. **dbo.NYCTaxiTripSmall** テーブルを右クリックし、 **[New SQL Script]\(新しい SQL スクリプト\)**  >  **[Select TOP 100 Rows]\(上位 100 行の選択\)** を選択します。
4. 新しい SQL スクリプトが作成されて実行されるまで待ちます。
5. SQL スクリプトの上部の **Connect to** が自動的に **SQLPOOL1** という SQL プールに設定されることに注意してください。
6. SQL スクリプトのテキストをこのコードで置き換えて実行します。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    このクエリは、合計乗車距離と平均乗車距離が乗客数とどのように関係しているかを示します。
1. SQL スクリプトの結果ウィンドウで、 **[ビュー]** を **[グラフ]** に変更して、結果を折れ線グラフとして視覚的に表示します。
    
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Storage アカウントでデータを分析する](get-started-analyze-storage.md)
