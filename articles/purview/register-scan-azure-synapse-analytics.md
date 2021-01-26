---
title: Azure Synapse Analytics をスキャンする方法
description: この攻略ガイドでは、Azure Synapse Analytics をスキャンする方法について詳しく説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 3ba43b83166b5548dee4ea4e52c7411db48d23f5
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567265"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Azure Synapse Analytics の登録とスキャン

この記事では、Purview で Azure Synapse Analytics (旧称 SQL DW) のインスタンスを登録およびスキャンする方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Synapse Analytics (旧称 SQL DW) では、メタデータとスキーマをキャプチャするためにフルおよび増分スキャンがサポートされています。 また、スキャンでは、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

### <a name="known-limitations"></a>既知の制限事項

Azure Purview では、Azure Synapse Analytics の[ビュー](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true)のスキャンはサポートされません。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。
- Azure Purview データ ソース管理者である必要があります
- Purview アカウントと Azure Synapse Analytics 間のネットワーク アクセス。
 
## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

Azure Synapse Analytics の認証を設定するには、次の 3 つの方法があります。

- マネージド ID
- SQL 認証
- サービス プリンシパル

    > [!Note]
    > サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可が付与されるまで 15 分ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

### <a name="managed-identity-recommended"></a>マネージド ID (推奨) 
   
Purview アカウントには独自のマネージド ID があり、これは基本的に作成時の Purview の名前になっています。 「[Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](/azure/azure-sql/database/authentication-aad-service-principal-tutorial)」の前提条件とチュートリアルに従い、Purview の正確なマネージド ID 名を使用して Azure Synapse Analytics (旧称 SQL DW) に Azure AD ユーザーを作成する必要があります。

ユーザーを作成してアクセス許可を付与する SQL 構文の例:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

この認証には、データベース、スキーマ、テーブルのメタデータを取得するためのアクセス許可が必要です。 また、分類用のサンプリングを行うために、テーブルに対してクエリを実行できる必要もあります。 `db_owner` アクセス許可を ID に割り当てることをお勧めします。

### <a name="service-principal"></a>サービス プリンシパル

スキャンにサービス プリンシパルの認証を使用するには、既存のものを使用するか、新しく作成することができます。 

> [!Note]
> 新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。
> 1. [Azure Portal](https://portal.azure.com) に移動します。
> 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
> 1. **[アプリの登録]** を選択します。
> 1. **[+ 新しいアプリケーションの登録]** を選択します。
> 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
> 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
> 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
> 1. 次に、 **[登録]** を選択します。

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) でサービス プリンシパルに移動します
1. **[アプリケーション (クライアント) ID]** の値を **[概要]** から、および **[クライアント シークレット]** の値を **[証明書とシークレット]** からコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>サービス プリンシパルに Azure Synapse Analytics (旧称 SQL DW) へのアクセス権を付与する

さらに、「[Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial)」の前提条件とチュートリアルに従って、Azure Synapse Analytics に Azure AD ユーザーを作成する必要もあります。 ユーザーを作成してアクセス許可を付与する SQL 構文の例:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Purview でスキャンを行うには、**アプリケーション (クライアント) ID** と **クライアント シークレット** が必要になります。

### <a name="sql-authentication"></a>SQL 認証

まだお持ちでない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1)」の手順に従って、Azure Synapse Analytics (旧称 SQL DW) のログインを作成できます。

選択した認証方法が **SQL 認証** の場合は、パスワードを取得して、キー コンテナーに格納する必要があります。

1. SQL ログインのパスワードを取得します
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、SQL ログイン の "*パスワード*" として **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Azure Synapse Analytics (旧称 SQL DW) インスタンスの登録

新しい Azure Synapse Analytics サーバーをデータ カタログに登録するには、次の操作を行います。

1. Purview アカウントに移動します。
1. 左側のナビゲーションで **[ソース]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Synapse Analytics (旧称 SQL DW)]** を選択します
1. **[続行]** を選択します。

**[ソースの登録 (Azure Synapse Analytics)]** 画面で、次の操作を行います。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
1. 目的のストレージ アカウントを指し示す方法を選択します。
   1. **[Azure サブスクリプションから]** を選択して、 **[Azure サブスクリプション]** ドロップダウン ボックスから適切なサブスクリプションを選択し、 **[サーバー名]** ドロップダウン ボックスから適切なサーバーを選択します。
   1. または、 **[手動で入力]** を選択して、**サーバー名** を入力することもできます。
1. **[完了]** を選択して、データ ソースを登録します。

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)

