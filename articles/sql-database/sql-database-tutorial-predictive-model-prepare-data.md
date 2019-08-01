---
title: チュートリアル:R で予測モデルをトレーニングするためのデータを準備する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアル シリーズのパート 1 では、R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルをトレーニングするための Azure SQL データベースのデータを準備します。
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
ms.openlocfilehash: c1271d5b63fa796fe44b7a40c364953464a87539
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596671"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルをトレーニングするためのデータを準備する

この 3 部構成のチュートリアル シリーズのパート 1 では、R を使用して、Azure SQL データベースのデータをインポートして準備します。このシリーズでは、その後、このデータを利用し、R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測機械学習モデルをトレーニングしてデプロイします。

このチュートリアル シリーズでは、あなたはスキー レンタル業を自ら営んでおり、今後のレンタル数を予測する必要があるとします。 この情報は、在庫、スタッフ、設備の準備に役立ちます。

このシリーズのパート 1 と 2 では、使用するデータを準備し、機械学習モデルをトレーニングするために、RStudio 内でいくつかの R スクリプトを開発します。 その後、パート 3 では、ストアド プロシージャを使用して SQL データベース内でそれらの R スクリプトを実行します。

この記事では、以下の方法について説明します。

> [!div class="checklist"]
> * R を使用して Azure SQL データベースにサンプル データベースをインポートする
> * Azure SQL データベースから R データ フレームにデータを読み込む
> * R でカテゴリとしていくつかの列を識別してデータを準備する

[パート 2](sql-database-tutorial-predictive-model-build-compare.md) では、R で複数の機械学習モデルを作成してトレーニングしてから、最も正確なものを選ぶ方法を学習します。

[パート 3](sql-database-tutorial-predictive-model-deploy.md) では、モデルをデータベースに格納した後、パート 1 と 2 で開発した R スクリプトからストアド プロシージャを作成する方法を学習します。 このストアド プロシージャは、SQL データベース内で実行され、新しいデータに基づいて予測を行います。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

* Machine Learning Services が有効な Azure SQL Database サーバー - パブリック プレビュー期間中は、Microsoft でお客様のオンボードを行い、既存のデータベースまたは新しいデータベースに対して機械学習を有効にします。 「[Sign up for the preview (プレビューにサインアップする)](sql-database-machine-learning-services-overview.md#signup)」の手順に従ってください。

* RevoScaleR パッケージ - このパッケージをローカルでインストールするためのオプションについては、[RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) に関するページを参照してください。

* R IDE - このチュートリアルでは [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) を使用します。

* SQL クエリ ツール - このチュートリアルでは、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) または [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用していることが前提となります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="import-the-sample-database"></a>サンプル データベースをインポートする

このチュートリアルで使用されるサンプル データセットは、 **.bacpac** データベース バックアップ ファイルに保存されています。このファイルをダウンロードして使用します。

1. ファイル [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac) をダウンロードします。

1. これらの詳細を使用して、[Azure SQL データベースを作成するための BACPAC ファイルのインポート](https://docs.microsoft.com/azure/sql-database/sql-database-import)に関するページの指示に従います。

   * ダウンロードした **TutorialDB.bacpac** ファイルからインポートする
   * パブリック プレビュー期間中は、新しいデータベースに対して **Gen5/vCore** 構成を選択する
   * 新しいデータベースに "TutorialDB" という名前を付ける

## <a name="load-the-data-into-a-data-frame"></a>データ フレームにデータを読み込む

R でデータを使用するには、Azure SQL データベースからデータ フレーム (`rentaldata`) にデータを読み込みます。

RStudio で新しい RScript ファイルを作成し、以下のスクリプトを実行します。 **Server**、**UID**、および **PWD** は、独自の接続情報に置き換えてください。

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

次のような結果が表示されるはずです。

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>データを準備する

このサンプル データベースでは、ほとんどの準備が既に行われていますが、ここでもう 1 つ準備を行います。
以下の R スクリプトを使用して、データ型を "*因子*" に変更し、"*カテゴリ*" として 3 つの列を識別します。

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

次のような結果が表示されるはずです。

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

これで、トレーニングのためのデータの準備ができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルを続行しない場合は、自分の Azure SQL Database サーバーから TutorialDB データベースを削除します。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**TutorialDB**」と入力し、お使いのサブスクリプションを選択します。
1. 自分の TutorialDB データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルのパート 1 では、これらの手順を完了しました。

* R を使用して Azure SQL データベースにサンプル データベースをインポートする
* Azure SQL データベースから R データ フレームにデータを読み込む
* R でカテゴリとしていくつかの列を識別してデータを準備する

TutorialDB データベースからデータを使用する機械学習モデルを作成するには、このチュートリアル シリーズのパート 2 に従います。

> [!div class="nextstepaction"]
> [チュートリアル:R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルを作成する](sql-database-tutorial-predictive-model-build-compare.md)
