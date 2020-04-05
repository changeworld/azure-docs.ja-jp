---
title: 認証
description: Azure Active Directory (AAD) または SQL Server 認証を使用して Azure Synapse Analytics に対して認証する方法について説明します。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: c998e3789a29d3cfeaf18a583913871f7edc5af1
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350721"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Azure Synapse Analytics に対する認証
Azure Active Directory (AAD) または SQL Server 認証を使用して Azure Synapse の SQL Analytics に対して認証する方法について説明します。

SQL プールに接続するには、認証のためにセキュリティ資格情報を渡す必要があります。 接続の確立時に、特定の接続の設定が、クエリ セッションの構築の一環として構成されます。  

セキュリティの詳細と、データ ウェアハウスへの接続を有効にする方法の詳細については、[データベースのセキュリティ保護](sql-data-warehouse-overview-manage-security.md)に関するドキュメントを参照してください。

## <a name="sql-authentication"></a>SQL 認証
SQL プールに接続するには、次の情報を指定する必要があります。

* 完全修飾サーバー名
* SQL 認証の指定
* ユーザー名
* Password
* 既定のデータベース (省略可)

既定では、ユーザー データベースではなく、*master* データベースに接続されます。 ユーザー データベースに接続するには、次の 2 つの操作のいずれかを選択します。

* SQL Server オブジェクト エクスプ ローラーで、SSDT、 SSMS またはアプリケーションの接続文字列を使用してサーバーを登録する際に、 既定のデータベースを指定する。 たとえば、ODBC 接続に InitialCatalog パラメーターを含めます。
* SSDT でセッションを作成する前に、ユーザー データベースを強調表示する。

> [!NOTE]
> 接続目的でデータベースを切り替えるための TRANSACT-SQL ステートメント **USE MyDatabase;** はサポートされていません。 SSDT を使用して SQL プールに接続する方法については、[Visual Studio を使用したクエリ](sql-data-warehouse-query-visual-studio.md)に関する記事を参照してください。
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) 認証
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) 認証は、Azure Active Directory (Azure AD) の ID を使用して SQL プールに接続するメカニズムです。 Azure Active Directory 認証を使用すると、データベース ユーザーの ID や他の Microsoft サービスを一元管理できます。 ID の一元管理では、1 か所で Azure Synapse ユーザーを管理できるようになるため、アクセス許可の管理が容易になります。 

### <a name="benefits"></a>メリット
Azure Active Directory には次のような利点があります。

* SQL Server 認証の代替方法が用意されています。
* データベース サーバー全体でユーザー ID が急増するのを防ぎます。
* 1 か所でのパスワードのローテーションを許可します。
* 外部の (AAD) グループを使用してデータベースのアクセス許可を管理できます。
* 統合 Windows 認証や、Azure Active Directory でサポートされる他の認証形式を有効にすると、パスワードが保存されません。
* 包含データベース ユーザーを使用して、データベース レベルで ID を認証します。
* SQL プールに接続するアプリケーション向けにトークンベース認証をサポートしています。
* [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md)、[SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json) などのさまざまなツールに対して、Active Directory ユニバーサル認証を介して Multi-Factor Authentication をサポートします。

> [!NOTE]
> Azure Active Directory はまだ比較的新しいため、制限がいくつかあります。 Azure Active Directory を環境に確実に適合させるには、「 [Azure AD の機能と制限事項](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)」を参照してください。
> 
> 

### <a name="configuration-steps"></a>構成の手順
Azure Active Directory 認証を構成するには、次の手順に従います。

1. Azure Active Directory を作成して設定する
2. 省略可能: Active Directory を関連付けるか、現在 Azure サブスクリプションに関連付けられている Active Directory を変更する
3. Azure Synapse の Azure Active Directory 管理者を作成する
4. クライアント コンピューターを構成する
5. Azure AD の ID にマップされている包含データベース ユーザーをデータベースに作成する
6. Azure AD ID を使用して SQL プールに接続する

現在、Azure Active Directory ユーザーは SSDT のオブジェクト エクスプローラーに表示されません。 回避策として、ユーザーを [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx) で表示してください。

### <a name="find-the-details"></a>詳細の確認
* Azure Active Directory 認証を構成および使用する手順は、Azure SQL Database と Azure Synapse の SQL Analytics とではほぼ同じです。 詳細な手順については、[Azure Active Directory 認証を使用して SQL Database または SQL プールに接続する](../../sql-database/sql-database-aad-authentication.md)方法に関する記事を参照してください。
* カスタム データベース ロールを作成し、ロールにユーザーを追加します。 その後、ロールに詳細なアクセス許可を付与します。 詳細については、「 [データベース エンジンの権限の概要](https://msdn.microsoft.com/library/mt667986.aspx)」をご覧ください。

## <a name="next-steps"></a>次のステップ
Visual Studio とその他のアプリケーションでクエリを開始するには、[Visual Studio を使用したクエリ](sql-data-warehouse-query-visual-studio.md)に関する記事を参照してください。
