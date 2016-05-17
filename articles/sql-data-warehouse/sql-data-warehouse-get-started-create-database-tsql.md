<properties
   pageTitle="TSQL で SQL Data Warehouse を作成する |Microsoft Azure"
   description="TSQL で Azure SQL Data Warehouse を作成する方法を説明します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Transact-SQL (TSQL) を使用して SQL Data Warehouse データベースを作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

この記事では、Transact-SQL (TSQL) を使用して SQL Data Warehouse データベースを作成する方法を示します。

## 開始する前に

この記事の手順を完了するには、次のものが必要です。

- Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合、このページの上部の**無料試用版**をクリックしてからこの記事に戻り、最後まで完了してください。
- V12 論理 SQL サーバー。SQL Data Warehouse を作成するには、V12 SQL サーバーが必要です。V12 論理 SQL サーバーを持っていない場合は、[Azure ポータルから SQL Data Warehouse を作成する方法][]に関する記事の**サーバーの構成と作成**の部分を参照してください。
- 見ることができます。Visual Studio の無償版については、[Visual Studio のダウンロード][]に関するページを参照してください。


> [AZURE.NOTE] 新しい SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。料金の詳細については、「[SQL Data Warehouse の価格][]」を参照してください。

## Visual Studio でデータベースを作成する

Visual Studio に慣れていない場合は、[Visual Studio での SQL Data Warehouse への接続][]に関する記事を参照してください。開始するには、Visual Studio で SQL Server オブジェクト エクスプローラーを開き、SQL Data Warehouse データベースをホストするサーバーに接続します。接続したら、**master** データベースに対して次の SQL コマンドを実行することで、SQL Data Warehouse を作成できます。このコマンドは、サービス目標を DW400 にしてデータベース MySqlDwDb を作成し、データベースのサイズを最大で 10 TB まで拡張できるようにします。

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## sqlcmd でデータベースを作成する

コマンド プロンプトで以下のように実行することによって、sqlcmd で同じコマンドを実行することもできます。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

**MAXSIZE** と **SERVICE\_OBJECTIVE** パラメーターは、ディスクでデータベースが使用できる最大領域と、Data Warehouse インスタンスに割り当てられるコンピューティング リソースを指定します。サービス目標は、本質的には、DWU のサイズに比例して拡張される CPU とメモリの割り当てです。

MAXSIZE には、250 GB から 60 TB までの値を指定できます。サービス目標には、DW100 から DW2000 までの値を指定できます。MAXSIZE および SERVICE\_OBJECTIVE のすべての有効な値の完全な一覧については、[CREATE DATABASE][] に関する MSDN ドキュメントを参照してください。MAXSIZE と SERVICE\_OBJECTIVE のいずれも、[ALTER DATABASE][] T-SQL コマンドで変更することもできます。SERVICE\_OBJECTIVE を変更すると、サービスが再起動され、処理中のすべてのクエリが取り消されるため、注意する必要があります。MAXSIZE の変更は、単なるメタデータ操作であるため、この心配はありません。

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を確認したりできます。

<!--Article references-->
[Azure ポータルから SQL Data Warehouse を作成する方法]: sql-data-warehouse-get-started-provision.md
[Visual Studio での SQL Data Warehouse への接続]: sql-data-warehouse-get-started-connect.md
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[読み込み]: sql-data-warehouse-overview-load.md
[サンプル データを読み込んだり]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Visual Studio のダウンロード]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0511_2016-->