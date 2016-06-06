<properties
   pageTitle="SQL Data Warehouse でのデータベースの保護 | Microsoft Azure"
   description="ソリューション開発のための Azure SQL Data Warehouse でのデータベース保護に関するヒント。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ronortloff"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/18/2016"
   ms.author="rortloff;barbkess;sonyama"/>

# SQL Data Warehouse でのデータベース保護

この記事では、Azure SQL Data Warehouse データベースの保護に関する基本事項を説明します。特にこの記事では、アクセスの制限、データの保護、データベースでのアクティビティの監視を行うためのリソースの概要を説明します。

## 接続のセキュリティ

接続のセキュリティとは、ファイアウォール ルールと接続の暗号化を使用して、データベースへの接続を制限し、保護する方法のことです。

ファイアウォール ルールはサーバーとデータベースの両方で使用され、明示的にホワイト リストに登録されていない IP アドレスからの接続試行を拒否します。アプリケーションまたはクライアント コンピューターのパブリック IP アドレスからの接続を許可するには、まず Azure クラシック ポータル、REST API、または PowerShell を使用して、サーバーレベルのファイアウォール ルールを作成する必要があります。ベスト プラクティスとして、可能な限りサーバーのファイアウォールにより許可される IP アドレスの範囲を制限する必要があります。ローカル コンピューターから Azure SQL Data Warehouse にアクセスするには、ネットワークとローカル コンピューターのファイアウォールで、TCP ポート 1433 での送信方向の通信が許可されていることを確認します。詳細については、「[Azure SQL Database ファイアウォール][]」を参照してください。

SQL Data Warehouse への接続は、接続文字列で暗号化モードを設定することで暗号化できます。接続の暗号化を有効にする構文は、プロトコルによって異なります。接続文字列を設定するには、Azure ポータルでデータベースに移動します。*[要点]* で *[データベース接続文字列の表示]* をクリックします。


## 認証

認証とは、データベースへの接続時に ID を証明する方法のことです。SQL Data Warehouse では現在、ユーザー名とパスワードを使用した SQL Server 認証と、Azure Active Directory のプレビューがサポートされています。

データベースの論理サーバーを作成したときに、ユーザー名とパスワードによる "サーバー管理" ログインを指定したとします。これらの資格情報を使用すると、データベース所有者、つまり "dbo" として、そのサーバーにある任意のデータベースを SQL Server 認証を通して認証できます。

ただし、ベスト プラクティスとして、組織のユーザーは別のアカウントを使用して認証する必要があります。この方法により、アプリケーションに付与されるアクセス許可を制限でき、アプリケーション コードが SQL インジェクション攻撃に対して脆弱な場合に、悪意のあるアクティビティのリスクを軽減できます。

SQL Server 認証済みユーザーを作成するには、サーバーの**マスター** データベースにサーバー管理ログインを使用して接続し、新しいサーバー ログインを作成します。

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'strong_password';

```

次に、サーバー管理者ログインを使用して **SQL Data Warehouse データベース**に接続し、先ほど作成したサーバー ログインに基づいてデータベース ユーザーを作成します。

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;

```

SQL Database の認証の詳細については、「[Azure SQL Database におけるデータベース、ログイン、およびユーザーの管理][]」を参照してください。SQL Data Warehouse の Azure AD プレビューの使用に関する詳細については、「[Azure Active Directory 認証を使用して SQL Data Warehouse に接続する][]」を参照してください。


## 承認

承認とは、Azure SQL Data Warehouse データベース内で実行できる事柄を指し、これはアカウントのロール メンバーシップと権限によって制御されます。ベスト プラクティスとして、必要最低限の特権をユーザーに付与することをお勧めします。Azure SQL Data Warehouse により、T-SQL 内のロールで簡単に管理できるようになります。

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

接続しているサーバー管理者のアカウントは db\_owner のメンバーであり、データベース内ですべての操作を実行する権限を持ちます。スキーマのアップグレードやその他の管理操作をデプロイするために、このアカウントを保存します。アクセス許可が限定された "ApplicationUser" アカウントを使用して、アプリケーションで必要な最小限の特権により、アプリケーションをデータベースに接続します。

ユーザーが Azure SQL Database で実行できる操作をさらに制限する方法がいくつかあります。

- 詳細な[アクセス許可][]により、個々の列、テーブル、ビュー、プロシージャ、その他のデータベース内のオブジェクトで実行できる操作を制御できます。詳細なアクセス許可を使用して最大限の制御を行い、必要最小限のアクセス許可を付与します。詳細なアクセス許可システムは、多少複雑なため、効率的に使用するには調査が必要になります。
- db\_datareader と db\_datawriter 以外の[データベース ロール][]を使用して、より権限の大きなアプリケーション ユーザー アカウント、またはより権限の小さな管理アカウントを作成できます。組み込みの固定データベース ロールを使用すると、簡単にアクセス許可を付与できますが、その結果、必要以上のアクセス許可を付与することになる可能性があります。
- [ストアド プロシージャ][]を使用すると、データベースで実行できるアクションを制限できます。

Azure クラシック ポータルまたは Azure リソース マネージャー API を使用したデータベースと論理サーバーの管理は、ポータル ユーザー アカウントのロールの割り当てによって制御されます。このトピックの詳細については、「[Azure ポータルでのロール ベースのアクセス制御][]」を参照してください。

## 暗号化

Azure SQL Data Warehouse では、[透過的なデータ暗号化][]を使用して、データが "静止" 状態のとき、またはデータベース ファイルやバックアップに格納されているときに、そのデータを暗号化することでデータを保護できます。TDE を有効にするには、管理者か、master データベース内の dbmanager ロールのメンバーである必要があります。データベースを暗号化するには、サーバーの master データベースに接続して以下を実行します。


```sql

ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;

```

透過的なデータ暗号化は、[Azure ポータル][]のデータベース設定から有効にすることもできます。詳細については、「[Transparent Data Encryption (TDE) の概要][]」をご覧ください。

## 監査

データベースの監査イベントと追跡イベントは、規制遵守の維持や、疑わしいアクティビティの特定に役立ちます。SQL Data Warehouse の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。また SQL Data Warehouse の監査を Microsoft Power BI と統合することにより、詳細なレポートと分析が容易になります。詳細については、「[SQL Database 監査の使用][]」を参照してください。

## 次のステップ
さまざまなプロトコルでの SQL Data Warehouse への接続の詳細と例については、「[SQL Data Warehouse への接続][]」をご覧ください。

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse への接続]: ./sql-data-warehouse-develop-connections.md
[SQL Database 監査の使用]: ./sql-data-warehouse-overview-auditing.md
[Transparent Data Encryption (TDE) の概要]: ./sql-data-warehouse-encryption-tde.md
[Azure Active Directory 認証を使用して SQL Data Warehouse に接続する]: ./sql-data-warehouse-aad-authentication.md

<!--MSDN references-->
[Azure SQL Database ファイアウォール]: https://msdn.microsoft.com/library/ee621782.aspx
[データベース ロール]: https://msdn.microsoft.com/library/ms189121.aspx
[Azure SQL Database におけるデータベース、ログイン、およびユーザーの管理]: https://msdn.microsoft.com/library/ee336235.aspx
[アクセス許可]: https://msdn.microsoft.com/library/ms191291.aspx
[ストアド プロシージャ]: https://msdn.microsoft.com/library/ms190782.aspx
[透過的なデータ暗号化]: https://go.microsoft.com/fwlink/?LinkId=526242
[Azure ポータル]: https://portal.azure.com/

<!--Other Web references-->
[Azure ポータルでのロール ベースのアクセス制御]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure

<!---HONumber=AcomDC_0525_2016-->