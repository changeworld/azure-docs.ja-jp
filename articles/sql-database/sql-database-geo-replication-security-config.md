<properties
	pageTitle="データベースを新しいサーバーに復元した後、またはセカンダリ データベースのコピーにデータベースをフェールオーバーした後にセキュリティを管理する方法 |Microsoft Azure"
	description="このトピックでは、データベースの復元またはフェールオーバー後にセキュリティを管理する際のセキュリティに関する考慮事項について説明します。"
	services="sql-database"
	documentationCenter="na"
	authors="carlrabeler"
	manager="jhubbard"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="06/16/2016"
	ms.author="carlrab" />

# 障害復旧後にセキュリティを管理する方法

>[AZURE.NOTE] [Active Geo-Replication]すべてのサービス階層のデータベースで (sql-database-geo-replication-overview.md) を使用できるようになりました。

## 障害復旧の認証要件の概要

このトピックでは、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)を構成し、制御するための認証要件と、セカンダリ データベースへのユーザー アクセスを設定するために必要な手順について説明します。また、geo リストア使用後に復旧されたデータベースへのアクセスを有効にする方法についても説明します。復旧オプションの詳細については、「[Azure SQL Database を障害から回復する](sql-database-disaster-recovery.md)」を参照してください。

## 包含ユーザーによる障害復旧

従来のユーザーは master データベース内のログインにマップする必要がありましたが、包含ユーザーは、データベース自体で完全に管理されます。これには 2 つ利点があります。障害復旧のシナリオでは、データベースがユーザーを管理するため、ユーザーは追加の構成なしで、新しいプライマリ データベースまたは geo リストアを使用して復旧されたデータベースに引き続き接続できます。また、ログインの観点からは、この構成を使用することで、スケーラビリティとパフォーマンスを向上できる可能性があります。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

主なトレードオフは、大規模な障害復旧プロセスの管理が困難になることです。同じログインを使用するデータベースが複数ある場合、複数のデータベースで包含ユーザーを使用して資格情報を維持すると、包含ユーザーの利点が損なわれる場合があります。たとえば、パスワード ローテーション ポリシーでは、マスター データベースで 1 回だけログイン用パスワードを変更するのではなく、複数のデータベースで一貫して変更を行う必要があります。このような理由から、同じユーザー名とパスワードを使用するデータベースが複数ある場合、包含ユーザーの使用は推奨されません。

## ログインとユーザーを構成する方法

包含ユーザーではなくログインとユーザーを使用している場合は、追加の手順を実行して、マスター データベースに同じログインが存在することを確認する必要があります。次のセクションでは、関連する手順とその他の考慮事項について概要を説明します。

### セカンダリ データベースまたは復旧されたデータベースへのユーザー アクセスの設定

セカンダリ データベースを読み取り専用のセカンダリ データベースとして使用できるようにし、新しいプライマリ データベースまたは geo リストアを使用して復旧されたデータベースへの適切なアクセスを保証するには、復旧の前に、ターゲット サーバーのマスター データベースに適切なセキュリティ構成を設定する必要があります。

各手順の詳細なアクセス許可については、後ほど詳しく説明します。

geo レプリケーション セカンダリに対するユーザー アクセスの準備は、geo レプリケーションの構成の一部として実行する必要があります。geo リストアされたデータベースへのユーザー アクセスの準備は、元のサーバーがオンラインになっているときに実行する必要があります (例: 障害復旧訓練の一部として)。

>[AZURE.NOTE] ログイン アクセスが適切に構成されていないサーバーにフェールオーバーまたは geo リストアする場合は、サーバー管理者アカウントに制限されます。

ターゲット サーバーでのログインの設定には、以下に示す 3 つの手順が含まれます。

#### 1\.プライマリ データベースにアクセスできるログインを特定する:
このプロセスの最初の手順は、ターゲット サーバー上に複製するログインを特定することです。そのために、ソース サーバーの論理 master データベースとプライマリ データベースでそれぞれ 1 回ずつ SELECT ステートメントを実行します。

サーバー管理者または **LoginManager** サーバー ロールのメンバーのみが、次の SELECT ステートメントを使用してソース サーバーのログインを特定できます。

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

また、db\_owner データベース ロールのメンバー、dbo ユーザー、またはサーバー管理者のみが、プライマリ データベース内にあるすべてのデータベース ユーザー プリンシパルを特定できます。

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\.手順 1. で特定したログインの SID を検索する:
前のセクションで実行したクエリの出力を比較して SID を一致させることで、サーバー ログインをデータベース ユーザーに対応付けることができます。一致する SID を持つデータベース ユーザーが対応付けられたログインは、そのデータベースのユーザー プリンシパルとしてデータベースに対するユーザー アクセス権が付与されます。

次のクエリを使用すると、データベース内のすべてのユーザー プリンシパルとその SID を表示できます。このクエリを実行できるのは、db\_owner データベース ロールのメンバーまたはサーバー管理者のみです。

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE] **INFORMATION\_SCHEMA** ユーザーと **sys** ユーザーの SID は *NULL* で、**ゲスト** SID は **0x00** です。データベースの作成者が **DbManager** のメンバーではなくサーバー管理者だった場合、**dbo** SID は *0x01060000000001648000000000048454* で始まることがあります。

#### 3\.ターゲット サーバーへのログインを作成する:
最後の手順では、1 つ以上のターゲット サーバーにアクセスし、適切な SID を使用してログインを生成します。基本的な構文は次のとおりです。

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE] セカンダリへのユーザー アクセスを許可し、プライマリへのアクセスは許可しない場合は、次の構文を使用してプライマリ サーバーでユーザー ログインを変更します。
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE を実行してもパスワードは変更されないため、必要な場合にいつでも有効にできます。

## 次のステップ

- データベースへのアクセスとログインの管理の詳細については、「[SQL Database のセキュリティ: データベースのアクセスとログインのセキュリティの管理](sql-database-manage-logins.md)」を参照してください。
- 包含データベース ユーザーの詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。
- アクティブ geo レプリケーションの使用および構成の方法については、「[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)」をご覧ください。
- geo リストアの使用方法については、「[geo リストア](sql-database-geo-restore.md)」をご覧ください。

## その他のリソース

- [概要: SQL Database を使用したクラウド ビジネス継続性とデータベース障害復旧](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (概要: SQL Database のポイントインタイム リストア)](sql-database-point-in-time-restore.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [geo レプリケーションのセキュリティ構成](sql-database-geo-replication-security-config.md)
- [SQL Database BCDR の FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->