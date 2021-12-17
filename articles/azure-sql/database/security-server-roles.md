---
title: サーバーの役割
titleSuffix: Azure SQL Database
description: この記事では、Azure SQL データベースの論理サーバーのサーバー ロールの概要を示します
ms.service: sql-database
ms.subservice: security
author: AndreasWolter
ms.author: anwolter
ms.topic: conceptual
ms.date: 09/02/2021
ms.reviewer: vanto
ms.openlocfilehash: cfb24ee98bf00fa46f75b0bef321a7b1694e2113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630264"
---
# <a name="azure-sql-database-server-roles-for-permission-management"></a>アクセス許可の管理のための Azure SQL Database サーバー ロール

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database では、サーバーは論理的な概念であり、許可をサーバー レベルで付与することはできません。 アクセス許可の管理を簡素化するために、Azure SQL Database では、[論理的なサーバー](logical-servers.md)上のアクセス許可の管理に使用でいる、一連の固定サーバーレベルのロールが提供されています。 ロールとは、ログインをグループ化するセキュリティ プリンシパルです。

> [!NOTE]
> 本記事における "*ロール*" というコンセプトは、Windows オペレーティング システムの "*グループ*" に似ています。

通常のユーザーが作成した他のプリンシパルと区別するため、これらの特別な固定サーバーレベルのロールには、プレフィックス **##MS_** とサフィックス **##** が使用されています。

オンプレミスの SQL Server と同様に、サーバーのアクセス許可は階層的に編成されています。 これらのサーバーレベルのロールによって保持されるアクセス許可は、データベースのアクセス許可に反映されることがあります。 アクセス許可をデータベースに効果的に反映させるには、ログインするユーザーのユーザー アカウントがそのデータベース内にある必要があります。

たとえば、サーバーレベルのロール **##MS_ServerStateReader##** に、**VIEW SERVER STATE** という権限が付与されているとします。 このロールのメンバーであるログイン ユーザーが、データベース *master* と *WideWorldImporters* のユーザー アカウントを持っている場合、このユーザーはこれら 2 つのデータベースに対する **VIEW DATABASE STATE** というアクセス許可を持つことになります。 

> [!NOTE]
> ユーザー データベース内では、どのようなアクセス許可でも拒否することができ、その場合は、ロール メンバーシップによってサーバー全体の許可を上書きすることができます。 ただし、システム データベース *master* で、アクセス許可を許可したり、拒否することはできません。

Azure SQL Database では、現在、3 つの固定サーバー ロールが提供されています。 固定サーバー ロールに付与されるアクセス許可は変更できません。また、これらのロールに他の固定ロールをメンバーとして含めることはできません。 サーバーレベルの SQL ログインを、サーバーレベルのロールのメンバーとして追加することができます。

> [!IMPORTANT]
> 固定サーバー ロールの各メンバーは、そのロールに他のログインを追加することができます。

Azure SQL Database のログインとユーザーについては、「[SQL Database、SQL Managed Instance、Azure Synapse Analytics へのデータベース アクセスを承認する](logins-create-manage.md)」を参照してください。
  
## <a name="built-in-server-level-roles"></a>組み込みのサーバーレベルのロール

次の表に、固定サーバー レベル ロールとその機能を示します。  
  
|組み込みのサーバーレベルのロール|説明|  
|------------------------------|-----------------|  
|**##MS_DefinitionReader##**|固定サーバー ロール **##MS_DefinitionReader##** のメンバーは、このロールのメンバーがユーザー アカウントを持っているすべてのデータベース上の **VIEW ANY DEFINITION**、またはそれぞれの **VIEW DEFINITION** でカバーされているすべてのカタログ ビューを読み取ることができます。|  
|**##MS_ServerStateReader##**|固定サーバー ロール **##MS_ServerStateReader##** のメンバーは、このロールのメンバーがユーザー アカウントを持っているすべてのデータベース上の **VIEW SERVER STATE**、またはそれぞれの **VIEW DATABASE STATE** でカバーされているすべての動的管理ビュー (DMV) および機能を読み取ることができます。|
|**##MS_ServerStateManager##**|固定サーバー ロール **##MS_ServerStateManager##** のメンバーは、 **##MS_ServerStateReader##** ロールと同じアクセス許可を持っています。 また、`DBCC FREEPROCCACHE`、`DBCC FREESYSTEMCACHE ('ALL')`、`DBCC SQLPERF()` などの複数の管理操作へのアクセス許可を付与する **ALTER SERVER STATE** も保持します |  


## <a name="permissions-of-fixed-server-roles"></a>固定サーバー ロールのアクセス許可

組み込みのサーバーレベルの各ロールには、特定のアクセス許可が割り当てられています。 次の表は、サーバーレベルのロールに割り当てられているアクセス許可を示しています。 また、データベースにユーザー アカウントが存在する場合に継承される、データベースレベルのアクセス許可も示します。
  
|固定サーバー レベル ロール|サーバーレベルのアクセス許可|データベースレベルの権限 (ログインに一致するデータベース ユーザーが存在する場合)  
|-------------|----------|-----------------|  
|**##MS_DefinitionReader##**|VIEW ANY DATABASE、VIEW ANY DEFINITION、VIEW ANY SECURITY DEFINITION|VIEW DEFINITION、VIEW SECURITY DEFINITION|  
|**##MS_ServerStateReader##**|VIEW SERVER STATE、VIEW SERVER PERFORMANCE STATE、VIEW SERVER SECURITY STATE|VIEW DATABASE STATE、VIEW DATABASE PERFORMANCE STATE、VIEW DATABASE SECURITY STATE|  
|**##MS_ServerStateManager##**|ALTER SERVER STATE、VIEW SERVER STATE、VIEW SERVER PERFORMANCE STATE、VIEW SERVER SECURITY STATE|VIEW DATABASE STATE、VIEW DATABASE PERFORMANCE STATE、VIEW DATABASE SECURITY STATE|   
  
  
## <a name="working-with-server-level-roles"></a>サーバーレベルのロールの操作

次の表では、Azure SQL Database でサーバー レベルのロールを操作するためのシステム、ビュー、および関数について説明します。  
  
|機能|Type|説明|  
|-------------|----------|-----------------|  
|[IS_SRVROLEMEMBER &#40;Transact-SQL&#41;](/sql/t-sql/functions/is-srvrolemember-transact-sql)|Metadata|SQL ログインが、指定されたサーバー レベルのロールのメンバーであるかどうかを示します。|  
|[sys.server_role_members &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-server-role-members-transact-sql)|Metadata|各サーバー レベルのロールのメンバーごとに 1 行のデータを返します。|
|[sys.sql_logins &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-sql-logins-transact-sql)|Metadata|SQL ログインごとに 1 行のデータを返します。|
|[ALTER SERVER ROLE &#40;Transact-SQL&#41;](/sql/t-sql/statements/alter-server-role-transact-sql)|コマンド|サーバー ロールのメンバーシップを変更します。| 

## <a name="examples"></a><a name="_examples"></a> 使用例

このセクションの例では、Azure SQL Database のサーバーレベルのロールを使用する方法が示されています。  

### <a name="a-adding-a-sql-login-to-a-server-level-role"></a>A. SQL ログインをサーバーレベルのロールに追加する

次の例では、SQL ログイン 'Jiao' をサーバーレベルのロール ##MS_ServerStateReader## に追加します。 このステートメントは、仮想マスター データベースで実行する必要があります。
  
```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao;  
GO
```  

### <a name="b-listing-all-principals-sql-authentication-which-are-members-of-a-server-level-role"></a>B. サーバーレベルのロールのメンバーであるプリンシパル (SQL 認証) の一覧を表示する

次のステートメントでは、カタログ ビュー `sys.server_role_members` と `sys.sql_logins` を使用して、任意の固定サーバーレベルのロールのすべてのメンバーが返されます。 このステートメントは、仮想マスター データベースで実行する必要があります。
  
```sql  
SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;  
GO  
```  
### <a name="c-complete-example-adding-a-login-to-a-server-level-role-retrieving-metadata-for-role-membership-and-permissions-and-running-a-test-query"></a>C. 完全な例: サーバーレベルのロールへログインを追加し、ロール メンバーシップとアクセス許可のメタデータを取得し、テスト クエリを実行する

#### <a name="part-1-preparing-role-membership-and-user-account"></a>パート 1: ロール メンバーシップとユーザー アカウントの準備

仮想マスター データベースからこのコマンドを実行します。

```sql  
ALTER SERVER ROLE ##MS_ServerStateReader##
    ADD MEMBER Jiao

-- check membership in metadata:
select IS_SRVROLEMEMBER('##MS_ServerStateReader##', 'Jiao')
--> 1 = Yes

SELECT
        sql_logins.principal_id         AS MemberPrincipalID
    ,   sql_logins.name                 AS MemberPrincipalName
    ,   roles.principal_id              AS RolePrincipalID
    ,   roles.name                      AS RolePrincipalName
FROM sys.server_role_members AS server_role_members
INNER JOIN sys.server_principals AS roles
    ON server_role_members.role_principal_id = roles.principal_id
INNER JOIN sys.sql_logins AS sql_logins 
    ON server_role_members.member_principal_id = sql_logins.principal_id
;   
GO  
``` 

結果セットは次のようになります。
  
```
MemberPrincipalID MemberPrincipalName RolePrincipalID RolePrincipalName        
------------- ------------- ------------------ -----------   
6         Jiao      11            ##MS_ServerStateReader##   
```  

ユーザー データベースからこのコマンドを実行します。

```sql  
-- Creating a database-User for 'Jiao'
CREATE USER Jiao
    FROM LOGIN Jiao
;   
GO  
``` 

#### <a name="part-2-testing-role-membership"></a>パート 2: ロール メンバーシップのテスト

ログイン `Jiao` としてログインし、この例で使用されているユーザー データベースに接続します。

```sql  
-- retrieve server-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'Server')
;  

-- check server-role membership for `##MS_ServerStateReader##` of currently logged on User
SELECT USER_NAME(), IS_SRVROLEMEMBER('##MS_ServerStateReader##')
--> 1 = Yes

-- Does the currently logged in User have the `VIEW DATABASE STATE`-permission?
SELECT HAS_PERMS_BY_NAME(NULL, 'DATABASE', 'VIEW DATABASE STATE'); 
--> 1 = Yes

-- retrieve database-level permissions of currently logged on User
SELECT * FROM sys.fn_my_permissions(NULL, 'DATABASE')
GO 

-- example query:
SELECT * FROM sys.dm_exec_query_stats
--> will return data since this user has the necessary permission

``` 

## <a name="limitations-of-server-level-roles"></a>サーバーレベルのロールの制限事項

- ロールの割り当てが有効になるまでに、最大で 5 分かかる場合があります。 また、既存のセッションの場合、サーバー ロールの割り当てを変更しても、接続を閉じて再度開くまでは有効になりません。 これは、"*マスター*" データベースと、同じ論理サーバー上の他のデータベースとの間の分散アーキテクチャが原因です。
  - 部分的な回避策: 待ち時間を短縮し、データベース内のサーバー ロールの割り当てが最新であることを確認するには、サーバー管理者または Azure AD 管理者のアクセス許可を使用し、ログインがアクセスできるユーザー データベースで `DBCC FLUSHAUTHCACHE` を実行します。 現在ログオンしているユーザーは、メンバーシップの変更を有効にするために、`DBCC FLUSHAUTHCACHE` の実行後に再接続する必要があります。

- `IS_SRVROLEMEMBER()` は、"*マスター*" データベースではサポートされていません。


## <a name="see-also"></a>関連項目

- [データベース レベルのロール](/sql/relational-databases/security/authentication-access/database-level-roles)   
- [セキュリティ カタログ ビュー &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)   
- [セキュリティ関数 &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)   
- [アクセス許可 &#40;データベース エンジン&#41;](/sql/relational-databases/security/permissions-database-engine)
- [DBCC FLUSHAUTHCACHE (Transact-SQL)](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)
