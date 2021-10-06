---
title: Azure database for PostgreSQL の登録とスキャン
description: このチュートリアルでは、Azure Purview で Azure database for PostgreSQL データベースをスキャンする方法について説明します。
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-map
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: abc676fbff551781f720db5937a9c35c7c8f81ea
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209807"
---
# <a name="register-and-scan-an-azure-database-for-postgresql"></a>Azure database for PostgreSQL の登録とスキャン

この記事では、Azure database for PostgreSQL を登録してスキャンする方法について説明します。


## <a name="supported-capabilities"></a>サポートされる機能
- Azure databases for PostgreSQL のメタデータと分類をキャプチャする **完全および増分スキャン**。

- ADF コピーとデータフロー アクティビティのためのデータ アセット間の **系列**。

### <a name="known-limitations"></a>既知の制限事項

Purview では、Azure database for PostgreSQL の SQL 認証のみをサポートしています。


## <a name="prerequisites"></a>必須コンポーネント

1. Purview アカウントをまだお持ちでない場合は、新たに作成します。

2. Purview アカウントと Azure database for PostgreSQL の間のネットワーク アクセス。

#### <a name="sql-authentication-for-an-azure-database-for-postgresql"></a>Azure database for PostgreSQL の SQL 認証

Azure Database for PostgreSQL データベースに接続するには、完全修飾サーバー名とログイン資格情報が必要です。 Azure database for PostgreSQL のログインが使用可能になっていない場合は、「[接続とクエリ](../postgresql/connect-python.md)」に関するページの手順に従って、ログイン情報を作成できます。 次の手順で、**ユーザー名** と **パスワード** が必要になります。

1. Azure portal で、キー コンテナーに移動します
1. **[設定] > [シークレット]** の順に選択します。
1. **[+ 生成/インポート]** を選択し、**名前** と **値** を Azure PostgreSQL Database の "*パスワード*" として入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して、SQL 認証型の [新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

## <a name="register-an-azure-database-for-postgresql-data-source"></a>Azure database for PostgreSQL データ ソースの登録

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

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

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

> [!NOTE]
> * スキャンを削除しても、以前のスキャンから作成されたカタログ アセットは削除されません。
> * Purview の [スキーマ] タブの説明を編集した後に、ソース テーブルが変更され、ソース テーブルを再スキャンした場合、アセットはスキーマの変更によって更新されなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)