---
title: "Azure SQL Data Warehouse のエラスティック クエリのチュートリアル | Microsoft Docs"
description: "Azure SQL Data Warehouse のエラスティック クエリの使用方法を説明します。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67g
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 11/03/2017
ms.author: elbutter
ms.openlocfilehash: a13b81213b7a47cb7209bc914f514fa10aede5c4
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2017
---
# <a name="configure-elastic-query-with-sql-data-warehouse"></a>SQL Data Warehouse のエラスティック クエリの構成

このチュートリアルでは、エラスティック クエリを使用して SQL Database から SQL Data Warehouse にクエリを送信する方法を学習します。 エラスティック クエリは、Azure SQL 製品間に存在する機能です。 概念としてのエラスティック クエリの詳細については、「[**SQL Data Warehouse のエラスティック クエリの使用方法**][How to use Elastic Query with SQL Data Warehouse]」をご覧ください。

## <a name="prerequisites-for-the-tutorial"></a>このチュートリアルの前提条件

このチュートリアルを開始する前に、次の前提条件を満たしておく必要があります。

1. SQL Server Management Studio (SSMS) をインストール済みであること。
2. Azure SQL サーバーと、そのサーバー内にデータベースおよびデータ ウェアハウスを作成済みであること。
3. Azure SQL Server にアクセスするためのファイアウォール規則を設定済みであること。

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>SQL Data Warehouse インスタンスと SQL Database インスタンス間の接続の設定 

1. SSMS または別のクエリ クライアントを使用して、論理サーバー上の **master** データベースに対する新しいクエリを開きます。

2. SQL Database からデータ ウェアハウスへの接続を表すログインとユーザーを作成します。

  ```sql
  CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
  ```

3. SSMS または別のクエリ クライアントを使用して、論理サーバー上の**SQL Data Warehouse インスタンス**に対する新しいクエリを開きます。

4. 手順 2 で作成したログインを使用して、データ ウェアハウス インスタンスのユーザーを作成します。

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. 手順 4 で作成したユーザーに、SQL Database で実行する必要がある操作の権限を与えます。 次の例では、特定のスキーマに対する SELECT 権限のみを与えており、SQL Database から特定のドメインへのクエリを制限する方法を示しています。 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. SSMS または別のクエリ クライアントを使用して、論理サーバー上の**SQL Database インスタンス**に対する新しいクエリを開きます。

7. マスター キーがまだない場合は、作成します。 

   ```sql
   CREATE MASTER KEY; 
   ```

8. 手順 2 で作成した資格情報を使用して、データベース スコープ資格情報を作成します。

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. データ ウェアハウス インスタンスをポイントする外部データ ソースを作成します。

  ```sql
  CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
      (TYPE = RDBMS, 
      LOCATION = '<SERVER NAME>.database.windows.net', 
      DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
      CREDENTIAL = SalesDBElasticCredential, 
  ) ;
  ```

10. これで、この外部データ ソースを参照する外部テーブルを作成できます。 そのテーブルを使用するクエリは、データ ウェアハウス インスタンスに送信されて処理され、データベース インスタンスに返送されます。


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>SQL Database から SQL Data Warehouse へのエラスティック クエリ

以下の手順では、データ ウェアハウス インスタンスにいくつかの値を含むテーブルを作成します。 次に、データベース インスタンスからデータ ウェアハウス インスタンスのクエリを実行するための外部テーブルの設定方法を示します。

1. SSMS または別のクエリ クライアントを使用して、論理サーバー上の **SQL Data Warehouse** に対する新しいクエリを開きます。

2. 次のクエリを送信して、データ ウェアハウス インスタンスに **OrdersInformation** テーブルを作成します。

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. SSMS または別のクエリ クライアントを使用して、論理サーバー上の**SQL Database** に対する新しいクエリを開きます。

4. 次のクエリを送信して、データ ウェアハウス インスタンス内の **OrdersInformation** テーブルをポイントする外部テーブル定義を作成します。

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. **SQL データベース インスタンス**に外部テーブル定義が作成されたことを確認します。

   ![エラスティック クエリの外部テーブル定義](./media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. 次のクエリを送信します。これにより、データ ウェアハウス インスタンスに対してクエリが実行されます。 手順 2 で挿入した 5 つの値が返されるはずです。 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> 値が数個であるにもかかわらず、このクエリから結果が返るまでにはかなりの時間がかかります。 データ ウェアハウスにエラスティック クエリを使用するときは、クエリ処理とネットワーク経由の移動のオーバーヘッド コストを考慮する必要があります。 エラスティック クエリのリモート実行は、待ち時間ではなくコンピューティング能力が優先される場合に使用してください。

これで、ごく基本的なエラスティック クエリの設定が完了しました。 




<!--Image references-->

<!--Article references-->

[How to use Elastic Query with SQL Data Warehouse]: ./how-to-use-elastic-query-with-sql-data-warehouse.md

<!--MSDN references-->

<!--Other Web references-->