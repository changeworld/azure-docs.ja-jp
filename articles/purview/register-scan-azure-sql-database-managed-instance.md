---
title: Azure SQL Database Managed Instance に接続して管理する
description: このガイドでは、Azure Purview で Azure SQL Database Managed Instance に接続し、Azure SQL Database Managed Instance ソースをスキャンおよび管理する方法について説明します。
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 0f1b570ce00a371a4f30e890eed771fa15c22174
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841868"
---
# <a name="connect-to-and-manage-an-azure-sql-database-managed-instance-in-azure-purview"></a>Azure Purview で Azure SQL Database Managed Instance に接続して管理する

この記事では、Azure Purview で Azure SQL Database Managed Instance を登録する方法と、Azure SQL Database Managed Instance を認証および操作する方法の概要を説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)| [あり](#scan) | [あり](#scan) | [あり](#scan) | いいえ | いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可に関するページ](catalog-permissions.md)を参照してください。

* [Azure SQL マネージド インスタンスのパブリック エンドポイントを構成する](../azure-sql/managed-instance/public-endpoint-configure.md)

<<<<<<< HEAD
- [Azure SQL Managed Instance のパブリック エンドポイントを構成する](../azure-sql/managed-instance/public-endpoint-configure.md)
=======
>>>>>>> repo_sync_working_branch
    > [!Note]
    > Azure Purview インジェスト プライベート エンドポイントとセルフホステッド統合ランタイム VM を使用してプライベート エンドポイントで構成されている、Azure SQL Database Managed Instances のスキャンがサポートされるようになりました。
    > 前提条件に関する詳細については、「[プライベートかつ安全な Azure Purview への接続とデータ ソースのスキャン](./catalog-private-link-end-to-end.md)」を参照してください

## <a name="register"></a>登録

このセクションでは、Azure Purview で [Purview Studio](https://web.purview.azure.com/) を使用して Azure SQL Database Managed Instance を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録のための認証

新しい認証を作成する必要がある場合は、[SQL Database Managed Instance へのデータベース アクセスを承認する](../azure-sql/database/logins-create-manage.md)必要があります。 現在、Purview では、3 つの認証方法がサポートされています。

- [Managed Identity](#managed-identity-to-register)
- [サービス プリンシパル](#service-principal-to-register)
- [SQL 認証](#sql-authentication-to-register)

#### <a name="managed-identity-to-register"></a>登録するマネージド ID

Purview マネージド ID は、他のユーザー、グループ、またはサービス プリンシパルと同様の、認証するために使用できる Purview アカウントの ID です。

自分のマネージド ID オブジェクト ID を確認するには、Azure portal で次の手順を実行します。

1. Azure portal を開き、Purview アカウントに移動します。
1. 左側のメニューで **[プロパティ]** タブを選択します。
1. **[管理 ID オブジェクト ID]** の値を選択し、コピーします。

マネージド ID には、データベース、スキーマ、およびテーブルのメタデータを取得したり、分類のためにテーブルを照会したりするためのアクセス許可が必要になります。
- 「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)」の前提条件とチュートリアルに従って、Azure SQL Database Managed Instance に Azure AD ユーザーを作成する
- ID に `db_datareader` アクセス許可を割り当てます。

#### <a name="service-principal-to-register"></a>登録するサービス プリンシパル

Purview でサービス プリンシパルを使用して Azure SQL Database Managed Instance をスキャンできるようにするには、いくつかの手順があります。

#### <a name="create-or-use-an-existing-service-principal"></a>サービス プリンシパルを作成するか、既存のものを使用する

サービス プリンシパルを使用するには、既存のものを使用するか、新しく作成することができます。 既存のサービス プリンシパルを使用する場合は、次の手順までスキップしてください。
新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。

 1. [Azure Portal](https://portal.azure.com) に移動します。
 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
 1. **[アプリの登録]** を選択します。
 1. **[+ 新しいアプリケーションの登録]** を選択します。
 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
 1. 次に、 **[登録]** を選択します。

#### <a name="configure-azure-ad-authentication-in-the-database-account"></a>データベース アカウントで Azure AD 認証を構成する

サービス プリンシパルには、データベース、スキーマ、およびテーブルのメタデータを取得するためのアクセス許可が必要です。 また、分類用のサンプリングを行うために、テーブルに対してクエリを実行できる必要もあります。
- [Azure SQL での Azure AD 認証を構成して管理する](../azure-sql/database/authentication-aad-configure.md)
- 「[Azure AD ID にマップされる包含ユーザーを作成する](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)」の前提条件とチュートリアルに従って、Azure SQL Database Managed Instance に Azure AD ユーザーを作成する
- ID に `db_datareader` アクセス許可を割り当てます。

#### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>キー コンテナーおよび Purview の資格情報にサービス プリンシパルを追加する

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) で自分のサービス プリンシパルに移動します
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定]、[シークレット]** の順に選択します
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、スキャンを設定するために、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)します。

#### <a name="sql-authentication-to-register"></a>登録する SQL 認証

> [!Note]
> サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可を付与した後、**15 分** ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

Azure SQL Database Managed Instance のログインが使用可能になっていない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)」の手順に従って、ログインを作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure portal で、キー コンテナーに移動します
1. **[設定]、[シークレット]** の順に選択します
1. **[+ Generate/Import]\(+ 生成/インポート\)** を選択し、**名前** と **値** を Azure SQL Database Managed Instance の "*パスワード*" として入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、スキャンを設定するために、**ユーザー名** と **パスワード** を使用して [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)します。

### <a name="steps-to-register"></a>登録する手順

1. [Purview Studio](https://web.purview.azure.com/resource/) に移動します

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. **[Azure SQL Database Managed Instance]** 、 **[続行]** の順に選択します。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="SQL データ ソースを設定する":::

1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。

1. **パブリック エンドポイントの完全修飾ドメイン名** および **ポート番号** を指定します。 その後、 **[登録]** を選択してデータ ソースを登録します。

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database Managed Instance を追加する":::

    たとえば: `foobar.public.123.database.windows.net,3342`

## <a name="scan"></a>スキャン

次の手順に従って Azure SQL Database Managed Instance をスキャンして、自動的にアセットを識別し、データを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側にあるペインで **[Data Map]** タブを選択します。

1. 自分が登録した Azure SQL Database Managed Instance ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-scan-sql-mi.png" alt-text="スキャンを設定する":::

1. リストから適切な項目を選択することによって、特定のテーブルに対するスキャンの範囲を指定することができます。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scope-your-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/trigger-scan.png" alt-text="trigger":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

<<<<<<< HEAD
- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
=======
ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
>>>>>>> repo_sync_working_branch
