---
title: T-SQL を使用して SQL ServerWindows ユーザーおよびグループをマネージド インスタンスに移行する
description: オンプレミスの SQL Server の Windows ユーザーとグループをマネージド インスタンスに移行する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227917"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>チュートリアル:T-SQL DDL 構文を使用してオンプレミスの SQL Server の Windows ユーザーとグループを Azure SQL Database Managed Instance に移行する

> [!NOTE]
> この記事でユーザーとグループをマネージド インスタンスに移行するために使用する構文は、**パブリック プレビュー**段階です。

この記事では、T-SQL 構文を使用して、オンプレミスの SQL Server 内の Windows ユーザーとグループを既存の Azure SQL Database マネージド インスタンスに移行するプロセスについて説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - SQL Server のログインを作成する
> - 移行用のテスト データベースを作成する
> - ログイン、ユーザー、およびロールを作成する
> - データベースをバックアップしてマネージド インスタンス (MI) に復元する
> - ALTER USER 構文を使用して手動でユーザーを MI に移行する
> - 新しいマップされたユーザーによる認証のテスト

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件が適用されます。

- Windows ドメインは Azure Active Directory (Azure AD) とフェデレーションされています。
- ユーザー/グループを作成できる Active Directory へのアクセス権。
- オンプレミス環境の既存の SQL Server。
- 既存のマネージド インスタンス。 「[クイック スタート:Azure SQL Database マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)」を参照してください。
  - Azure AD ログインを作成するには、マネージド インスタンス内の `sysadmin` が使用される必要があります。
- [マネージド インスタンスに対する Azure AD 管理者を作成します](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)。
- ネットワーク内のマネージド インスタンスに接続できます。 その他の情報については、次の記事を参照してください。 
    - [Azure SQL Database Managed Instance にアプリケーションを接続する](sql-database-managed-instance-connect-app.md)
    - [クイック スタート: オンプレミスから Azure SQL Database Managed Instance へのポイント対サイト接続を構成する](sql-database-managed-instance-configure-p2s.md)
    - [Azure SQL Database マネージド インスタンスのパブリック エンドポイントの構成](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL 構文

Azure AD 認証を使用してオンプレミスの SQL Server の Windows ユーザーとグループをマネージド インスタンスに移行する処理をサポートするために使用される T-SQL DDL 構文を次に示します。

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>引数

_domainName_</br>
ユーザーのドメイン名を指定します。

_userName_</br>
データベース内で識別されるユーザーの名前を指定します。

_= loginName\@domainName.com_</br>
Azure AD ログインにユーザーを再マッピングします

_groupName_</br>
データベース内で識別されるグループの名前を指定します。

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>パート 1: オンプレミスの SQL Server のユーザーとグループのログインを作成する

> [!IMPORTANT]
> 次の構文では、SQL サーバーでユーザーとグループのログインを作成します。 以下の構文を実行する前に、ユーザーとグループが Active Directory (AD) 内に存在することを確認する必要があります。 </br> </br>
> Users: testUser1, testGroupUser </br>
> Group: migration - testGroupUser は AD の移行グループに属している必要があります

以下の例では、ドメイン _aadsqlmi_ の _testUser1_ という名前のアカウントのログインを SQL Server で作成します。 

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases
 
use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;   
go; 

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser]. 
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;    
go; 


-- Check logins were created
select * from sys.server_principals; 
go; 
```

このテスト用のデータベースを作成します。

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>パート 2: Windows ユーザーとグループを作成し、ロールとアクセス許可を追加する

次の構文を使用して、テスト ユーザーを作成します。

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

ユーザーのアクセス許可を確認します。

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

ロールを作成し、そのロールにテスト ユーザーを割り当てます。

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

次のクエリを使用して、特定のロールに割り当てられたユーザー名を表示します。

```sql
-- Display user name assigned to a specific role 
SELECT DP1.name AS DatabaseRoleName,    
   isnull (DP2.name, 'No members') AS DatabaseUserName    
 FROM sys.database_role_members AS DRM   
 RIGHT OUTER JOIN sys.database_principals AS DP1   
   ON DRM.role_principal_id = DP1.principal_id   
 LEFT OUTER JOIN sys.database_principals AS DP2   
   ON DRM.member_principal_id = DP2.principal_id   
WHERE DP1.type = 'R' 
ORDER BY DP1.name; 
```

次の構文を使用して、グループを作成します。 次に、ロール `db_owner` にグループを追加します。

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration]; 
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration'; 
go 

--Check the db_owner role for 'aadsqlmi\migration' group 
select is_rolemember('db_owner', 'aadsqlmi\migration')   
go 
-- Output  ( 1 means YES) 
```

次の構文を使用して、テスト テーブルを作成し、データを追加します。

```sql
-- Create a table and add data 
create table test ( a int, b int); 
go 

insert into test values (1,10) 
go 

-- Check the table values
select * from test; 
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>パート 3: 個々のユーザー データベースをバックアップしてマネージド インスタンスに復元する

「[バックアップと復元によるデータベースのコピー](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)」の記事を使用するか、次の構文を使用して、移行データベースのバックアップを作成します。

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

「[クイックスタート:データベースをマネージド インスタンスに復元する](sql-database-managed-instance-get-started-restore.md)」に従います。

## <a name="part-4-migrate-users-to-managed-instance"></a>パート 4:マネージド インスタンスにユーザーを移行する

> [!NOTE]
> 作成後にマネージド インスタンス機能の Azure AD 管理者が変更されました。 詳しくは、「[マネージド インスタンス用の新しい Azure AD 管理機能](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)」をご覧ください。

ALTER USER コマンドを実行して、マネージド インスタンスの移行プロセスを完了します。

1. マネージド インスタンス用の Azure AD 管理者アカウントを使用して、マネージド インスタンスにサインインします。 次に、以下の構文を使用して、マネージド インスタンスに Azure AD ログインを作成します。 詳細については、「[チュートリアル:Azure AD サーバー プリンシパル (ログイン) を使用した Azure SQL Database におけるマネージド インスタンスのセキュリティ](sql-database-managed-instance-aad-security-tutorial.md)」を参照してください。

    ```sql
    use master 
    go 
    
    -- Create login for AAD user [testUser1@aadsqlmi.net] 
    create login [testUser1@aadsqlmi.net] from external provider 
    go
    
    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net] 
    create login [migration] from external provider 
    go 
     
    --Check the two new logins 
    select * from sys.server_principals 
    go
    ```

1. 適切なデータベース、テーブル、およびプリンシパルが移行されたことを確認します。

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. ALTER USER 構文を使用して、オンプレミス ユーザーを Azure AD ログインにマップします。

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net]; 
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
    -- New user testUser1@aadsqlmi.net should be there instead 
     
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go

    -- Check a specific role   
    -- Display Db user name assigned to a specific role 
    SELECT DP1.name AS DatabaseRoleName,    
    isnull (DP2.name, 'No members') AS DatabaseUserName    
    FROM sys.database_role_members AS DRM   
    RIGHT OUTER JOIN sys.database_principals AS DP1   
    ON DRM.role_principal_id = DP1.principal_id   
    LEFT OUTER JOIN sys.database_principals AS DP2   
    ON DRM.member_principal_id = DP2.principal_id   
    WHERE DP1.type = 'R' 
    ORDER BY DP1.name;
    ```

1. ALTER USER 構文を使用して、オンプレミス グループを Azure AD ログインにマップします。

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration]; 
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals; 
    go 
     
    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions; 
    go 
     
    --Check the db_owner role for 'aadsqlmi\migration' user 
    select is_rolemember('db_owner', 'migration')   
    go 
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>パート 5: Azure AD ユーザーまたはグループの認証をテストする

以前に ALTER USER 構文を使用して Azure AD ログインにマップしたユーザーを使用して、マネージド インスタンスへの認証をテストします。
 
1. `aadsqlmi\testUser1` という MI サブスクリプションを使用して、フェデレーション VM にログインします
1. SQL Server Management Studio (SSMS) を使用し、**Active Directory Integrated** 認証を使用してマネージド インスタンスにサインインし、データベース `migration` に接続します。
    1. SSMS オプション **[Active Directory - MFA サポートで汎用]** で testUser1@aadsqlmi.net の資格情報を使用してサインインすることもできます。 ただし、この場合、シングル サインオン メカニズムを使用することはできず、パスワードを入力する必要があります。 フェデレーション VM を使用してマネージド インスタンスにログインする必要はありません。
1. ロール メンバー **SELECT** の一部として、`test` テーブルから選択できます

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Windows グループ `migration` のメンバーを使用して、マネージド インスタンスに対する認証をテストします。 移行の前に、ユーザー `aadsqlmi\testGroupUser` がグループ `migration` に追加されている必要があります。

1. `aadsqlmi\testGroupUser` という MI サブスクリプションを使用して、フェデレーション VM にログインします 
1. SSMS と **Active Directory 統合**認証を使用して、MI サーバーとデータベース `migration` に接続します
    1. SSMS オプション **[Active Directory - MFA サポートで汎用]** で testGroupUser@aadsqlmi.net の資格情報を使用してサインインすることもできます。 ただし、この場合、シングル サインオン メカニズムを使用することはできず、パスワードを入力する必要があります。 フェデレーション VM を使用してマネージド インスタンスにログインする必要はありません。 
1. `db_owner` ロールの一部として、新しいテーブルを作成できます。

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Azure SQL DB の設計上の既知の問題により、グループのメンバーとして実行されるテーブルの作成ステートメントは、次のエラーで失敗します。 </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> 現在の回避策は、上記の <dbo.new> の場合に既存のスキーマを使用してテーブルを作成することです

## <a name="next-steps"></a>次のステップ

- [チュートリアル:DMS を使用してオフラインで SQL Server を Azure SQL Database Managed Instance に移行する](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)