<properties
	pageTitle="データベースの Stretch Database を有効にする | Microsoft Azure"
	description="Stretch Database のデータベースを設定する方法について説明します。"
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# データベースの Stretch Database を有効にする

Stretch Database のデータベースを設定するには、SQL Server Management Studio でデータベースの **[タスク]、[Stretch]、[有効化]** の順に選択し、**[Stretch Database を有効にする]** ウィザードを起動します。Transact-SQL を利用し、データベースの Stretch Database を有効にすることもできます。

テーブルに **[タスク]、[Stretch]、[有効化]** を選択したとき、Stretch Database のデータベースを有効にしていない場合、ウィザードにより Stretch Database のデータベースが設定され、プロセスの一環としてテーブルを設定できます。「[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)」の手順ではなく、このトピックの手順に従ってください。

データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。データベースで Stretch Database を有効にするには、CONTROL DATABASE アクセス許可も必要です。

## 開始する前に

-   Stretch のデータベースを設定する前に、Stretch Database Advisor を実行し、Stretch の対象となるデータベースとテーブルを特定することが推奨されます。Stretch Database Advisor はブロック問題も特定します。詳細については、「[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)」を参照してください。

-   「[Stretch Database の制限事項](sql-server-stretch-database-limitations.md)」を確認します。

-   Stretch Database はデータを Azure に移行します。そのため、請求のために Azure アカウントとサブスクリプションが必要になります。Azure アカウントを取得するには、[ここをクリック](http://azure.microsoft.com/pricing/free-trial/)してください。

-   必要な情報を用意し、新しいリモート データベースを作成するか、既存のリモート データベースを選択するか、ローカル サーバーとリモート サーバーの通信を可能にするファイアウォール ルールを作成します。

## <a name="EnableTSQLServer"></a>前提条件: サーバーで Stretch Database を有効にするためのアクセス許可
データベースまたはテーブルで Stretch Database を有効にする前に、ローカル サーバーでそれを有効にする必要があります。この操作にはアクセス許可として sysadmin または serveradmin が必要になります。

-   必要な管理アクセス許可がある場合、**[Stretch Database を有効にする]** ウィザードにより Stretch のサーバーが設定されます。

-   必要なアクセス許可がない場合、ウィザードを自分で実行するか、管理者が実行する前に、管理者が **sp\_configure** を実行し、オプションを手動で有効にする必要があります。

サーバーで手動で Stretch Database を有効にするには、**sp\_configure** を実行し、**remote data archive** オプションをオンにします。次の例では、値が 1 に設定され、**remote data archive** オプションが有効になります。

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
詳細については、「[Configure the remote data archive Server Configuration Option (サーバー設定オプションの remote data archive を設定する)](https://msdn.microsoft.com/library/mt143175.aspx)」と「[sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx)」を参照してください。

## <a name="Wizard"></a>ウィザードを使用し、データベースで Stretch Database を有効にする
入力する情報と選択肢を含む、[Stretch Database を有効にする] ウィザードの詳細については、「[[Enable Database for Stretch (Stretch Database を有効にする)] ウィザードを実行する方法の概要](sql-server-stretch-database-wizard.md)」をご覧ください。

## <a name="EnableTSQLDatabase"></a>Transact-SQL を使用し、データベースで Stretch Database を有効にする
個々のテーブルで Stretch Database を有効にする前に、データベースでそれを有効にする必要があります。

データベースまたはテーブルで Stretch Database を有効にするには、db\_owner アクセス許可が必要です。データベースで Stretch Database を有効にするには、CONTROL DATABASE アクセス許可も必要です。

1.  開始する前に、Stretch Database が移行するデータの既存の Azure サーバーを選択するか、新しいサーバーを作成します。

2.  Azure サーバーで、SQL Server とリモート サーバーの通信を可能にするファイアウォール ルールを SQL Server の IP アドレス (または、IP アドレス範囲)で作成します。

3.  Stretch Database の SQL Server データベースを構成するには、データベースにデータベース マスター キーを与える必要があります。データベース マスター キーにより、Stretch Database がリモート データベースの接続に使用する資格情報が守られます。データベース マスター キーを手動で作成するには、「[CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)」と「[データベース マスター キーの作成](https://msdn.microsoft.com/library/aa337551.aspx)」を参照してください。

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD ='<password>'
    ```

4.  Stretch Database のデータベースを設定するとき、オンプレミス SQL Server とリモート Azure サーバーの間で通信するための Stretch Database の資格情報を入力する必要があります。2 つのオプションがあります。

    -   管理者の資格情報を指定できます。

        -   ウィザードを実行して Stretch Database を有効にする場合、その時点で資格情報を作成できます。

        -   **ALTER DATABASE** を実行し、Stretch Database を有効にする場合、その前に資格情報を手動で作成する必要があります。

        資格情報を手動で作成するには、「[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx)」を参照してください。資格情報を作成するには、ALTER ANY CREDENTIAL というアクセス許可が必要です。

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   SQL Server のフェデレーション サービス アカウントを使用すれば、次の条件に該当するとき、リモート Azure サーバーと通信できます。

        -   SQL Server のインスタンスを実行しているサービス アカウントがドメイン アカウントです。

        -   Active Directory が Azure Active Directory との連合になっているドメインにドメイン アカウントが属しています。

        -   Azure Active Directory 認証をサポートするようにリモート Azure サーバーが設定されます。

        -   SQL Server のインスタンスを実行しているサービス アカウントは、リモート Azure サーバーで dbmanager または sysadmin アカウントとして設定する必要があります。

5.  Stretch Database のデータベースを設定するには、ALTER DATABASE コマンドを実行します。

    1.  SERVER 引数の場合、既存の Azure サーバーの名前を指定します。名前の `.database.windows.net` 部分も含まれます。たとえば、`MyStretchDatabaseServer.database.windows.net` のようになります。

    2.  CREDENTIAL 引数で既存の管理者資格情報を指定するか、FEDERATED\_SERVICE\_ACCOUNT = ON を指定します。次の例では、既存の資格情報が指定されています。

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = '<server_name>',
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## 次のステップ
Stretch Database の追加テーブルを有効にします。データ移行を監視し、Stretch 対応のデータベースとテーブルを管理します。

-   [テーブルの Stretch Database を有効にして](sql-server-stretch-database-enable-table.md)追加テーブルを有効にします。

-   [Stretch Database を監視して](sql-server-stretch-database-monitor.md)データベース移行の状態を確認します。

-   [Stretch Database を一時停止し、再開します](sql-server-stretch-database-pause.md)。

-   [Stretch Database を管理し、問題を解決します。](sql-server-stretch-database-manage.md)

-   [Stretch 対応データベースをバックアップします。](sql-server-stretch-database-backup.md)

## 関連項目

[Stretch Database のデータベースとテーブルを特定する](sql-server-stretch-database-identify-databases.md)

[ALTER DATABASE SET のオプション (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0608_2016-->