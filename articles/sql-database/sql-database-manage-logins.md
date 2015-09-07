<properties
   pageTitle="Azure SQL Database におけるデータベースとログインの管理 | Microsoft Azure"
	description="SQL Database でサーバーレベル プリンシパルなどのアカウントを使用してログインとデータベースを管理する方法"
	services="sql-database"
	documentationCenter=""
	authors="BYHAM"
	manager="jeffreyg"
	editor=""
	tags=""/>

<tags
   ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/20/2015"
	ms.author="rickbyh"/>

# Azure SQL Database におけるデータベースとログインの管理

Microsoft Azure SQL Database では、サービスをサインアップすると、プロビジョニング処理が実行され、Azure SQL データベース サーバー、**master** という名前のデータベース、ログインが作成されます。このログインは Azure SQL Database サーバーのサーバーレベル プリンシパルとなります。このログインは、オンプレミスの SQL Server インスタンスにおけるサーバーレベル プリンシパルである **sa** と似ています。

Azure SQL Database サーバーレベル プリンシパル アカウントには、サーバーレベルとデータベースレベルのセキュリティをすべて管理する権限が常にあります。このトピックでは、SQL Database でサーバーレベル プリンシパルなどのアカウントを使用してログインとデータベースを管理する方法について説明します。

> [AZURE.IMPORTANT]SQL Database V12 では、ユーザーは包含データベース ユーザーを使用して、データベースを認証できます。包含データベース ユーザーはログインする必要がありません。そのため、データベースは軽量になりますが、データベースへのアクセスを制御するサーバーレベルのプリンシパルの機能が減ります。包含データベース ユーザーを有効にすると、セキュリティに重大な影響があります。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」、「[CREATE USER (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」、「[包含データベース](https://msdn.microsoft.com/library/ff929071.aspx)」をご覧ください。

## SQL Database のセキュリティ管理の概要

SQL Database におけるセキュリティ管理は、オンプレミスの SQL Server インスタンスにけるセキュリティ管理と似ています。データベースレベルでのセキュリティ管理はほぼ同じで、使用できるパラメーターのみが異なります。SQL Database は 1 つ以上の物理コンピューターに拡張できるため、Azure SQL Database のサーバーレベル管理については別の方法が使用されます。次の表に、オンプレミスの SQL Server と Azure SQL Database のセキュリティ管理の相違点についてまとめています。

| 相違点 | オンプレミスの SQL Server | Azure SQL Database |
|------------------------------------------------|-----------------------------------------------------------------------------|--------------------------------------------------|
| サーバーレベル セキュリティを管理する場所 | SQL Server Management Studio のオブジェクト エクスプ ローラーの [セキュリティ] フォルダー | master データベースと Azure ポータル経由 |
| ログインの作成に使用するサーバーレベル セキュリティ ロール | securityadmin 固定サーバー ロール。詳細については、「サーバー レベルのロール」をご覧ください。 | master データベースの loginmanager データベース ロール |
| ログインを管理するためのコマンド | CREATE LOGIN、ALTER LOGIN、DROP LOGIN | CREATE LOGIN、ALTER LOGIN、DROP LOGIN (一部のパラメーターが制限されます。また、master データベースに接続する必要があります。) |
| すべてのログインの表示 | sys.server\_principals | sys.sql\_logins (master データベースに接続する必要があります。)|
| データベースの作成に使用するサーバー レベル ロール | dbcreator 固定データベース ロール。詳細については、「サーバー レベルのロール」をご覧ください。 | master データベースの dbmanager データベース ロール |
| データベースの作成に使用するコマンド | CREATE DATABASE | CREATE DATABASE (一部のパラメーターが制限されます。また、master データベースに接続する必要があります。) |
| すべてのデータベースの一覧表示 | sys.databases | sys.databases (master データベースに接続する必要があります。) |

### サーバーレベル管理と master データベース

Azure SQL Database サーバーは、データベースのグループを定義する抽象的なものです。Azure SQL Database サーバーに関連付けられているデータベースは、マイクロソフトのデータ センターに置かれた別々の物理コンピューターの中に存在している可能性があります。そのすべてに対してサーバーレベル管理を実行するには、master という名前の 1 つのデータベースを使用します。

master データベースによってログインが追跡され、どのログインにデータベースや他のログインを作成する権限があるかが追跡されます。ログインまたはデータベースの作成 (CREATE)、変更 (ALTER)、削除 (DROP) を実行する際には常に、master データベースに接続する必要があります。**master** データベースには、ログインの表示に使用できる ``sys.sql_logins`` ビューと、データベースの表示に使用できる ``sys.databases`` ビューも用意されています。

> [AZURE.NOTE]``USE`` コマンドによるデータベースの切り替えはサポートされていません。ターゲット データベースに対して直接接続を確立します。

Microsoft Azure SQL Database では、オンプレミスの SQL Server インスタンスと同じ方法で、ユーザーとオブジェクトのデータベースレベル セキュリティを管理できます。それぞれのコマンドで使用できるパラメーターのみが異なります。詳細については、「[Azure SQL Database のセキュリティのガイドラインと制限事項](https://msdn.microsoft.com/library/azure/ff394108.aspx)」をご覧ください。

### ログインの管理

サーバー レベル プリンシパル ログインを使用して master データベースに接続し、ログインを管理します。``CREATE LOGIN``、``ALTER LOGIN``、``DROP LOGIN`` の各ステートメントを使用できます。次の例では、**login1** という名前のログインを作成します。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
```

> [AZURE.NOTE]ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

#### 新しいログインの使用

作成したログインを使用して Microsoft Azure SQL Database に接続するには、まず ``CREATE USER`` コマンドを使用して、各ログインにデータベースレベルの権限を付与する必要があります。詳細については、ログインに対するデータベースレベル権限の付与をご覧ください。

一部のツールでは表形式データ ストリーム (TDS) の実装方法が異なるため、接続文字列でログインに Azure SQL Database サーバー名を付加する必要があります。その際には、``<login>@<server>`` という表記を使用します。この場合は、``@`` 記号を使用してログインと Azure SQL Database サーバー名を区分します。たとえば、ログイン名が **login1** であり、Azure SQL Database サーバーの完全修飾名が **servername.database.windows.net** である場合、接続文字列の username パラメーターは ****login1@servername** にする必要があります。この制限により、ログイン名として選択できるテキストが限定されます。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」をご覧ください。

## ログインに対するサーバーレベル権限の付与

サーバーレベル プリンシパル以外のログインがサーバーレベルのセキュリティを管理するために、Microsoft Azure SQL Database では次の 2 つのセキュリティ ロールを用意しています。**loginmanager** はログイン作成用であり、**dbmanager** はデータベース作成用です。**master** データベースのユーザーのみ、これらのデータベース ロールに追加することができます。

> [AZURE.NOTE]ログインまたはデータベースを作成するには、(**master** の論理表現である) **master** データベースに接続する必要があります。

### loginmanager ロール

オンプレミスの SQL Server インスタンスの **securityadmin** 固定サーバー ロールと同様に、Microsoft Azure SQL Database の **loginmanager** データベース ロールにはログインを作成する権限があります。新しいログインを作成できるのは、サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) または **loginmanager** データベース ロールのメンバーのみです。

### dbmanager ロール

Microsoft Azure SQL Database の **dbmanager** データベース ロールは、オンプレミスの SQL Server インスタンスの **dbcreator** 固定サーバー ロールと似ています。データベースを作成できるのは、サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) または **dbmanager** データベース ロールのメンバーのみです。**dbmanager** データベース ロールのメンバーになったユーザーは、Azure SQL Database の ``CREATE DATABASE`` コマンドを使用してデータベースを作成できますが、このコマンドは master データベース内で実行する必要があります。詳細については、「[CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx)」をご覧ください。

### SQL Database のサーバーレベル ロールを割り当てる方法

データベースや他のログインを作成できるログインや関連するユーザーを作成するには、次の手順に従います。

1. サーバーレベル プリンシパル ログイン (プロビジョニング処理で作成されたもの) の資格情報、または **loginmanager** データベース ロールの既存のメンバーの資格情報を使用して **master** データベースに接続します。
2. ``CREATE LOGIN`` コマンドを使用して、ログインを作成します。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」をご覧ください。
3. ``CREATE USER`` コマンドを使用して、そのログインの新しいユーザーを master データベースに作成します。詳細については、「[CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」をご覧ください。
4. ストアド プロシージャ ``sp_addrolememeber`` を使用して、新しいユーザーを **dbmanager** データベース ロール、loginmanager データベース ロールのどちらか、またはその両方に追加します。

次のコード例は、ログイン (**login1**) と対応するデータベース ユーザー (**login1User**) を作成する方法を示しています。このユーザーは、**master** データベースへの接続中に、データベースまたは他のログインを作成することができます。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE USER login1User FROM LOGIN login1;
EXEC sp_addrolemember 'dbmanager', 'login1User';
EXEC sp_addrolemember 'loginmanager', 'login1User';
```

> [AZURE.NOTE]ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

## ログインのデータベース アクセスの許可

すべてのログインは master データベースに作成する必要があります。ログインが作成された後で、別のデータベースにそのログインのユーザー アカウントを作成できます。Microsoft Azure SQL Database では、オンプレミスの SQL Server インスタンスと同じ方法でデータベース ロールをサポートします。

別のデータベースにユーザー アカウントを作成するには (ログインまたはデータベースをまだ作成していないとします)、次の手順に従います。

1. **master** データベースに接続します (**loginmanager** ロールと **dbmanager** ロールを持つログインを使用します)。
2. ``CREATE LOGIN`` コマンドを使用して、新しいログインを作成します。詳細については、「[CREATE LOGIN (Transact-SQL)](https://msdn.microsoft.com/library/ms189751.aspx)」をご覧ください。Windows 認証はサポートされません。
3. ``CREATE DATABASE`` コマンドを使用して、新しいデータベースを作成します。詳細については、「[CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx)」をご覧ください。
4. 新しいデータベースとの接続を確立します (データベースの作成に使用したログインを使用します)。
5. ``CREATE USER`` コマンドを使用して、新しいデータベースに新しいユーザーを作成します。詳細については、「[CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)」をご覧ください。

次のコード例では、**login1** という名前のログインと、**database1** という名前のデータベースの作成方法を示します。

```
-- first, connect to the master database
CREATE LOGIN login1 WITH password='<ProvidePassword>';
CREATE DATABASE database1;
```

> [AZURE.NOTE]ログインを作成する際には、強力なパスワードを使用する必要があります。詳細については、「[強力なパスワード](https://msdn.microsoft.com/library/ms161962.aspx)」を参照してください。

次の例では、**database1** という名前のデータベースに、ログイン **login1** に対応する **login1User** という名前のデータベース ユーザーを作成する方法を示します。

```
-- Establish a new connection to the database1 database
CREATE USER login1User FROM LOGIN login1;
```

Microsoft Azure SQL Database におけるこのようなデータベースレベルの権限モデルは、オンプレミスの SQL Server インスタンスと同じです。詳細については、SQL Server オンライン ブックの次のトピックをご覧ください。

- [ログイン、ユーザー、およびスキーマの管理方法に関するトピック](https://msdn.microsoft.com/library/aa337552.aspx) 
- [レッスン 2: データベース オブジェクトに対する権限の構成](https://msdn.microsoft.com/library/ms365345.aspx) 

> [AZURE.NOTE]Transact-SQL のセキュリティ関連の Microsoft Azure SQL Database ステートメントでは、使用できるパラメーターが多少異なる場合があります。詳細については、「[Azure SQL Database Transact-SQL リファレンス](sql-database-transact-sql-information.md)」を参照してください。

## ログインとデータベースの表示

Azure SQL Database サーバーのログインとデータベースを表示するには、master データベースの ``sys.sql_logins`` ビューと ``sys.databases`` ビューをそれぞれ使用します。次の例では、Azure SQL Database サーバーのすべてのログインとデータベースの一覧を表示する方法を示します。

```
-- first, connect to the master database
SELECT * FROM sys.sql_logins;
SELECT * FROM sys.databases; 
```

## 関連項目

[Azure SQL Database のセキュリティのガイドラインと制限事項](sql-database-security-guidelines.md)

<!---HONumber=August15_HO9-->