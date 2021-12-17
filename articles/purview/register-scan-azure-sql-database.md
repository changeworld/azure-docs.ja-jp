---
title: Azure SQL DB の登録とスキャン
description: このアーティクルでは、Azure Purview に Azure SQL データベース を登録する手順について説明します。これには、Azure SQL DB ソースを認証し操作する手順が含まれます
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: cba37228902600852963068ba4d46e75adf21c57
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853892"
---
# <a name="connect-to-azure-sql-database-in-azure-purview"></a>Azure Purview の Azure SQL Database のデータベースに接続します。

このアーティクルでは、Azure Purview に Azure SQL データ ソースを登録する手順について説明します。これには、Azure SQL DB ソースを認証し操作する手順が含まれます

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)|[あり](#scan) | [あり](#scan)|[あり](#scan)| いいえ | いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

### <a name="known-limitations"></a>既知の制限事項

* Azure Purview の [スキーマ] タブでは 300 を超える列がサポートされておらず、"Additional-Columns-Truncated (その他の列は切り詰められています)" と表示されます。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、Azure SQL DB データ ソースを登録し、適切な認証メカニズムを設定して、データ ソースが正常にスキャンされるようにします。

### <a name="steps-to-register"></a>登録する手順

データ ソースのスキャンを設定する前に、Azure Purview にデータ ソースを登録することが重要です。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[Purview アカウント]** ページに移動して、自分の _Purview アカウント_ をクリックします

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-purview-acct.png" alt-text="データ ソースの登録に使用する Purview アカウントを示すスクリーンショット":::

1. **Purview Studio を開き**、 **[Data Map]** に移動します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-open-purview-studio.png" alt-text="Data Map の [ソース] リンクへの移動を示すスクリーンショット":::

<<<<<<< HEAD
Purview でサービス プリンシパルまたは Purview の **マネージド ID** を使用して Azure SQL Database をスキャンできるようにするには、いくつかの手順があります
=======
1. **コレクション** メニューを使用して [コレクション階層](./quickstart-create-collection.md)を作成し、必要に応じて個々のサブコレクションへのアクセス許可を割り当てます
>>>>>>> repo_sync_working_branch

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-collections.png" alt-text="コレクション階層にアクセス制御権限を割り当てるコレクション メニューのスクリーンショット":::

1. **ソース** メニューの該当するコレクションに移動し、 **[Register (登録)]** アイコンをクリックして新しい Azure SQL DB を登録します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-data-source.png" alt-text="データ ソースの登録に使用するコレクションを示すスクリーンショット":::

1. **Azure SQL Database** データ ソースを選択し、 **[続行]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-select-ds.png" alt-text="データ ソースの選択を示すスクリーンショット":::

1. データ ソースに適切な **名前** を指定し、関連する **Azure サブスクリプション**、SQL サーバーの **サーバー名**、**コレクション** を選択し、 **[適用する]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-details.png" alt-text="データ ソースを登録するために入力する詳細を示すスクリーンショット":::

1. Azure SQL Server データベースが、選択したコレクションの下に表示されます

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-ds-collections.png" alt-text="スキャンを開始するためにコレクションにマップされたデータ ソースを示すスクリーンショット":::

## <a name="scan"></a>スキャン

### <a name="authentication-for-a-scan"></a>スキャンの認証

データ ソースをスキャンするアクセス権を得るには、Azure SQL Database の認証方法を構成する必要があります。
次のオプションがサポートされています。

* **SQL 認証**

* **マネージド ID** - Azure Purview アカウントが作成されるとすぐに、システムの **マネージド ID** が Azure AD テナント内に自動的に作成されます。 Azure Purview MSI でスキャンを実行するには、リソースの種類に応じて、特定の RBAC ロールを割り当てる必要があります。
マネージド ID を使用している場合、Purview アカウントには独自のマネージド ID があります。これは基本的に、作成時の Purview の名前です

* **サービス プリンシパル** - この方法では、Azure Active Directory テナントで新しく作成することも、既存のサービス プリンシパルを使用することもできます。

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>データベース アカウントで Azure AD 認証を構成する

サービス プリンシパルまたはマネージド ID には、データベース、スキーマ、およびテーブルのメタデータを取得するためのアクセス許可が必要です。 また、分類用のサンプリングを行うために、テーブルに対してクエリを実行できる必要もあります。

- [Azure SQL での Azure AD 認証を構成して管理する](../azure-sql/database/authentication-aad-configure.md)
- 「[Azure SQL Database にサービス プリンシパル ユーザーを作成する](../azure-sql/database/authentication-aad-service-principal-tutorial.md#create-the-service-principal-user-in-azure-sql-database)」のチュートリアルに従い、Purview の正確なマネージド ID または独自のサービス プリンシパルを使用して、Azure SQL Database に Azure AD ユーザーを作成する必要があります。 ID に適切なアクセス許可 (`db_datareader` など) を割り当てる必要があります。 ユーザーを作成してアクセス許可を付与する SQL 構文の例:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_datareader', [Username]
    GO
    ```

    > [!Note]
    > `Username` は、独自のサービス プリンシパルか、Purview のマネージド ID です。 [固定データベース ロールとその機能](/sql/relational-databases/security/authentication-access/database-level-roles#fixed-database-roles)を確認してください。

#### <a name="using-sql-authentication-for-scanning"></a>スキャンに SQL 認証を使用

> [!Note]
> サーバーレベル プリンシパルのログイン (準備プロセスで作成) または master データベースの `loginmanager` データベース ロールのメンバーだけが新しいログインを作成できます。 アクセス許可を付与した後、**15 分** ほどかかります。Purview アカウントには、リソースをスキャンできるようにするための適切なアクセス許可が必要です。

Azure SQL Database のログインが使用可能になっていない場合は、「[CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1)」の手順に従って、ログインを作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure portal で、キー コンテナーに移動します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="キー コンテナーを示すスクリーンショット":::

1. **[設定 > シークレット]** を選択し、 **[+ 生成/インポート]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="シークレットを生成するキー コンテナー オプションを示すスクリーンショット":::

1. Azure SQL Database から **名前** と **値** を *パスワード* として入力します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret-sql.png" alt-text="SQL シークレット値を入力するキー コンテナー オプションを示すスクリーンショット":::

1. **[作成]** を選択して完了します

1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="認証情報を設定するキー コンテナー オプションを示すスクリーンショット":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault-options.png" alt-text="シークレットを生成するキー コンテナー オプションを示すスクリーンショット":::

#### <a name="using-managed-identity-for-scanning"></a>スキャンでのマネージド ID の使用

Purview アカウントに、Azure SQL DB をスキャンするためのアクセス許可を付与することは重要です。 スキャンのアクセス許可を付与する対象に応じて、サブスクリプション、リソースグループ、またはリソースのレベルに Catalog の MSI を追加できます。

> [!Note] 
> Azure リソースにマネージド ID を追加できるようにするには、サブスクリプションの所有者である必要があります。

1. [Azure portal](https://portal.azure.com) で、カタログのスキャンを許可したいサブスクリプション、リソース グループ、リソース (Azure SQL Database など) のいずれかを探します。

1. 左側のナビゲーションで **[アクセス制御 (IAM)]** を選択し、 **[追加]**  -->  **[ロール割り当ての追加]** を選択します。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-ds.png" alt-text="Azure SQL データベースのスクリーンショット":::

1. **[ロール]** を **[閲覧者]** に設定し、 **[選択]** の入力ボックスに _Azure Purview アカウント名_ を入力します。 次に、 **[保存]** を選択して、このロールの割り当てを Purview アカウントに付与します。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-access-managed-identity.png" alt-text="Purview アカウントにアクセス許可を割り当てる方法の詳細を示すスクリーンショット":::

#### <a name="using-service-principal-for-scanning"></a>スキャンでのサービス プリンシパルの使用

##### <a name="creating-a-new-service-principal"></a>新しいサービス プリンシパルの作成

[新しいサービス プリンシパルを作成する](./create-service-principal-azure.md)必要がある場合は、Azure AD テナントにアプリケーションを登録し、データ ソースでサービス プリンシパルへのアクセスを付与する必要があります。 Azure AD 全体管理者やアプリケーション管理者などの他のロールで、この操作を実行できます。

##### <a name="getting-the-service-principals-application-id"></a>サービス プリンシパルのアプリケーション ID の取得

1. 既に作成されている [_サービス プリンシパル_](./create-service-principal-azure.md)の **[概要]** にある **アプリケーション (クライアント) ID** をコピーします

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-appln-id.png" alt-text="サービス プリンシパルのアプリケーション (クライアント) ID を示すスクリーンショット":::

##### <a name="granting-the-service-principal-access-to-your-azure-sql-database"></a>Azure SQL Database へのアクセス権をサービス プリンシパルに付与する

1. Azure portal で、キー コンテナーに移動します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-key-vault.png" alt-text="サービス プリンシパルのシークレットを追加するキー コンテナーのスクリーンショット":::

1. **[設定 > シークレット]** を選択し、 **[+ 生成/インポート]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-secret.png" alt-text="サービス プリンシパルのシークレットを生成するキー コンテナーのスクリーンショット":::

1. **[名前]** に選択した内容を入力し、サービス プリンシパルの **クライアント シークレット** として **値** を入力します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-create-secret.png" alt-text="シークレット値を入力するキー コンテナー オプションを示すスクリーンショット":::

1. **[作成]** を選択して完了します

1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。

1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-credentials.png" alt-text="サービス プリンシパルの資格情報を追加するためのキー コンテナー オプションを示すスクリーンショット":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp-cred.png" alt-text="サービス プリンシパルのシークレットを作成するキー コンテナーのスクリーンショット":::

### <a name="firewall-settings"></a>ファイアウォールの設定

データベース サーバーでファイアウォールを有効にしている場合は、次に示す 2 つの方法のいずれかでアクセスを許可するようにファイアウォールを更新する必要があります。

1. ファイアウォールを介した Azure 接続を許可します。
1. セルフホステッド統合ランタイムをインストールし、ファイアウォールを介したアクセスを許可します。

#### <a name="allow-azure-connections"></a>Azure 接続を許可する

Azure 接続を有効にすると、ファイアウォール自体を更新することなく、Azure Purview がサーバーに到達して接続できるようになります。 [Azure 内からの接続](../azure-sql/database/firewall-configure.md#connections-from-inside-azure)については、ハウツー ガイドに従ってください。

1. データベース アカウントに移動します
1. **[概要]** ページでサーバー名を選択します
1. **[セキュリティ]、[ファイアウォールと仮想ネットワーク]** の順に選択します
1. **Azure のサービスとリソースにこのサーバーへのアクセスを許可**
:::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-firewall.png" alt-text="Azure のサービスとリソースにこのサーバーへのアクセスを許可" border="true":::で、 **[Yes (はい)]** を選択します。

#### <a name="self-hosted-integration-runtime"></a>セルフホステッド統合ランタイム

セルフホステッド統合ランタイム (SHIR) をマシンにインストールすると、プライベート ネットワーク内のリソースと接続できるようになります。

1. 個人用マシンまたはデータベース サーバーと同じ VNet 内のマシンに[セルフホステッド統合ランタイムを作成してインストールします](./manage-integration-runtimes.md)。
1. データベース サーバーのファイアウォールを確認し、SHIR マシンからファイアウォールを介してアクセスできることを確認します。 まだアクセスできない場合は、マシンの IP を追加します。
1. Azure SQL Server がプライベート エンドポイントの背後にある場合や VNet 内にある場合は、[インジェスト プライベート エンドポイント](catalog-private-link-ingestion.md#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)を使用して、エンドツーエンドのネットワーク分離を確保することができます。

### <a name="creating-the-scan"></a>スキャンの作成

1. **Purview アカウント** を開き、 **[Purview Studio を開く]** を選択します
1. **[データ マップ]**  -->  **[ソース]** に移動してコレクション階層を表示します
1. 以前に登録した **Azure SQL DB** の **[新しいスキャン]** アイコンを選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan.png" alt-text="新しいスキャンを作成する画面を示すスクリーンショット":::

#### <a name="if-using-sql-authentication"></a>SQL 認証を使用する場合

1. スキャンの **名前** を指定し、 **[Database selection method (データベースの選択方法)]** を _[Enter manually (手動で入力)]_ と選択し、**データベース名** と先に作成した **資格情報** を入力し、スキャンに適したコレクションを選択し、 **[テスト接続]** を選択して接続を検証します。 検証が成功したら、 **[Continue (続行)]** を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sql-auth.png" alt-text="スキャンの SQL 認証オプションを示すスクリーンショット":::

#### <a name="if-using-managed-identity"></a>マネージド ID を使用した場合

1. スキャンの **名前** を指定し、 **[資格情報]** で **[Purview MSI]** を選択し、スキャンに適したコレクションを選択して、スキャンに適切なコレクションを選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-managed-id.png" alt-text="スキャンを実行するマネージド ID オプションのスクリーンショット":::

1. **[接続テスト]** を選択します。 接続に成功したら **[続行]** をクリックします

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-test.png" alt-text="マネージド ID オプションでスキャンを実行できるスクリーンショット":::

#### <a name="if-using-service-principal"></a>サービス プリンシパルを使用する場合

1. スキャンの **名前** を指定し、スキャン用の適切なコレクションを選択し、 **[資格情報]** ドロップダウンを選択して、以前に作成した資格情報を選択します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sp.png" alt-text="スキャンを有効にするサービス プリンシパル オプションのスクリーンショット":::

1. **[接続テスト]** を選択します。 接続に成功したら **[続行]** をクリックします

### <a name="scoping-and-running-the-scan"></a>スキャンのスコープと実行

1. 特定のフォルダーおよびサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. 新規に _スキャン ルール セット_ を作成する場合

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-new-scan-rule-set.png" alt-text="新しいスキャン ルール セット":::

1. スキャン ルールに含める **分類ルール** を選択できます

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-classification.png" alt-text="スキャン ルール セットの分類ルール":::

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-sel-scan-rule.png" alt-text="スキャン ルール セットの選択":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-trigger.png" alt-text="スキャン トリガー":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-review-scan.png" alt-text="スキャンの確認":::

### <a name="view-scan"></a>スキャンの表示

1. _[コレクション]_ 内の _[データ ソース]_ に移動し、 **[詳細の表示]** をクリックしてスキャンの状態を確認します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan.png" alt-text="スキャンの表示":::

1. スキャンの詳細には、**最終実行状態** でのスキャンの進行状況と、_スキャン_ および _分類_ されたアセットの数が示されます

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-view-scan-details.png" alt-text="スキャンの詳細の表示":::

1. スキャン全体が正常に実行されると、**最後の実行状態** が **[進行中]** に更新され、その後 **[完了]** に更新されます

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-scan-complete.png" alt-text="完了したスキャンの表示":::

### <a name="manage-scan"></a>スキャンの管理

スキャンは、完了時に管理したり、再度実行できます

1. **スキャン名** をクリックしてスキャンを管理します

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage scan.png" alt-text="スキャンの管理":::

1. _スキャンを再実行_ したり、_スキャンを編集_ したり、_スキャンを削除_ したりできます  

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-manage-scan-options.png" alt-text="スキャン オプションの管理":::

1. _増分スキャンを実行_ することも、_完全スキャン_ を再度実行することもできます

    :::image type="content" source="media/register-scan-azure-sql-database/register-scan-azure-sql-db-full-inc.png" alt-text="完全スキャンまたは増分スキャン":::

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
