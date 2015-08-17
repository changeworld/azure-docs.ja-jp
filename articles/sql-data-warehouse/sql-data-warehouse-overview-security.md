<properties
   pageTitle="SQL Data Warehouse でのデータベースの保護 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのデータベース保護に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# SQL Data Warehouse でのデータベース保護

この記事では、Azure SQL Data Warehouse データベースの保護に関する基本事項を説明します。特にこの記事では、アクセスの制限、データの保護、およびデータベースでのアクティビティの監視を行うためのリソースの概要を説明します。

## 接続のセキュリティ

接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。新しいデータベースへの接続を試行するために、アプリケーションまたはクライアント コンピューターのパブリック IP アドレスを許可するには、まず Azure の管理ポータル、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。詳細については、「[Azure SQL データベース ファイアウォール][]」を参照してください。


## 認証

認証とは、データベースへの接続時に ID を証明する方法のことです。SQL Data Warehouse では現在、ユーザー名とパスワードを使用した SQL 認証がサポートされています。

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを認証できます。

ただし、組織のユーザーは認証に別のアカウントを使用することをお勧めします。このようにすることで、アプリケーションに付与される権限を制限し、アプリケーション コードが SQL インジェクション攻撃に対して脆弱である場合に、悪意のあるアクティビティによるリスクを低減できます。包含データベース ユーザーを作成する方法をお勧めします。この方法により、ユーザー名とパスワードを使用して、アプリケーションから単一のデータベースに直接認証できます。サーバー管理ログインでユーザー データベースに接続しているときに次の T-SQL を実行すると、包含データベース ユーザーを作成できます。

```
CREATE USER ApplicationUser WITH PASSWORD = 'strong_password';
```

SQL Database の認証の詳細については、「[Azure SQL データベースにおけるデータベース、ログイン、およびユーザーの管理][]」を参照してください。


## 承認

承認とは、Azure SQL Data Warehouse データベース内で実行できる事柄を指し、これはアカウントのロール メンバーシップと権限によって制御されます。ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。Azure SQL Data Warehouse により、T-SQL 内のロールで簡単に管理できるようになります。

```
ALTER ROLE db_datareader ADD MEMBER ApplicationUser; -- allows ApplicationUser to read data
ALTER ROLE db_datawriter ADD MEMBER ApplicationUser; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db\_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

Azure SQL Database でユーザーが実行できる事柄をさらに限定する方法がいくつかあります。- db\_datareader や db\_datawriter 以外の[データベース ロール][]を使用して、より強力なアプリケーション ユーザー アカウントを作成したり、それほど強力ではない管理アカウントを作成したりすることができます。細分化された[権限][]を使用して、個々の列、テーブル、ビュー、プロシージャ、およびデータベース内のその他のオブジェクトで実行できる操作を管理できます。 - [ストアド プロシージャ][]を使用して、データベースで実行できるアクションを制限できます。

Azure の管理ポータルまたは Azure リソース マネージャー API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。このトピックの詳細については、「[Azure プレビュー ポータルでのロール ベースのアクセス制御][]」を参照してください。


## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[開発の概要]: sql-data-warehouse-overview-develop.md


<!--MSDN references-->
[Azure SQL データベース ファイアウォール]: https://msdn.microsoft.com/library/ee621782.aspx
[データベース ロール]: https://msdn.microsoft.com/library/ms189121.aspx
[Azure SQL データベースにおけるデータベース、ログイン、およびユーザーの管理]: https://msdn.microsoft.com/library/ee336235.aspx
[権限]: https://msdn.microsoft.com/library/ms191291.aspx
[ストアド プロシージャ]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: http://go.microsoft.com/fwlink/?linkid=526242&clcid=0x409

<!--Other Web references-->
[Azure プレビュー ポータルでのロール ベースのアクセス制御]: http://azure.microsoft.com/documentation/articles/role-based-access-control-configure.aspx

<!---HONumber=August15_HO6-->