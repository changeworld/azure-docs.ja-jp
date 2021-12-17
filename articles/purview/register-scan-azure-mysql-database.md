---
title: Azure Database for MySQL に接続して管理する
description: このガイドでは、Azure Purview で Azure Database for MySQL に接続し、Azure Purview の機能を使用して Azure Database for MySQL ソースをスキャンおよび管理する方法について説明します。
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: bcde9aee9719f8dbb127b908c312cc734c559f02
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131841895"
---
# <a name="connect-to-and-manage-azure-databases-for-mysql-in-azure-purview"></a>Azure Purview で Azure Database for MySQL に接続して管理する

この記事では、Azure Purview で、Azure Database for MySQL を登録する方法と、Azure Database for MySQL を認証して操作する方法について説明します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register) | [あり](#scan)| [はい*](#scan) | [あり](#scan) | [あり](#scan) | いいえ | いいえ** |

\* Purview では、増分スキャンのために Azure Database for MySQL の UPDATE_TIME メタデータが利用されます。 場合によっては、このフィールドがデータベースに保持されておらず、フルスキャンが実行されることがあります。 詳細については、MySQL の [INFORMATION_SCHEMA TABLES テーブル](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html)を参照してください。

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[Purview Studio](https://web.purview.azure.com/) を使用して Azure Purview に Azure Database for MySQL を登録する方法について説明します。

### <a name="authentication-for-registration"></a>登録の認証

次の手順で、**ユーザー名** と **パスワード** が必要になります。

[データベースとユーザーの作成](../mysql/howto-create-users.md)に関するページの手順に従って、ご自分の Azure Database for MySQL のログインを作成します。

1. Azure portal で、キー コンテナーに移動します
1. **[設定]、[シークレット]** の順に選択します
1. **[+ Generate/Import]\(+ 生成/インポート\)** を選択し、**名前** と **値** を Azure SQL Database の "*パスワード*" として入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して、SQL 認証型の [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、ご自分のスキャンを設定します。

### <a name="steps-to-register"></a>登録する手順

新しい Azure Database for MySQL をデータ カタログに登録するには、次の手順を実行します。

1. ご自分の Purview アカウントに移動します。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. **[登録]** を選択します。

1. **[ソースの登録]** で、 **[Azure Database for MySQL]** を選択します。 **[続行]** をクリックします。

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[Register sources (Azure Database for MySQL)]\(Azure Database for MySQL のソース登録\)** 画面で、以下を実行します。

1. ご自分のデータ ソースの **名前** を入力します。 これがご自分のカタログのこのデータ ソースの表示名になります。
1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。
1. **[登録]** を選択してデータ ソースを登録します。

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="scan"></a>スキャン

次の手順に従って、Azure Database for MySQL をスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください。

### <a name="create-and-run-scan"></a>スキャンの作成と実行

新しいスキャンを作成して実行するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) の左側のペインで **[Data Map]** タブを選択します。

1. 登録した Azure Database for MySQL のソースを選択します。

1. **[新しいスキャン]** を選択します。

1. ご自分のデータソースに接続する資格情報を選択し、接続を確認して、ご自分の資格情報が適切に構成されていることを確認します。

   :::image type="content" source="media/register-scan-azure-mysql/03-new-scan-azure-mysql-connection-credential.png" alt-text="スキャンを設定する":::

1. 特定のフォルダーまたはサブフォルダーをリストから選んで、スキャンのスコープに指定できます。

   :::image type="content" source="media/register-scan-azure-mysql/04-scope-azure-mysql-scan.png" alt-text="スキャンの範囲を指定する":::

1. 次に、スキャン ルール セットを選択します。 システムの既定のものを選択するか、既存のカスタム ルール セットを使用するか、新しいルール セットをインラインで作成することができます。

   :::image type="content" source="media/register-scan-azure-mysql/05-scan-rule-set.png" alt-text="スキャン ルール セット":::

1. スキャン トリガーを選択します。 スケジュールを設定することも、1 回限りのスキャンを実行することもできます。

   :::image type="content" source="media/register-scan-azure-mysql/06-trigger-scan.png" alt-text="trigger":::

1. ご自分のスキャンを確認し、 **[保存および実行]** を選択します。

:::image type="content" source="media/register-scan-azure-mysql/07-review-your-scan.png" alt-text="スキャンを確認する" border="true":::

[!INCLUDE [view and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
