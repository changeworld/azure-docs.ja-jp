---
title: "Azure Portal を使用した Azure Database for MySQL サーバーの作成と管理 | Microsoft Docs"
description: "この記事では、Azure Portal を使用して Azure Database for MySQL サーバーをすばやく作成して管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: b73fe2214a165d7c02c0a58551d8b84bee39f919
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal を使用した Azure Database for MySQL サーバーの作成と管理
このトピックでは、新しい Azure Database for MySQL サーバーをすばやく作成する方法について説明します。 Azure Portal を使用したサーバーの管理方法に関する情報も含まれています。 サーバーの管理には、サーバーの詳細とデータベースの表示、パスワードのリセット、サーバーの削除が含まれます。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
"mysqlserver4demo" という名前の Azure Database for MySQL サーバーを作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[リソースの作成]** ボタンをクリックします。

2. [新規] ページで **[データベース]** を選択し、[データベース] ページで **[Azure Database for MySQL]** を選択します。

    > Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使用して作成されます。 データベースは、Azure リソース グループ内と Azure Database for MySQL サーバー内に作成されます。

   ![新しいサーバーの作成](./media/howto-create-manage-server-portal/create-new-server.png)

3. 次の情報を使用して、Azure Database for MySQL のフォームに入力します。

    | **フォームのフィールド** | **フィールドの説明** |
    |----------------|-----------------------|
    | *[サーバー名]* | azure-mysql (サーバー名はグローバルに一意である必要があります) |
    | *サブスクリプション* | MySQLaaS (ドロップダウン メニューから選択) |
    | *[リソース グループ]* | myresource (新しいリソース グループを作成するか既存のリソース グループを使用します) |
    | *[サーバー管理者ログイン]* | myadmin (管理者アカウント名をセットアップ) |
    | *パスワード* | 管理者アカウントのパスワードをセットアップします |
    | *パスワードの確認* | 管理者アカウントのパスワードを確認 |
    | *場所* | 北ヨーロッパ (北ヨーロッパ～米国西部を選択します) |
    | *バージョン* | 5.6 (Azure Database for MySQL サーバーのバージョンを選択します) |

4. **[価格レベル]** をクリックして、新しいサーバーのサービス レベルとパフォーマンス レベルを指定します。 コンピューティング ユニット数は、Basic レベルの場合は 50 ～ 100、Standard レベルの場合は 100 ～ 200 で構成できます。また、ストレージは、含まれている使用量に基づいて追加できます。 このハウツー ガイドでは、50 コンピューティング ユニットと 50 GB を選択します。 **[OK]** をクリックして選択を保存します。

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. **[作成]** をクリックしてサーバーをプロビジョニングします。 プロビジョニングには数分かかります。

    > **[ダッシュボードにピン留めする]** チェック ボックスをオンにすると、デプロイを追跡しやすくなります。
    > [!NOTE]
    > ストレージは Basic レベルでは最大 1000 GB、Standard レベルでは 10000 GB までサポートされますが、パブリック プレビューでは一時的に 1000 GB に制限されます。</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの更新
新しいサーバーがプロビジョニングされると、ユーザーは、既存のサーバーの編集について、管理者パスワードをリセットするか、コンピューティング ユニットの変更によってサーバーをスケールアップまたはスケールダウンするという 2 つの選択肢があります。

### <a name="change-the-administrator-user-password"></a>管理者ユーザー パスワードを変更する
1. サーバーの **[概要]** ブレードで、**[パスワードのリセット]** をクリックして、パスワードの入力と確認ウィンドウで設定を行います。

2. 次のように、ウィンドウで新しいパスワードを入力し、そのパスワードを確認します。

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. **[OK]** をクリックして新しいパスワードを保存します。

### <a name="scale-updown-by-changing-compute-units"></a>コンピューティング ユニットを変更して スケール アップまたはダウンする

1. サーバー ブレードで、**[設定]** の **[価格レベル]** をクリックして、Azure Database for MySQL サーバーの [価格レベル] ブレードを開きます。

2. 「**Azure Database for MySQL サーバーの作成**」の手順 4. に従って、同じ価格レベルのコンピューティング ユニットを変更します。

## <a name="delete-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの削除

1. サーバーの **[概要]** ブレードで、**[削除]** コマンド ボタンをクリックして、[削除中] 確認ブレードを開きます。

2. もう一度確認するため、ブレードの入力ボックスに正しいサーバー名を入力します。

3. **[削除]** ボタンをもう一度クリックして削除中の動作を確認し、通知バーに "Deleting success (削除完了)" のポップアップが表示されるまで待ちます。

## <a name="list-the-azure-database-for-mysql-databases"></a>Azure Database for MySQL データベースの一覧表示
サーバーの **[概要]** ブレードで、下部にあるデータベース タイルが表示されるまで下にスクロールします。 すべてのデータベースが表に一覧表示されます。 **[削除]** コマンド ボタンをクリックすると、[削除中] 確認ブレードが開きます。

   ![データベースの表示](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの詳細を表示する
サーバー ブレードの **[設定]** をクリックし、**[プロパティ]** をクリックして **[プロパティ]** ブレードを開き、サーバーに関する詳細情報をすべて表示します。

## <a name="next-steps"></a>次の手順

[クイックスタート: Azure Portal を使用した Azure Database for MySQL サーバーの作成](./quickstart-create-mysql-server-database-using-azure-portal.md)