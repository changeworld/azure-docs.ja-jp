---
title: チュートリアル:R で予測モデルをデプロイする
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアルのパート 3 では、R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルをデプロイします。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/26/2019
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596776"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルをデプロイする

この 3 部構成のチュートリアルのパート 3 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して、R で開発した予測モデルを SQL データベースにデプロイします。

埋め込みの R スクリプトで、モデルを使用して予測するストアド プロシージャを作成します。 モデルは Azure SQL データベースで実行されるため、データベースに格納されているデータに対して簡単にトレーニングすることができます。

この記事では、パート 1 と 2 で開発した R スクリプトを使用して、次の方法を学習します。

> [!div class="checklist"]
> * 機械学習モデルを生成するストアド プロシージャを作成する
> * データベース テーブルにモデルを格納する
> * モデルを使用して予測するストアド プロシージャを作成する
> * 新しいデータでモデルを実行する

[パート 1](sql-database-tutorial-predictive-model-prepare-data.md) では、サンプル データベースをインポートし、R での予測モデルのトレーニングに使用されるデータを準備する方法を学習しました。

[パート 2](sql-database-tutorial-predictive-model-build-compare.md) では、R で複数の機械学習モデルを作成してトレーニングしてから、最も正確なものを選ぶ方法を学習しました。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* このチュートリアル シリーズのパート 3 では、[**パート 1**](sql-database-tutorial-predictive-model-prepare-data.md) と[**パート 2**](sql-database-tutorial-predictive-model-build-compare.md) が完了していることを前提とします。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>モデルを生成するストアド プロシージャを作成する

このチュートリアル シリーズのパート 2 では、デシジョン ツリー (dtree) モデルが最も正確であると判断しました。 ここでは、開発した R スクリプトを使用して、RevoScaleR パッケージの rxDTree を利用し、dtree モデルをトレーニングして生成するストアド プロシージャ (`generate_rental_rx_model`) を作成します。

Azure Data Studio または SSMS で以下のコマンドを実行します。

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>データベース テーブルにモデルを格納する

TutorialDB データベースにテーブルを作成してから、そのテーブルにモデルを保存します。

1. モデルを格納するためのテーブル (`rental_rx_models`) を作成します。

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. バイナリ オブジェクトとしてテーブルにモデルを保存します。モデル名は "rxDTree" です。

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>予測するストアド プロシージャを作成する

トレーニング済みのモデルと一連の新しいデータを使用して予測するストアド プロシージャ (`predict_rentalcount_new`) を作成します。

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>新しいデータでモデルを実行する

ここで、ストアド プロシージャ `predict_rentalcount_new` を使用して、新しいデータからレンタル数を予測できます。

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

次のような結果が表示されるはずです。

```results
RentalCount_Predicted
332.571428571429
```

Azure SQL データベースでモデルを正常に作成し、トレーニングしてデプロイしました。 その後、ストアド プロシージャでそのモデルを使用して、新しいデータに基づいて値を予測しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

TutorialDB データベースの使用が終わったら、それを Azure SQL Database サーバーから削除します。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**TutorialDB**」と入力し、お使いのサブスクリプションを選択します。
1. 自分の TutorialDB データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアル シリーズのパート 3 では、これらの手順を完了しました。

* 機械学習モデルを生成するストアド プロシージャを作成する
* データベース テーブルにモデルを格納する
* モデルを使用して予測するストアド プロシージャを作成する
* 新しいデータでモデルを実行する

Azure SQL Database Machine Learning Services (プレビュー) での R の使用について、さらに学習する場合は、以下を参照してください。

* [Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する](sql-database-machine-learning-services-functions.md)
* [Azure SQL Database Machine Learning Services (プレビュー) での R および SQL データの処理](sql-database-machine-learning-services-data-issues.md)
* [Azure SQL Database Machine Learning Services (プレビュー) に R パッケージを追加する](sql-database-machine-learning-services-add-r-packages.md)
