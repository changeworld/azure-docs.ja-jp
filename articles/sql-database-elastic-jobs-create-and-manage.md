<properties 
	pageTitle="弾力性データベースのジョブの作成と管理" 
	description="弾力性データベースのジョブの作成と管理について説明します。" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh"/>

# 弾力性データベースのジョブの作成と管理

**弾力性データベース プール**は、大量のデータベースをデプロイするために予測可能なモデルを提供します。設定されたコストで、各データベースに最小のデータ スループット ユニット (DTUs) を設定します。これらのデータベースの一般的なオブジェクトの管理は、**弾力性データベース ジョブ**を使用して簡単に実現できます。このサービスでは、単一の操作でプール内のすべてのデータベースに対して T-SQL スクリプトを実行することができます。たとえば、各データベースにポリシーを設定し、適切な資格情報を持つユーザーのみに機密データを表示することができます。

## 前提条件

* Azure サブスクリプション。無料評価版については、「[1 か月間の無料評価版](http://azure.microsoft.com/pricing/free-trial/)」をご覧ください。
* 弾力性データベース プール。「[弾力性データベース プール](sql-database-elastic-pool.md)」をご覧ください。
* 弾力性データベース ジョブ サービス コンポーネントのインストール。「[弾力性データベース ジョブ サービスのインストール](vsql-database-elastic-jobs-service-installation.md)」をご覧ください。

## ジョブの作成

1. [弾力性データベース ジョブ プール] ブレードで、**[ジョブの作成]** をクリックします。
2. (インストール時に作成した) データベース管理者の名前とパスワード を入力します。
2. **[ジョブの作成]** ブレードで、ジョブの名前を入力します。
3. T-SQL スクリプトを貼り付けるか、入力します。
4. **[保存]** をクリックし、次に **[実行]** をクリックします。

	![ジョブに名前を付け、コードに入力するか、貼り付け、[実行] をクリックします。][1]

## べき等ジョブを実行します

一連のデータベースに対してスクリプトを実行する場合、スクリプトがべき等であることを確認する必要があります。つまり、スクリプトは、前に不完全な状態で失敗した場合でも、複数回実行される必要があります。たとえば、スクリプトが失敗すると、ジョブは成功するまで自動的に再試行されます (制限内、再試行ロジックが最終的に再試行を停止するまで)。これを行うには、"IF EXISTS" 句を使用して、新しいオブジェクトを作成する前に、検出されたすべてのインスタンスを削除します。例を次に示します。

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID 
    ON Purchasing.ProductVendor (VendorID);

代わりに、新しいインスタンスを作成する前に、"IF NOT EXISTS" 句を使用します。

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable') 
	BEGIN 
	 CREATE TABLE TestTable( 
	  TestTableId INT PRIMARY KEY IDENTITY, 
	  InsertionTime DATETIME2 
	 ); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime()); 
	GO 

このスクリプトは、以前に作成されたテーブルを更新します。

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation') 
	BEGIN 
	
	ALTER TABLE TestTable 
	
	ADD AdditionalInformation NVARCHAR(400); 
	END 
	GO 
	
	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test'); 
	GO 


## ジョブの状態を確認する

ジョブが開始されると、進行状況を確認できます。

1. 弾力性データベース ページ プールで、**[ジョブの管理]** をクリックします。

	![[ジョブの管理] をクリックします。][2]

2. ジョブの名前 (a) をクリックします。**[状態]** は、「完了」か「失敗」です。 ジョブの詳細は、作成と実行の日時とともに (b) に表示されます。下記のリスト (c) は、各データベースに対するスクリプトの進行状況を日時の詳細とともに表示します。

	![完成したジョブの確認][3]


## 失敗したジョブの確認

ジョブが失敗した場合、その実行ログを確認できます。失敗したジョブの名前をクリックして詳細を表示します。

![失敗したジョブを確認する][4]


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/provide-creds.png

<!---HONumber=58-->