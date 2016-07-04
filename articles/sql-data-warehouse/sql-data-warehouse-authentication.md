<properties
   pageTitle="Azure SQL Data Warehouse への認証 | Microsoft Azure"
   description="Azure SQL Data Warehouse への Azure Active Directory (AAD) および SQL Server 認証。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="byham"
   manager="barbkess"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="06/17/2016"
   ms.author="rickbyh;barbkess;sonyama"/>

# Azure SQL Data Warehouse への認証

> [AZURE.SELECTOR]
- [概要](sql-data-warehouse-connect-overview.md)
- [認証](sql-data-warehouse-authentication.md)
- [ドライバー](sql-data-warehouse-connection-strings.md)

SQL Data Warehouse に接続するには、認証のためのセキュリティ資格情報を渡す必要があります。接続の確立時に、特定の接続の設定が、クエリ セッションの構築の一環として構成されているのがわかります。

セキュリティの詳細、および SQL Data Warehouse への接続を有効にする方法の詳細については、「[SQL Data Warehouse でのデータベース保護][]」をご覧ください。

## SQL 認証
SQL Data Warehouse に接続するには、次の情報を提供する必要があります。

- 完全修飾サーバー名
- SQL 認証の指定
- ユーザー名
- パスワード
- 既定のデータベース (省略可)

ユーザーの認証には SQL 認証を使用しなければならないことに注意してください。現在、信頼済みの認証はサポートされていません。

既定では、ユーザー データベースではなく、マスター データベースに接続されます。ユーザー データベースに接続するには、次の 2 つの操作のいずれかを選択します。

1. SQL Server オブジェクト エクスプ ローラーで、SSDT または アプリケーションの接続文字列を使用してサーバーを登録する際に、 既定のデータベースを指定する。たとえば、ODBC 接続に InitialCatalog パラメーターを含めます。
2. SSDT でセッションを作成する前に、まずユーザー データベースを強調表示する。

> [AZURE.NOTE] SSDT を使用した SQL Data Warehouse への接続に関するガイダンスは、[Visual Studio を使用したクエリ][]についての記事をご覧ください。

重要事項を繰り返しますが、接続目的でデータベースを切り替えるための TRANSACT-SQL ステートメント **USE <your DB>** はサポートされていません。


## Azure Active Directory (AAD) 認証

Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Data Warehouse に接続するメカニズムです。Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。ID の一元管理では、1 か所で SQL Data Warehouse ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。

### メリット

次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- データベース サーバー全体でユーザー ID が急増するのを防ぎます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- Azure Active Directory 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- Azure Active Directory では、SQL Data Warehouse に接続するアプリケーション向けにトークンベース認証をサポートしています。

> [AZURE.IMPORTANT] Azure Active Directory 認証はプレビュー機能で、ライセンス契約 (例: Enterprise Agreement、Microsoft Azure 契約、マイクロソフト オンライン サブスクリプション契約) に定められたプレビューの規定に従うものとします。また、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)が適用される場合があります。

### 構成の手順

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure Active Directory を作成して設定する
2. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
3. Azure SQL Data Warehouse 用に Azure Active Directory 管理者を作成する
4. クライアント コンピューターを構成する
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
6. Azure AD の ID を使用してデータ ウェアハウスに接続する

Azure Active Directory 認証の使用して Azure SQL Data Warehouse に接続する場合、Azure SQL Database と主に違うのは、SQL Server Management Studio ではなく SQL Server Data Tools を使用しする点です。SQL Data Warehouse には、April 2016 (バージョン 14.0.60311.1) 以降の SQL Server Data Tools for Visual Studio 2015 が必要です。現在、Azure Active Directory ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。回避策として、ユーザーは [sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx) で見てください。
  
### 詳細の確認
- 詳細な手順を実行します。Azure Active Directory 認証を構成および使用する詳細な手順は、Azure SQL Database と Azure SQL Data Warehouse ではほぼ同じです。詳細な手順については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](../sql-database/sql-database-aad-authentication.md)」をご覧ください。
- カスタム データベース ロールを作成し、ロールにユーザーを追加します。その後、ロールに詳細なアクセス許可を付与します。詳細については、「[データベース エンジンの権限の概要](https://msdn.microsoft.com/library/mt667986.aspx)」をご覧ください。

## 次のステップ

Visual Studio またはその他のアプリケーションを使用してデータ ウェアハウスに対するクエリを開始するには、[Visual Studio を使用したクエリ][]に関するページをご覧ください。

<!-- Article references -->
[SQL Data Warehouse でのデータベース保護]: sql-data-warehouse-overview-manage-security.md
[Visual Studio を使用したクエリ]: sql-data-warehouse-query-visual-studio.md

<!---HONumber=AcomDC_0622_2016-->