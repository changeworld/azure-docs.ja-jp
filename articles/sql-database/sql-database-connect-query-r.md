---
title: Machine Learning Services で R を使用してデータベースを照会する (プレビュー)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: このトピックでは、Azure SQL Database Machine Learning service で R スクリプトを使用して、Azure SQL データベースに接続し、Transact-SQL ステートメントを使用してデータベースに照会する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768512"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>クイック スタート:Machine Learning service で R を使用して Azure SQL データベースにクエリを実行する (プレビュー)

このクイックスタートでは、Machine Learning Services で R を使用して Azure SQL データベースに接続し、T-SQL ステートメントを使用してデータを照会します。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL データベース](sql-database-single-database-get-started.md)
- R が有効な [Machine Learning Services](sql-database-machine-learning-services-overview.md)。 [プレビューにサインアップしてください](sql-database-machine-learning-services-overview.md#signup)。
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> この記事のスクリプトは、**Adventure Works** データベースを使用するように記述されています。

> [!NOTE]
> パブリック プレビュー期間中は、Microsoft がお客様のオンボードを行い、既存のデータベースまたは新しいデータベースに対して機械学習を有効にします。ただし、マネージド インスタンスのデプロイ オプションは現在サポートされていません。

Machine Learning Services と R は、データベース内の R スクリプトを実行するために使用される、Azure SQL Database の機能です。 詳細については、[R プロジェクト](https://www.r-project.org/)に関するページを参照してください。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する

1. **SQL Server Management Studio** を開き、SQL データベースに接続します。

   接続についてご不明な点がある場合は、「[クイック スタート: SQL Server Management Studio を使用して Azure SQL データベースに接続しクエリを実行する](sql-database-connect-query-ssms.md)」をご覧ください。

1. R スクリプト全体を [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャに渡します。

   このスクリプトは、`@script` 引数を通して渡されます。 `@script`引数内のすべては、有効な R コードである必要があります。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > なんらかのエラーが発生した場合は、お使いの SQL データベースで Machine Learning Services のパブリック プレビューと R が有効になっていない可能性があります。 前述の「[前提条件](#prerequisites)」を参照してください。

## <a name="run-the-code"></a>コードの実行

1. [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャを実行します。

1. 上位 20 カテゴリ/製品の行が **[メッセージ]** ウィンドウに返されることを確認します。

## <a name="next-steps"></a>次のステップ

- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
- [Azure SQL Database の Machine Learning Services と R (プレビュー)](sql-database-machine-learning-services-overview.md)
- [Azure SQL Database Machine Learning Services (プレビュー) で簡単な R スクリプトを作成して実行する](sql-database-quickstart-r-create-script.md)
- [Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する](sql-database-machine-learning-services-functions.md)
