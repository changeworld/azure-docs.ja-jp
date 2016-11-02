<properties
   pageTitle="SQL Database の認証と承認: アクセス権の付与 | Microsoft Azure"
   description="SQL Database のセキュリティ管理 (具体的にはサーバーレベル プリンシパル アカウントを使用してデータベースのアクセスとログインのセキュリティを管理する方法) について説明します。"
   keywords="SQL Database のセキュリティ,データベース セキュリティ管理,ログイン セキュリティ,データベース セキュリティ,データベース アクセス"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# SQL Database の認証と承認: アクセス権の付与 


> [AZURE.SELECTOR]
- [Notification Hubs の使用](sql-database-get-started-security.md)
- [アクセス権の付与](sql-database-manage-logins.md)


管理者、管理者以外、およびロールの SQL Database のアクセスの概念の概要については、最初にこのページをお読みください。

## 制限なしの管理者アカウント

仮想 master データベースとすべてのユーザー データベースに対するアクセスに無制限のアクセス許可を持つ管理者アカウントは 2 つ考えられます。これらのアカウントは、サーバーレベル プリンシパル アカウントと呼ばれます。

### Azure SQL Database サブスクライバー アカウント 

論理 SQL インスタンスを作成すると、SQL Database サブスクライバー アカウントと呼ばれる 1 つのログイン アカウントが作成されます。このアカウントは、SQL Server 認証 (ユーザー名とパスワード) を使用して接続します。このアカウントは、論理サーバー インスタンスと、そのインスタンスに接続されているすべてのユーザー データベースの管理者です。サブスクライバー アカウントのアクセス許可を制限することはできません。これらのアカウントのうち、存在できるのは 1 つだけです。

### Azure Active Directory 管理者
1 つの Azure Active Directory アカウントも、管理者として構成できます。このアカウントは、個別の Azure AD ユーザーであっても、複数の Azure AD ユーザーを含む Azure AD グループであってもかまいません。Azure AD 管理者の構成は任意ですが、Azure AD アカウントが SQL Database に接続する際に Windows 認証を使用する場合は、Azure AD 管理者を構成する必要があります。Azure Active Directory アクセスの構成の詳細については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」と「[SSMS support for Azure AD MFA with SQL Database and SQL Data Warehouse](sql-database-ssms-mfa-authentication.md)」 (SQL Database と SQL Data Warehouse での Azure AD MFA のための SSMS のサポート) を参照してください。

### ファイアウォールの構成
サーバー レベルのファイアウォールを構成すると、Azure SQL Database サブスクライバー アカウントと Azure Active Directory アカウントは、master データベースとすべてのユーザー データベースに接続できます。サーバー レベルのファイアウォールは、ポータルで構成できます。接続が確立されると、[sp\_set\_firewall\_rule](https://msdn.microsoft.com/library/dn270017.aspx) Transact-SQL ステートメントを使用して、サーバー レベルのファイアウォール規則を追加で構成することもできます。ファイアウォールの構成の詳細については、[Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する方法](sql-database-configure-firewall-settings.md)に関する記事を参照してください。

### Administrator access path

サーバー レベルのファイアウォールが正しく構成されている場合、SQL Database サブスクライバー アカウントと Azure Active Directory SQL Server 管理者は、SQL Server Management Studio や SQL Server Data Tools などのクライアント ツールを使用して接続できます。すべての機能を提供しているのは、最新のツールだけです。次の図は、2 つの管理者アカウントの標準的な構成を示しています。![Administrator access path](./media/sql-database-manage-logins/1sql-db-administrator-access.png)

サーバー レベルのファイアウォールで開かれているポートを使用する場合、管理者はどの SQL データベースにも接続できます。

### SQL Server Management Studio を使用したデータベースへの接続
SQL Server Management Studio を使用した接続のチュートリアルについては、「[SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)」を参照してください。


> [AZURE.IMPORTANT] 常に最新バージョンの Management Studio を使用して、Microsoft Azure と SQL Database の更新プログラムとの同期を維持することをお勧めします。[SQL Server Management Studio を更新します](https://msdn.microsoft.com/library/mt238290.aspx)。


## その他の特殊アカウント
SQL Database には、仮想 master データベースの制限付き管理者ロールが 2 つ用意されています。このロールには、ユーザー アカウントを追加できます。

### データベース作成者
管理者アカウントは、新しいデータベースを作成できます。データベースを作成できる追加のアカウントを作成するには、master にユーザーを作成し、そのユーザーを特殊な **dbmanager** データベース ロールに追加する必要があります。そのユーザーは、包含データベース ユーザーにすることも、仮想 master データベースの SQL Server ログインに基づくユーザーにすることもできます。

1.	管理者アカウントを使用して、仮想 master データベースに接続します。
2.	省略可能な手順: [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx) ステートメントを使用して、SQL Server 認証ログインを作成します。サンプル ステートメントは、次のとおりです。

     ```
     CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
     ```

     > [AZURE.NOTE] ログイン ユーザーまたは包含データベース ユーザーを作成するときは、強力なパスワードを使用します。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

    パフォーマンスを向上させるため、ログイン (サーバー レベルのプリンシパル) はデータベース レベルで一時的にキャッシュされます。認証キャッシュを更新する方法については、「[DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)」をご覧ください。

3.	仮想 master データベースで、[CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用してユーザーを作成します。このユーザーは、Azure Active Directory 認証の包含データベース ユーザー (Azure AD 認証用の環境を構成した場合)、SQL Server 認証の包含データベース ユーザー、または SQL Server 認証ログインに基づく SQL Server 認証ユーザー (前の手順で作成したもの) にすることができます。 サンプル ステートメントは、次のとおりです。

     ```
     CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
     CREATE USER Tran WITH PASSWORD = '<strong_password>';
     CREATE USER Mary FROM LOGIN Mary; 
     ```

4.	[ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) ステートメントを使用して、新しいユーザーを **dbmanager** データベース ロールに追加します。サンプル ステートメントは、次のとおりです。

     ```
     ALTER ROLE dbmanager ADD MEMBER Mary; 
     ALTER ROLE dbmanager ADD MEMBER [mike@contoso.com];
     ```

     > [AZURE.NOTE] dbmanager は仮想 master データベースのデータベース ロールであるため、dbmanager ロールにはユーザーのみを追加できます。データベース レベルのロールにサーバー レベルのログインを追加することはできません。

5.	必要に応じて、新しいユーザーに接続を許可するようにサーバー レベルのファイアウォールを構成します。

これで、ユーザーは仮想 master データベースに接続し、新しいデータベースを作成できるようになりました。データベースを作成したアカウントは、そのデータベースの所有者になります。

### ログイン マネージャー

必要であれば、同じ手順を実行して (ログインとユーザーを作成し、ユーザーを **loginmanager** ロールに追加して)、ユーザーが仮想 master に新しいログインを作成できるようにすることができます。通常、この作業は必要ありません。Microsoft は、ログインに基づくユーザーを使用する代わりに、データベース レベルで認証される包含データベース ユーザーを使用することを推奨しているからです。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

## 管理者以外のユーザー

一般に、管理者以外のアカウントは、仮想 master データベースへのアクセスを必要としません。[CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx) ステートメントを使用して、データベース レベルの包含データベース ユーザーを作成してください。このユーザーは、Azure Active Directory 認証の包含データベース ユーザー (Azure AD 認証用の環境を構成した場合)、SQL Server 認証の包含データベース ユーザー、または SQL Server 認証ログインに基づく SQL Server 認証ユーザー (前の手順で作成したもの) にすることができます。 詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

ユーザーを作成するには、データベースに接続し、次の例のようなステートメントを実行します。

```
CREATE USER Mary FROM LOGIN Mary; 
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

最初、ユーザーを作成できるのは、データベースの管理者の 1 人か所有者だけです。新しいユーザーの作成を他のユーザーに許可するには、次のようなステートメントを使用して、選択したユーザーに `ALTER ANY USER` アクセス許可を付与します。

```
GRANT ALTER ANY USER TO Mary;
```

データベースのフル コントロールを他のユーザーに与えるには、`ALTER ROLE` ステートメントを使用して、そのユーザーを **db\_owner** 固定データベース ロールのメンバーにします。

> [AZURE.NOTE] ログインに基づくデータベース ユーザーを作成するのは、主に、複数のデータベースへのアクセスを必要とする SQL Server 認証ユーザーがいる場合があるためです。ログインに基づくユーザーは、ログインと、そのログインのために保持されている 1 つのパスワードのみに関連付けられています。個々のデータベース内の包含データベース ユーザーは、それぞれが個別のエンティティであり、それぞれが独自のパスワードを保持します。このため、包含データベース ユーザーは、同じパスワードを保持しない場合に混乱することがあります。

### データベース レベルのファイアウォールの構成

ベスト プラクティスとして、管理者以外のユーザーは、使用するデータベースにファイアウォール経由でのみアクセスできるようにすることをお勧めします。サーバー レベルのファイアウォール経由で IP アドレスを承認し、すべてのデータベースへのアクセスを許可するのではなく、[sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) ステートメントを使用して、データベース レベルのファイアウォールを構成してください。データベース レベルのファイアウォールは、ポータルを使用して構成することはできません。

### Non-administrator access path

データベース レベルのファイアウォールが正しく構成されると、データベース ユーザーは SQL Server Management Studio や SQL Server Data Tools などのクライアント ツールを使用して接続できます。すべての機能を提供しているのは、最新のツールだけです。次の図は、管理者以外の標準的なアクセス パスを示しています。![Non-administrator access path](./media/sql-database-manage-logins/2sql-db-nonadmin-access.png)
 
## グループとロール
効率的なアクセス管理では、個々のユーザーではなく、グループとロールに割り当てられたアクセス許可を使用します。たとえば、Azure Active Directory 認証を使用する場合は、次のようにします。

- Azure Active Directory ユーザーを Azure Active Directory グループに含めます。そのグループ用に包含データベース ユーザーを作成します。データベース ロールに 1 人以上のデータベース ユーザーを含めます。その後、そのデータベース ロールにアクセス許可を割り当てます。

SQL Server 認証を使用している場合は、次のようにします。

- データベースに包含データベース ユーザーを作成します。データベース ロールに 1 人以上のデータベース ユーザーを含めます。その後、そのデータベース ロールにアクセス許可を割り当てます。

データベース ロールは、**db\_owner**、**db\_ddladmin**、**db\_datawriter**、**db\_datareader**、**db\_denydatawriter**、**db\_denydatareader** などの組み込みロールを指定できます。**db\_owner** は、少数のユーザーのみに完全なアクセス許可を付与する際によく使用されます。他の固定データベース ロールは、開発段階の単純なデータベースをすばやく取得するには便利ですが、運用段階のほとんどのデータベースには推奨されません。たとえば、**db\_datareader** 固定データベース ロールは、データベース内のすべてのテーブルへの読み取りアクセスを許可しますが、これは、通常、必要以上のことです。[CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) ステートメントを使用して独自のユーザー定義データベース ロールを作成し、各ロールに対してビジネスのニーズに応じて必要な最小限のアクセス許可を慎重に付与することをお勧めします。ユーザーが複数のロールのメンバーである場合は、それらのアクセス許可すべてが集約されます。

## アクセス許可

SQL Database では、個別に許可または拒否できるアクセス許可が 100 個を超えています。これらのアクセス許可の多くは、入れ子になっています。たとえば、スキーマに対する `UPDATE` アクセス許可には、そのスキーマ内の各テーブルに対する `UPDATE` アクセス許可が含まれています。ほとんどのアクセス許可システムと同様に、アクセス許可の拒否は許可より優先されます。入れ子になっている性質と、アクセス許可の数により、データベースを正しく保護するのに適切なアクセス許可システムを設計するには、慎重な調査を行う場合があります。まず「[権限 (データベース エンジン)](https://msdn.microsoft.com/library/ms191291.aspx)」でアクセス許可の一覧を確認してから、アクセス許可の[ポスター サイズの図](http://go.microsoft.com/fwlink/?LinkId=229142)も確認してください。


## 次のステップ

[SQL Database の保護](sql-database-security.md)

[テーブルの作成 (チュートリアル)](https://msdn.microsoft.com/library/ms365315.aspx)

[テーブルのデータの挿入と更新 (チュートリアル)](https://msdn.microsoft.com/library/ms365309.aspx)

[テーブルのデータの読み取り (チュートリアル)](https://msdn.microsoft.com/library/ms365310.aspx)

[ビューとストアド プロシージャの作成](https://msdn.microsoft.com/library/ms365311.aspx)

[データベース オブジェクトへのアクセス権の付与](https://msdn.microsoft.com/library/ms365327.aspx)


## その他のリソース

[SQL Database の保護](sql-database-security.md)

[SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589.aspx)

<!---HONumber=AcomDC_0914_2016-->