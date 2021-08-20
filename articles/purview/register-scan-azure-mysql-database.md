---
title: Azure Database for MySQL の登録とスキャン
description: このチュートリアルでは、Azure Database for MySQL をスキャンする方法について説明します
author: evwhite
ms.author: evwhite
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 06/30/2021
ms.openlocfilehash: a5edf1b85d97798e5ed159433961c4c0d76cfcca
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114208255"
---
# <a name="register-and-scan-an-azure-mysql-database"></a>Azure Database for MySQL の登録とスキャン

この記事では、Azure Database for MySQL を登録してスキャンする方法について説明します。


## <a name="supported-capabilities"></a>サポートされる機能
- Azure Database for MySQL のメタデータと分類をキャプチャする **完全および増分スキャン**。

- ADF コピーとデータフロー アクティビティのためのデータ アセット間の **系列**。

### <a name="known-limitations"></a>既知の制限事項
Purview は、Azure Database for MySQL の SQL 認証のみをサポートしています。


## <a name="prerequisites"></a>必須コンポーネント

1. Purview アカウントをまだお持ちでない場合は、新たに作成します。

2. ご自分の Purview アカウントと Azure Database for MySQL 間のネットワーク アクセス。

### <a name="set-up-authentication-for-a-scan"></a>スキャンの認証の設定

次の手順で、**ユーザー名** と **パスワード** が必要になります。

[データベースとユーザーの作成](../mysql/howto-create-users.md)に関するページの手順に従って、ご自分の Azure Database for MySQL のログインを作成します。

1. Azure portal で、キー コンテナーに移動します
1. **[設定]、[シークレット]** の順に選択します
1. **[+ Generate/Import]\(+ 生成/インポート\)** を選択し、**名前** と **値** を Azure SQL Database の "*パスワード*" として入力します
1. **[作成]** を選択して完了します
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、**ユーザー名** と **パスワード** を使用して、SQL 認証型の[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、ご自分のスキャンを設定します。

## <a name="register-an-azure-mysql-database-data-source"></a>Azure Database for MySQL のデータ ソースの登録

新しい Azure Database for MySQL をお使いのデータ カタログに登録するには、次の手順を行います。

1. ご自分の Purview アカウントに移動します。

1. 左側のナビゲーションで **[ソース]** を選択します。

1. **[登録]** を選択します。

1. **[ソースの登録]** で、 **[Azure Database for MySQL]** を選択します。 **[続行]** をクリックします。

:::image type="content" source="media/register-scan-azure-mysql/01-register-azure-mysql-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[Register sources (Azure Database for MySQL)]\(ソースの登録 (Azure Database for MySQL)\)** 画面で、以下を実行します。

1. ご自分のデータ ソースの **名前** を入力します。 これがご自分のカタログのこのデータ ソースの表示名になります。
1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択し、 **[Azure subscription]\(Azure サブスクリプション\)** ドロップダウン ボックスから適切なサブスクリプション、 **[Server name]\(サーバー名\)** ドロップダウン ボックスから適切なサーバーを選択します。
1. **[登録]** を選択してデータ ソースを登録します。 

:::image type="content" source="media/register-scan-azure-mysql/02-register-azure-mysql-name-connection.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="creating-and-running-a-scan"></a>スキャンを作成し、実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio の左側にあるペインで **[Data Map]** タブを選択します。

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

> [!NOTE]
> * ご自分のスキャンを削除しても、以前の Azure Database for MySQL のスキャンのアセットは削除されません。
> * Purview の [スキーマ] タブの説明を編集した後に、お使いのソース テーブルが変更され、ソース テーブルを再スキャンした場合は、アセットはスキーマの変更によって更新されなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
