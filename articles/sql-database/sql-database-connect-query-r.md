---
title: R を使用して Azure SQL データベースに照会する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: このトピックでは、R スクリプトを使用して、Azure SQL データベースに接続し Transact-SQL ステートメントを使用してデータベースに照会する方法について説明します。
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
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416368"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>クイック スタート:R を使用して Azure SQL データベースに照会する (プレビュー)

 このクイック スタートでは、Machine Learning Services で [R](https://www.r-project.org/) を使用して Azure SQL データベースに接続し、Transact-SQL ステートメントを使用してデータを照会する方法について説明します。 Machine Learning Services は、データベース内の R スクリプトを実行するために使用される、Azure SQL Database の機能です。 詳細については、「[Azure SQL Database Machine Learning Services と R (プレビュー)](sql-database-machine-learning-services-overview.md)」を参照してください。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下のものが必要です。

- Azure SQL データベース。 以下のいずれかのクイック スタートを使用して、Azure SQL Database でデータベースを作成し、構成できます。

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || 単一データベース |
  |:--- |:--- |
  | Create| [ポータル](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | 構成 | [サーバーレベルの IP ファイアウォール規則](sql-database-server-level-firewall-rule.md) |
  | データを読み込む | クイック スタートごとに読み込まれる Adventure Works |
  |||

  > [!NOTE]
  > Azure SQL Database Machine Learning Services と R のプレビュー期間中は、マネージド インスタンスのデプロイ オプションがサポートされません。

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning Services (R を含む) が有効になっていること。 パブリック プレビュー期間中は、Microsoft がお客様のオンボードを行い、既存のデータベースまたは新しいデータベースに対して機械学習を有効にします。 「[Sign up for the preview (プレビューにサインアップする)](sql-database-machine-learning-services-overview.md#signup)」の手順に従ってください。

- 最新の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。 他のデータベース管理またはクエリ ツールを使用して R スクリプトを実行することはできますが、このクイック スタートでは、SSMS を使用します。

## <a name="get-sql-server-connection-information"></a>SQL サーバーの接続情報を取得する

Azure SQL データベースに接続するために必要な接続情報を取得します。 後の手順で、完全修飾サーバー名またはホスト名、データベース名、およびログイン情報が必要になります。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

2. **[SQL データベース]** または **[SQL マネージド インスタンス]** ページに移動します。

3. **[概要]** ページで、単一データベースの場合は **[サーバー名]** の横の完全修飾サーバー名を確認し、マネージド インスタンスの場合は **[ホスト]** の横の完全修飾サーバー名を確認します。 サーバー名またはホスト名をコピーするには、名前をポイントして **[コピー]** アイコンを選択します。

## <a name="create-code-to-query-your-sql-database"></a>コードを作成して、SQL データベースのクエリを実行する

1. **SQL Server Management Studio** を開き、SQL データベースに接続します。

   接続についてご不明な点がある場合は、「[クイック スタート: SQL Server Management Studio を使用して Azure SQL データベースに接続しクエリを実行する](sql-database-connect-query-ssms.md)」をご覧ください。

1. R スクリプト全体を [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャに渡します。

   スクリプトは `@script` 引数で渡します。 `@script` 引数の内容は有効な R コードである必要があります。
   
   >[!IMPORTANT]
   >この例のコードでは、サンプル データ AdventureWorksLT を使用します。これは、データベースの作成時にソースとして選択できます。 データベースに別のデータがある場合は、SELECT クエリで独自のデータベースからのテーブルを使用します。 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > なんらかのエラーが発生した場合は、お使いの SQL データベースで Machine Learning Services のパブリック プレビューと R が有効になっていない可能性があります。 前記の「[前提条件](#prerequisites)」をご覧ください。

## <a name="run-the-code"></a>コードの実行

1. [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) ストアド プロシージャを実行します。

1. 上位 20 カテゴリ/製品の行が **[メッセージ]** ウィンドウに返されることを確認します。

## <a name="next-steps"></a>次の手順

- [最初の Azure SQL データベースの設計](sql-database-design-first-database.md)
- [Azure SQL Database の Machine Learning Services と R (プレビュー)](sql-database-machine-learning-services-overview.md)
- [Azure SQL Database Machine Learning Services (プレビュー) で簡単な R スクリプトを作成して実行する](sql-database-quickstart-r-create-script.md)
- [Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する](sql-database-machine-learning-services-functions.md)
