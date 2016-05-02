<properties
   pageTitle="SQL Database のセキュリティ管理 - ログイン セキュリティ | Microsoft Azure"
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
   ms.date="03/22/2016"
   ms.author="rickbyh"/>

# SQL Database のセキュリティ: データベースのアクセスとログインのセキュリティの管理  

SQL Database のセキュリティ管理 (具体的にはサーバーレベル プリンシパル アカウントを使用してデータベースのアクセスとログインのセキュリティを管理する方法) について説明します。SQL Database とオンプレミスの SQL Server におけるログイン セキュリティ オプションの相違点と類似点についても確認します。クイック チュートリアルについては、[Azure SQL Database のチュートリアル: Azure SQL Database セキュリティの概要](sql-database-get-started-security.md)に関するページを参照してください。

## データベースのプロビジョニングとサーバーレベル プリンシパル ログイン

Microsoft Azure SQL Database では、サービスをサインアップすると、プロビジョニング処理が実行され、Azure SQL Database サーバー、**master** という名前のデータベース、ログインが作成されます。このログインは Azure SQL Database サーバーのサーバーレベル プリンシパルとなります。このログインは、オンプレミスの SQL Server インスタンスにおけるサーバーレベル プリンシパル (**sa**) に似ています。

Azure SQL Database サーバーレベル プリンシパル アカウントには、サーバーレベルとデータベースレベルのセキュリティをすべて管理する権限が常にあります。このトピックでは、SQL Database でサーバーレベル プリンシパルなどのアカウントを使用してログインとデータベースを管理する方法について説明します。

Azure のロールベースのアクセス制御 (RBAC) と Azure Resource Manager REST API を使用して SQL Database にアクセスする Azure ユーザーは、Azure ロールからアクセス許可を受け取ります。これらのロールは、データ プレーン操作ではなく、管理プレーン操作へのアクセスを提供します。管理プレーン操作では、SQL Database 内のさまざまなプロパティやスキーマ要素を読み取ることができます。また、SQL Database に関連する一部のサーバー レベルの機能の作成、削除、構成を行うことができます。これらの管理プレーン操作の多くは、Azure ポータルを使用しているときに表示および構成できる項目です。RBAC ロールを使用すると、データベース内の Azure ロールのメンバーのアクション (テーブルの表示など) は、データベース エンジンによってそれらのメンバーに対して実行されるので、標準の SQL Server アクセス許可システムの GRANT/REVOKE/DENY ステートメントによる影響は受けません。データの読み取りや変更はデータ プレーン操作であるため、RBAC ロールでこれらの操作を実行することはできません。詳細については、「[RBAC: 組み込みのロール](../active-directory/role-based-access-built-in-roles.md)」を参照してください。

> [AZURE.IMPORTANT] SQL Database V12 では、ユーザーは包含データベース ユーザーを使用して、データベースを認証できます。包含データベース ユーザーはログインする必要がありません。そのため、データベースは軽量になりますが、データベースへのアクセスを制御するサーバーレベルのプリンシパルの機能が減ります。包含データベース ユーザーを有効にすると、セキュリティに重大な影響があります。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」、「[包含データベース](https://technet.microsoft.com/library/ff929071.aspx)」、「[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)」、「[Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」を参照してください。

## SQL Database のセキュリティ管理の概要

SQL Database のセキュリティ管理は、オンプレミスの SQL Server インスタンスのセキュリティ管理と似ています。データベースレベルでのセキュリティ管理はほぼ同じで、使用できるパラメーターのみが異なります。SQL Database は 1 つ以上の物理コンピューターに拡張できるため、Azure SQL Database のサーバーレベル管理については別の方法が使用されます。次の表に、オンプレミスの SQL Server と Azure SQL Database のデータベース セキュリティ管理の相違点についてまとめています。

| 相違点 | オンプレミスの SQL Server | Azure SQL Database |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| サーバーレベル セキュリティを管理する場所 | SQL Server Management Studio のオブジェクト エクスプローラーの **[セキュリティ]** フォルダー | **master** データベースと Azure ポータル経由 |
| Windows 認証 | Active Directory ID | Azure Active Directory ID |
| ログインの作成に使用するサーバーレベル セキュリティ ロール | **securityadmin** 固定サーバー ロール | **master** データベースの **loginmanager** データベース ロール |
| ログインを管理するためのコマンド | CREATE LOGIN、ALTER LOGIN、DROP LOGIN | CREATE LOGIN、ALTER LOGIN、DROP LOGIN (一部のパラメーターが制限されます。また、**master** データベースに接続する必要があります)。 |
| すべてのログインの表示 | sys.server\_principals | sys.sql\_logins (**master** データベースに接続する必要があります)。|
| データベースの作成に使用するサーバー レベル ロール | **dbcreator** 固定データベース ロール | **master** データベースの **dbmanager** データベース ロール |
| データベースの作成に使用するコマンド | CREATE DATABASE | CREATE DATABASE (一部のパラメーターが制限されます。また、**master** データベースに接続する必要があります)。 |
| すべてのデータベースの一覧表示 | sys.databases | sys.databases (**master** データベースに接続する必要があります。) |

## サーバーレベル管理と master データベース

Azure SQL Database サーバーは、データベースのグループを定義する抽象的なものです。Azure SQL Database サーバーに関連付けられているデータベースは、マイクロソフトのデータ センターに置かれた別々の物理コンピューターの中に存在している可能性があります。そのすべてに対してサーバーレベル管理を実行するには、**master** という名前の 1 つのデータベースを使用します。

**master** データベースでは、ログインと、どのログインにデータベースや他のログインを作成するためのアクセス許可があるかが追跡されます。ログインまたはデータベースの作成 (CREATE)、変更 (ALTER)、削除 (DROP) を実行する際は、常に **master** データベースに接続する必要があります。**master** データベースには、ログインの表示に使用できる ``sys.sql_logins`` ビューと、データベースの表示に使用できる ``sys.databases`` ビューも用意されています。

> [AZURE.NOTE] ``USE`` コマンドによるデータベースの切り替えはサポートされていません。ターゲット データベースに対して直接接続を確立します。

Azure SQL Database では、オンプレミスの SQL Server インスタンスと同じ方法で、ユーザーとオブジェクトのデータベースレベルのセキュリティを管理できます。それぞれのコマンドで使用できるパラメーターのみが異なります。詳細については、「[Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md)」を参照してください。

## 包含データベース ユーザーの管理

サーバーレベル プリンシパルを使用してデータベースに接続することで、データベースに最初の包含データベース ユーザーを作成します。``CREATE USER``、``ALTER USER``、``DROP USER`` の各ステートメントを使用します。次の例では、user1 という名前のユーザーを作成します。

```
CREATE USER user1 WITH password='<Strong_Password>';
```

> [AZURE.NOTE] 包含データベース ユーザーを作成する際は、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

**ALTER ANY USER** 権限を持つユーザーは、追加の包含データベース ユーザーを作成できます。

SQL Database V12 のプレビュー機能では、包含データベース ユーザーとして Azure Active Directory ID をサポートしています。詳細については、「[Azure Active Directory の認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」を参照してください。

SQL Database では、包含データベース ユーザーを使用することをお勧めします。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

## ログインの管理

サーバー レベル プリンシパル ログインを使用して master データベースに接続し、ログインを管理します。``CREATE LOGIN``、``ALTER LOGIN``、``DROP LOGIN`` の各ステートメントを使用できます。次の例では、**login1** という名前のログインを作成します。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE] ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

#### 新しいログインの使用

作成したログインを使用して Microsoft Azure SQL Database に接続するには、まず ``CREATE USER`` コマンドを使用して、各ログインにデータベースレベルの権限を付与する必要があります。詳細については、後述する「**ログインのデータベース アクセスの許可**」セクションを参照してください。

一部のツールでは表形式データ ストリーム (TDS) の実装方法が異なるため、接続文字列でログインに Azure SQL Database サーバー名を付加することが必要になる場合があります。その際には、``<login>@<server>`` という表記を使用します。この場合は、``@`` 記号を使用してログインと Azure SQL Database サーバー名を区切ります。たとえば、ログイン名が **login1** であり、Azure SQL Database サーバーの完全修飾名が **servername.database.windows.net** である場合、接続文字列の username パラメーターは ****login1@servername** にする必要があります。この制限により、ログイン名として選択できるテキストが限定されます。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」を参照してください。

## ログインに対するサーバーレベル権限の付与

サーバーレベル プリンシパル以外のログインによってサーバーレベルのセキュリティを管理するために、Azure SQL Database では、ログイン作成用の **loginmanager** と、データベース作成用の **dbmanager** という 2 つのセキュリティ ロールを用意しています。これらのデータベース ロールには、**master** データベースのユーザーのみを追加することができます。

> [AZURE.NOTE] ログインまたはデータベースを作成するには、(**master** の論理表現である) **master** データベースに接続する必要があります。

### loginmanager ロール

オンプレミスの SQL Server インスタンスの **securityadmin** 固定サーバー ロールと同様に、Azure SQL Database の **loginmanager** データベース ロールにはログインを作成する権限があります。新しいログインを作成できるのは、サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) または **loginmanager** データベース ロールのメンバーのみです。

### dbmanager ロール

Azure SQL Database の **dbmanager** データベース ロールは、オンプレミスの SQL Server インスタンスの **dbcreator** 固定サーバー ロールに似ています。データベースを作成できるのは、サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) または **dbmanager** データベース ロールのメンバーのみです。**dbmanager** データベース ロールのメンバーになったユーザーは、Azure SQL Database の ``CREATE DATABASE`` コマンドを使用してデータベースを作成できますが、このコマンドは master データベース内で実行する必要があります。詳細については、「[CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)」を参照してください。

### SQL Database のサーバーレベル ロールを割り当てる方法

データベースや他のログインを作成できるログインや関連するユーザーを作成するには、次の手順に従います。

1. サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) の資格情報、または **loginmanager** データベース ロールの既存のメンバーの資格情報を使用して **master** データベースに接続します。
2. ``CREATE LOGIN`` コマンドを使用して、ログインを作成します。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」を参照してください。
3. ``CREATE USER`` コマンドを使用して、作成したログインの新しいユーザーを master データベースに作成します。詳細については、「[CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。
4. ストアド プロシージャ ``sp_addrolememeber`` を使用して、新しいユーザーを **dbmanager** データベース ロール、loginmanager データベース ロールのどちらか、またはその両方に追加します。

次のコード例は、ログイン (**login1**) と対応するデータベース ユーザー (**login1User**) を作成する方法を示しています。このユーザーは、**master** データベースへの接続中に、データベースまたは他のログインを作成することができます。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE] ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

## ログインのデータベース アクセスの許可

すべてのログインは **master** データベースに作成する必要があります。ログインが作成された後で、別のデータベースにそのログインのユーザー アカウントを作成できます。Azure SQL Database では、オンプレミスの SQL Server インスタンスと同じ方法でデータベース ロールをサポートします。

別のデータベースにユーザー アカウントを作成するには (ログインまたはデータベースをまだ作成していないとします)、次の手順に従います。

1. **master** データベースに接続します (**loginmanager** ロールと **dbmanager** ロールを持つログインを使用します)。
2. ``CREATE LOGIN`` コマンドを使用して、新しいログインを作成します。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」を参照してください。Windows 認証はサポートされません。
3. ``CREATE DATABASE`` コマンドを使用して、新しいデータベースを作成します。詳細については、「[CREATE DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/dn268335.aspx)」を参照してください。
4. 新しいデータベースとの接続を確立します (データベースの作成に使用したログインを使用します)。
5. ``CREATE USER`` コマンドを使用して、新しいデータベースに新しいユーザーを作成します。詳細については、「[CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」を参照してください。

次のコード例では、**login1** という名前のログインと、**database1** という名前のデータベースの作成方法を示します。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE] ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

次の例では、**database1** という名前のデータベースに、ログイン **login1** に対応する **login1User** という名前のデータベース ユーザーを作成する方法を示します。次の例を実行するには、まず database1 データベース内に **ALTER ANY USER** アクセス許可を持つログインを使用して、そのデータベースに新しく接続する必要があります。**db\_owner** ロールのメンバーとして接続するすべてのユーザーは、データベースを作成したログインのように、そのアクセス許可を持ちます。

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Azure SQL Database におけるこのようなデータベースレベルの権限モデルは、オンプレミスの SQL Server インスタンスと同じです。詳細については、SQL Server オンライン ブックの次のトピックをご覧ください。

- [ログイン、ユーザー、およびスキーマの管理方法に関するトピック](https://msdn.microsoft.com/library/aa337552.aspx)
- [レッスン 2: データベース オブジェクトに対する権限の構成](https://msdn.microsoft.com/library/ms365345.aspx)

> [AZURE.NOTE] Azure SQL Database のセキュリティ関連の Transact-SQL ステートメントでは、使用できるパラメーターが多少異なる場合があります。詳細については、特定のステートメントの構文に関するオンライン ブックを参照してください。

## ログインとデータベースの表示


Azure SQL Database サーバーのログインとデータベースを表示するには、master データベースの ``sys.sql_logins`` ビューと ``sys.databases`` ビューをそれぞれ使用します。次の例では、Azure SQL Database サーバーのすべてのログインとデータベースの一覧を表示する方法を示します。

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases;
```

## 関連項目

[Azure SQL Database チュートリアル: Azure SQL Database セキュリティの概要](sql-database-get-started-security.md) [Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md) [Azure Active Directory 認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)

<!---HONumber=AcomDC_0420_2016-->