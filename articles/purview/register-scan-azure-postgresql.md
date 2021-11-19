---
title: Azure Database for PostgreSQL に接続して管理する
description: このガイドでは、Azure Purview で Azure Database for PostgreSQL 単一サーバーに接続し、Azure Purview の機能を使用して Azure Database for PostgreSQL ソースをスキャンおよび管理する方法について説明します。
author: evangelinew
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 46e2f60adc895b60370ed7109305bb8966057fa3
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402392"
---
# <a name="connect-to-and-manage-an-azure-database-for-postgresql-in-azure-purview"></a>Azure Purview で Azure Database for PostgreSQL に接続して管理する

この記事では、単一サーバー デプロイ オプションでデプロイされた Azure Database for PostgreSQL を登録する方法と、Azure Purview で Azure Database for PostgreSQL を認証して操作する方法の概要を説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)| [あり](#scan) | [はい](#scan) | [はい](#scan) | いいえ | いいえ** |

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

> [!Important]
>  Purview は、Azure Database for PostgreSQL の単一サーバー デプロイ オプションのみをサポートしています。 バージョン 8.x ～ 12.x

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Azure Database for PostgreSQL を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

現時点では、Azure Database for PostgreSQL 単一サーバーを管理および操作するためには、SQL 認証のみがサポートされています。

#### <a name="sql-authentication"></a>SQL 認証

Azure Database for PostgreSQL データベースに接続するには、完全修飾サーバー名とログイン資格情報が必要です。 Azure database for PostgreSQL のログインが使用可能になっていない場合は、「[接続とクエリ](../postgresql/connect-python.md)」に関するページの手順に従って、ログイン情報を作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure Key Vault をまだお持ちではない場合は、[このガイドに従って Azure Key Vault を作成](../key-vault/certificates/quick-create-portal.md)してください。
1. Azure portal で、キー コンテナーに移動します
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、**名前** と **値** を Azure PostgreSQL Database の "*パスワード*" として入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して、SQL 認証型の [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、ご自分のスキャンを設定します。

### <a name="steps-to-register"></a>登録する手順

新しい Azure database for PostgreSQL をデータ カタログに登録するには、次の手順を実行します。

1. ご自分の Purview アカウントに移動します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します

1. **[ソースの登録]** で、 **[Azure Database for PostgreSQL]** を選択します。 **[続行]** をクリックします。

:::image type="content" source="media/register-scan-azure-postgresql/01-register-azure-postgres.png" alt-text="新しいデータ ソースの登録" border="true":::

**[Azure Database for PostgreSQL のソース登録]** 画面で、以下を実行します。

1. データ ソースの **名前** を入力します。 これがご自分のカタログのこのデータ ソースの表示名になります。
1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。
1. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-postgresql/02-register-source-azure-postgres.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Azure Database for PostgreSQL データベースをスキャンして、資産を自動的に識別し、データを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Database for PostgreSQL ソースを選択します。

1. **[新しいスキャン]** を選択します。

1. 対象のデータ ソースに接続するための資格情報を選択します。

   :::image type="content" source="media/register-scan-azure-postgresql/03-azure-postgres-scan.png" alt-text="スキャンを設定する":::

1. 特定のフォルダーまたはサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

   :::image type="content" source="media/register-scan-azure-postgresql/04-scope-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-postgresql/05-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-postgresql/06-trigger-scan.png" alt-text="スキャンのトリガー":::

1. スキャンを確認し、 **[保存および実行]** を選択します。

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
