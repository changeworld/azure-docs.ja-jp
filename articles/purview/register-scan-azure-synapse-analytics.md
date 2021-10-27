---
title: 専用 SQL プールをスキャンする方法
description: この攻略ガイドでは、Azure Purview 内で専用 SQL プールをスキャンする方法の詳細について説明します。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 05/08/2021
ms.openlocfilehash: 507ea92f6aa50d4d1441874e8dc62bbb06621aec
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047401"
---
# <a name="register-and-scan-dedicated-sql-pools-formerly-sql-dw"></a>専用 SQL プール (以前の SQL DW) の登録とスキャン

> [!NOTE]
> Synapse ワークスペース内で専用 SQL データベースを登録およびスキャンする場合は、[こちら](register-scan-synapse-workspace.md)の手順に従う必要があります。

この記事では、Purview で専用 SQL プール (以前の SQL DW) のインスタンスを登録およびスキャンする方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

専用 SQL プール (旧称 SQL DW) では、メタデータとスキーマをキャプチャするためにフルおよび増分スキャンがサポートされています。 また、スキャンでは、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

### <a name="known-limitations"></a>既知の制限事項

> * Azure Purview の [スキーマ] タブでは 300 を超える列がサポートされておらず、"Additional-Columns-Truncated (その他の列は切り詰められています)" と表示されます。 

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。
- Azure Purview データ ソース管理者である必要があります
- Purview アカウントと Azure Synapse Analytics の専用 SQL プールの間のネットワーク アクセス。
 
## <a name="setting-up-authentication-for-a-scan"></a>スキャンの認証の設定

認証を設定するには、次の 3 つの方法があります。

- マネージド ID
- SQL 認証
- サービス プリンシパル

    > [!Note]
    > サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可が付与されるまで 15 分ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

### <a name="managed-identity-recommended"></a>マネージド ID (推奨) 
   
Purview アカウントには独自のマネージド ID があり、これは基本的に作成時の Purview の名前になっています。 「[Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](../azure-sql/database/authentication-aad-service-principal-tutorial.md)」の前提条件とチュートリアルに従い、Purview の正確なマネージド ID 名を使用して 専用 SQL プール (旧称 SQL DW) に Azure AD ユーザーを作成する必要があります。

ユーザーを作成してアクセス許可を付与する SQL 構文の例:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_datareader', [PurviewManagedIdentity]
GO
```

この認証には、データベース、スキーマ、テーブルのメタデータを取得するためのアクセス許可が必要です。 また、分類用のサンプリングを行うために、テーブルに対してクエリを実行できる必要もあります。 `db_datareader` アクセス許可を ID に割り当てることをお勧めします。

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
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定]、[シークレット]** の順に選択します
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します 

#### <a name="granting-the-service-principal-access"></a>サービス プリンシパルへのアクセスの付与

さらに、「[Azure AD アプリケーションを使用して Azure AD ユーザーを作成する](../azure-sql/database/authentication-aad-service-principal-tutorial.md)」の前提条件とチュートリアルに従って、専用 SQL プールに Azure AD ユーザーを作成する必要もあります。 ユーザーを作成してアクセス許可を付与する SQL 構文の例:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

ALTER ROLE db_datareader ADD MEMBER [ServicePrincipalName]
GO
```

> [!Note]
> Purview でスキャンを行うには、**アプリケーション (クライアント) ID** と **クライアント シークレット** が必要になります。

### <a name="sql-authentication"></a>SQL 認証

まだお持ちでない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1)」の手順に従って、専用 SQL プール (旧称 SQL DW) のログインを作成できます。

選択した認証方法が **SQL 認証** の場合は、パスワードを取得して、キー コンテナーに格納する必要があります。

1. SQL ログインのパスワードを取得します
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、SQL ログイン の "*パスワード*" として **名前** と **値** を入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

## <a name="register-a-sql-dedicated-pool-formerly-sql-dw"></a>SQL 専用プール (以前の SQL DW) を登録する

Purview で新しい SQL 専用プールを登録するには、次の手順を実行します。

1. ご自分の Purview アカウントに移動します。
1. 左側のナビゲーションで **[Data Map]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、**[Azure SQL 専用プール (以前の SQL DW)]** を選択します。
1. **[続行]** を選択します

**[ソースの登録]** 画面で、次のようにします。

1. データ ソースがカタログに表示されるときの **名前** を入力します。
2. Azure サブスクリプションを選択して、専用 SQL プールの検索結果を絞り込みます。
3. 専用 SQL プールを選択します。
4. コレクションを選択するか、新しいものを作成します (省略可能)。
5. **[登録]** を選択してデータ ソースを登録します。

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した SQL 専用プールのソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/sql-dedicated-pool-set-up-scan.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のテーブルに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/select-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-synapse-analytics/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
