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
   ms.date="06/04/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Transact-SQL (TSQL) を使用して SQL Data Warehouse データベースを作成する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

この記事では、Transact-SQL (T-SQL) を使用して SQL Data Warehouse データベースを作成する方法を示します。

## 前提条件
開始する前に、以下の前提条件を確認します。

- **Azure アカウント**: アカウントを作成するには、[Azure 無料試用版][]に関するページまたは [MSDN Azure クレジット][]に関するページを参照してください。
- **V12 Azure SQL Server**: [Azure ポータルを使用した Azure SQL Database 論理サーバーの作成][]に関する記事または [PowerShell を使用した Azure SQL Database 論理サーバーの作成][]に関する記事を参照してください。
- **リソース グループ名**: V12 Azure SQL Server と同じリソース グループを使用します。新しいリソース グループを作成する場合は、[リソース グループ][]に関する記事を参照してください。
- **Visual Studio と SQL Server Data Tools**: インストールの手順については、[Visual Studio と SSDT のインストール][]に関するページを参照してください。

> [AZURE.NOTE] 新しい SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。料金の詳細については、「[SQL Data Warehouse の価格][]」を参照してください。

## Visual Studio でデータベースを作成する

Visual Studio に慣れていない場合は、「[Visual Studio で SQL Data Warehouse に接続する][]」を参照してください。開始するには、Visual Studio で SQL Server オブジェクト エクスプローラーを開き、SQL Data Warehouse データベースをホストするサーバーに接続します。接続したら、**master** データベースに対して次の SQL コマンドを実行することで、SQL Data Warehouse を作成できます。このコマンドは、サービス目標を DW400 にしてデータベース MySqlDwDb を作成し、データベースのサイズを最大で 10 TB まで拡張できるようにします。

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## sqlcmd でデータベースを作成する

コマンド プロンプトで以下のように実行することによって、sqlcmd で同じコマンドを実行することもできます。

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

**MAXSIZE** と **SERVICE\_OBJECTIVE** パラメーターは、ディスクでデータベースが使用できる最大領域と、Data Warehouse インスタンスに割り当てられるコンピューティング リソースを指定します。サービス目標は、本質的には、DWU のサイズに比例して拡張される CPU とメモリの割り当てです。

MAXSIZE には、250 GB から 240 TB までの値を指定できます。サービス目標には、DW100 から DW2000 までの値を指定できます。MAXSIZE および SERVICE\_OBJECTIVE のすべての有効な値の完全な一覧については、[CREATE DATABASE][] に関する MSDN ドキュメントを参照してください。MAXSIZE と SERVICE\_OBJECTIVE のどちらも、[ALTER DATABASE][] T-SQL コマンドで変更することもできます。SERVICE\_OBJECTIVE を変更すると、サービスが再起動され、処理中のすべてのクエリが取り消されるため、注意する必要があります。MAXSIZE の変更は、単なるメタデータ操作であるため、サービスが再起動されることはありません。

## 次のステップ
SQL Data Warehouse のプロビジョニングが完了すると、[サンプル データを読み込んだり][]、[開発][]、[読み込み][]、[移行][]の方法を確認したりできます。

<!--Article references-->

[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Visual Studio で SQL Data Warehouse に接続する]: ./sql-data-warehouse-get-started-connect.md
[移行]: ./sql-data-warehouse-overview-migrate.md
[開発]: ./sql-data-warehouse-overview-develop.md
[読み込み]: ./sql-data-warehouse-overview-load.md
[サンプル データを読み込んだり]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Azure ポータルを使用した Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[PowerShell を使用した Azure SQL Database 論理サーバーの作成]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[リソース グループ]: ../azure-portal/resource-group-portal.md
[Visual Studio と SSDT のインストール]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[SQL Data Warehouse の価格]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Azure 無料試用版]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN Azure クレジット]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0608_2016-->