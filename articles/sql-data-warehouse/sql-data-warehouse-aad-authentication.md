<properties
   pageTitle="Azure Active Directory 認証を使用して SQL Data Warehouse に接続する | Microsoft Azure"
   description="Azure Active Directory 認証を使用して SQL Data Warehouse に接続する方法について説明します。"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="05/11/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure Active Directory 認証を使用して SQL Data Warehouse に接続する


Azure Active Directory 認証は、Azure Active Directory (Azure AD) の ID を使用して Microsoft Azure SQL Data Warehouse に接続するメカニズムです。Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。ID の一元管理では、1 か所で SQL Data Warehouse ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。

## メリット

次のような利点があります。

- SQL Server 認証の代替方法が用意されています。
- データベース サーバー全体でユーザー ID が急増するのを防ぎます。
- 1 か所でのパスワードのローテーションを許可します。
- 顧客は外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
- 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすることで、パスワードが保存されないようにすることができます。
- Azure Active Directory 認証では、包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
- Azure Active Directory では、SQL Data Warehouse に接続するアプリケーション向けにトークンベース認証をサポートしています。

> [AZURE.IMPORTANT] Azure Active Directory 認証はプレビュー機能で、ライセンス契約 (例: Enterprise Agreement、Microsoft Azure 契約、マイクロソフト オンライン サブスクリプション契約) に定められたプレビューの規定に従うものとします。また、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)が適用される場合があります。

## 構成の手順

構成の手順には、Azure Active Directory 認証を構成して使用する次の手順が含まれます。

1. Azure Active Directory を作成して設定する
2. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
3. Azure SQL Data Warehouse 用に Azure Active Directory 管理者を作成する
4. クライアント コンピューターを構成する
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
6. Azure AD の ID を使用してデータ ウェアハウスに接続する

Azure Active Directory 認証の使用して Azure SQL Data Warehouse に接続する場合、Azure SQL Database と主に違うのは、SQL Server Management Studio ではなく SQL Server Data Tools を使用しする点です。SQL Data Warehouse には、April 2016 (バージョン 14.0.60311.1) 以降の SQL Server Data Tools for Visual Studio 2015 が必要です。現在、Azure Active Directory ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。代替策として、[sys.database\_principals](https://msdn.microsoft.com/library/ms187328.aspx) でユーザーを表示してください。
  
## 次のステップ 
- 詳細な手順を実行します。Azure Active Directory 認証を構成および使用する詳細な手順は、Azure SQL Database と Azure SQL Data Warehouse ではほぼ同じです。詳細な手順については、[Azure Active Directory 認証を使用した SQL Database または SQL Data Warehouse への接続](../sql-database/sql-database-aad-authentication.md)に関するページをご覧ください。
- カスタム データベース ロールを作成し、ロールにユーザーを追加します。その後、ロールに詳細なアクセス許可を付与します。詳細については、「[Getting Started with Database Engine Permissions](https://msdn.microsoft.com/library/mt667986.aspx)」(データベースエンジンのアクセス許可の概要) を参照してください。

<!---HONumber=AcomDC_0525_2016-->