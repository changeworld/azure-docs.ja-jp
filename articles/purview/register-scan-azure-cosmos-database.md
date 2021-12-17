---
title: Azure Cosmos Database (SQL API) の登録とスキャン
description: この記事では、Azure Purview に Azure Cosmos データ ソース (SQL API) を登録する手順について説明します。これには、Azure Cosmos データベースを認証し操作する手順が含まれます
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ba4c12c51ad744e6c68d63c1aaad17f32fbec15e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841982"
---
# <a name="connect-to-azure-cosmos-database-sql-api-in-azure-purview"></a>Azure Purview で Azure Cosmos データベース (SQL API) に接続する

この記事では、Azure Purview に Azure Cosmos データベース (SQL API) を登録する手順について説明します。これには、Azure Cosmos データベース ソースを認証し操作する手順が含まれます

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)|[あり](#scan) | [あり](#scan)|[あり](#scan)|いいえ|いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、Azure Cosmos データベース (SQL API) を登録し、適切な認証メカニズムを設定して、データ ソースが正常にスキャンされるようにします。

### <a name="steps-to-register"></a>登録する手順

データ ソースのスキャンを設定する前に、Azure Purview にデータ ソースを登録することが重要です。

1. [Azure portal](https://portal.azure.com) にアクセスし、 **[Purview アカウント]** ページに移動して、自分の _Purview アカウント_ をクリックします

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-purview-acct.png" alt-text="データ ソースの登録に使用する Purview アカウントを示すスクリーンショット":::

1. **Purview Studio を開き**、 **[Data Map] > [コレクション]** の順に移動します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-open-purview-studio.png" alt-text="Data Map の [ソース] リンクへの移動を示すスクリーンショット":::

1. **コレクション** メニューを使用して [コレクション階層](./quickstart-create-collection.md)を作成し、必要に応じて個々のサブコレクションへのアクセス許可を割り当てます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collections.png" alt-text="コレクション階層を作成するコレクション メニューを示すスクリーンショット":::

1. **ソース** メニューの該当するコレクションに移動し、 **[登録]** アイコンをクリックして新しい Azure Cosmos データベースを登録します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-register-data-source.png" alt-text="データ ソースの登録に使用するコレクションを示すスクリーンショット":::

1. **Azure Cosmos DB (SQL API)** データ ソースを選択し、 **[続行]** を選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-data-source.png" alt-text="データ ソースの選択を許可するスクリーンショット":::

1. データ ソースに適切な **名前** を指定し、関連する **Azure サブスクリプション**、**Cosmos DB アカウント名**、**コレクション** を選択し、 **[適用]** を選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-data-source-details.png" alt-text="データ ソースを登録するために入力する詳細を示すスクリーンショット":::

1. 選択したコレクションの下に _Azure Cosmos データベース_ ストレージ アカウントが表示されます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-collection-mapping.png" alt-text="スキャンを開始するためにコレクションにマップされたデータ ソースを示すスクリーンショット":::

## <a name="scan"></a>スキャン

### <a name="authentication-for-a-scan"></a>スキャンの認証

データ ソースをスキャンするアクセス権を得るには、Azure Cosmos データベース ストレージ アカウントの認証方法を構成する必要があります。

Azure Cosmos Database の認証を設定する方法は 1 つだけです。

**アカウント キー** - Azure Purview へのアクセスを有効にし、シークレットを使用してデータソースを安全にスキャンするために、Azure Key Vault 内にシークレットを作成して資格情報を格納できます。 シークレットには、ストレージ アカウント キー、SQL ログイン パスワード、またはパスワードを指定できます。

> [!Note]
> サブスクリプションに _Azure キー コンテナー_ リソースをデプロイし、_Azure キー コンテナー_ 内のシークレットへの必要なアクセス許可を持つ _Azure Purview アカウントの_ MSI を割り当てる必要があります。

#### <a name="using-account-key-for-scanning"></a>スキャンにアカウント キーを使用する

アクセス キーを取得し、キー コンテナーに格納する必要があります。

1. Azure Cosmos データベース ストレージ アカウントに移動します
1. **[設定]、[キー]** の順に選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-access-keys.png" alt-text="ストレージ アカウント内のアクセス キーを示すスクリーンショット":::

1. *キー* をコピーし、次の手順のために個別に保存します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key.png" alt-text="コピーするアクセス キーを示すスクリーンショット":::

1. お使いのキー コンテナーに移動する

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault.png" alt-text="キー コンテナーを示すスクリーンショット":::

1. **[設定 > シークレット]** を選択し、 **[+ 生成/インポート]** を選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-generate-secret.png" alt-text="シークレットを生成するキー コンテナー オプションを示すスクリーンショット":::

1. **[名前]** と **[値]** にストレージ アカウントの *キー* を入力し、 **[作成]** を選択して完了します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-key-vault-options.png" alt-text="シークレット値を入力するキー コンテナー オプションを示すスクリーンショット":::

1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、キーを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します。

### <a name="creating-the-scan"></a>スキャンの作成

1. **Purview アカウント** を開き、 **[Purview Studio を開く]** を選択します
1. **[データ マップ]**  -->  **[ソース]** に移動してコレクション階層を表示します
1. 以前に登録した **Azure Cosmos データベース** の **[新しいスキャン]** アイコンを選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-create-scan.png" alt-text="新しいスキャンを作成する画面を示すスクリーンショット":::

1. スキャンの **名前** を指定し、スキャンに適したコレクションを選択して、 **[資格情報]** で **[+ 新規]** を選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-option.png" alt-text="スキャン用の [アカウント キー] オプションを示すスクリーンショット":::

1. _アカウント キー_ の作成時に使用した適切な **キー コンテナー接続** と **シークレット名** を選択します。 **認証方法** として _[アカウント キー]_ を選択します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-acct-key-details.png" alt-text="アカウント キー オプションを示すスクリーンショット":::

1. **[接続テスト]** を選択します。 接続に成功したら **[続行]** をクリックします

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-test-connection.png" alt-text="接続テストの成功を示すスクリーンショット":::

### <a name="scoping-and-running-the-scan"></a>スキャンの対象範囲と実行

1. 特定のフォルダーおよびサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-rule-set.png" alt-text="スキャン ルール セット":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-new-scan-rule-set.png" alt-text="新しいスキャン ルール":::

1. スキャン ルールに含める **分類ルール** を選択できます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-classification.png" alt-text="スキャン ルール セットの分類ルール":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-select-scan-rule-set.png" alt-text="スキャン ルール セットの選択":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-trigger.png" alt-text="スキャン トリガー":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-review-scan.png" alt-text="スキャンの確認":::

### <a name="viewing-scan"></a>スキャンの表示

1. _[コレクション]_ 内の _[データ ソース]_ に移動し、 **[詳細の表示]** をクリックしてスキャンの状態を確認します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-view-scan.png" alt-text="スキャンの表示":::

1. スキャンの詳細には、**最終実行状態** でのスキャンの進行状況と、_スキャン_ および _分類_ されたアセットの数が示されます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-last-run-status.png" alt-text="スキャンの詳細の表示":::

1. スキャン全体が正常に実行されると、**最後の実行状態** が **[進行中]** に更新され、その後 **[完了]** に更新されます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-in-progress.png" alt-text="進行中のスキャンの表示":::

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-scan-completed.png" alt-text="完了したスキャンの表示":::

### <a name="managing-scan"></a>スキャンの管理

スキャンは、完了時に管理したり、再度実行したりできます。

1. **スキャン名** をクリックしてスキャンを管理します

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan.png" alt-text="スキャンの管理":::

1. _スキャンを再実行_ したり、_スキャンを編集_ したり、_スキャンを削除_ したりできます  

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-manage-scan-options.png" alt-text="スキャン オプションの管理":::

1. _フル スキャン_ を再度実行することができます

    :::image type="content" source="media/register-scan-azure-cosmos-database/register-cosmos-db-full-scan.png" alt-text="フル スキャン":::

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
