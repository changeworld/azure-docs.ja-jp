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
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: cbf1bcd90c53a7221773c7dc7731b37cf762e953
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal を使用した Azure Database for MySQL サーバーの作成と管理
この記事では、Azure Portal を使用して Azure Database for MySQL サーバーをすばやく作成して管理する方法について説明します。 サーバーの管理には、サーバーの詳細とデータベースの表示、パスワードのリセット、サーバーの削除が含まれます。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする
[Azure ポータル](https://portal.azure.com)にログインします。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
“mysqlserver4demo” という名前の Azure Database for MySQL サーバーを作成するには、次の手順に従います。

1- Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2- [新規] ページで **[データベース]** を選択し、[データベース] ページで **[Azure Database for MySQL]** を選択します。

> Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使用して作成されます。 データベースは、Azure リソース グループ内と Azure Database for MySQL サーバー内に作成されます。

![新しいサーバーの作成](./media/howto-create-manage-server-portal/create-new-server.png)

3- Azure Database for MySQL のフォームに次の情報を入力します。

| **フォーム フィールド** | **フィールドの説明** |
|----------------|-----------------------|
| *[サーバー名]* | azure-mysql (サーバー名はグローバルに一意である必要があります) |
| *サブスクリプション* | MySQLaaS (ドロップダウンから選択します) |
| *[リソース グループ]* | myresource (新しいリソース グループを作成するか既存のリソース グループを使用します) |
| *[サーバー管理者ログイン]* | myadmin (管理者アカウント名をセットアップします) |
| *パスワード* | 管理者アカウントのパスワードをセットアップします |
| *パスワードの確認* | 管理者アカウントのパスワードを確認します |
| *場所* | 北ヨーロッパ (北ヨーロッパ～米国西部を選択します) |
| *バージョン* | 5.6 (Azure Database for MySQL サーバーのバージョンを選択します) |

4- **[価格レベル]** をクリックして、新しいサーバーのサービス レベルとパフォーマンス レベルを指定します。 コンピューティング ユニット数は、Basic レベルなら 50 から 100、Standard レベルなら 100 から 200 で構成でき、ストレージは各サービス レベルの最大容量に基づいて追加できます。 このハウツー ガイドでは、50 コンピューティング ユニットと 50 GB を選択します。 **[OK]** をクリックして選択を保存します。
![サーバーの価格レベルの作成](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5- **[作成]** をクリックし、サーバーをプロビジョニングします。 プロビジョニングには数分かかります。

> **[ダッシュボードにピン留めする]** にチェックを入れると、デプロイを追跡しやすくなります。
> [!NOTE]
> ストレージは Basic レベルでは最大 1000 GB、Standard レベルでは 10000 GB までサポートされますが、パブリック プレビューでは一時的に 1000 GB に制限されます。 
</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの更新
新しいサーバーをプロビジョニングした後、既存のサーバーを編集して管理者パスワードをリセットする、またはコンピューティング ユニットを変更することで スケール アップまたはダウンする、という 2 つの選択肢があります。

### <a name="change-the-administrator-user-password"></a>管理者ユーザー パスワードを変更する
1- サーバーの **[概要]** ブレードで、**[パスワードのリセット]** をクリックしたあと、パスワードを入力して確認ウィンドウで確認します。

2- ![[パスワードのリセット]](./media/howto-create-manage-server-portal/reset-password.png) ウィンドウで、以下の図のように新しいパスワードを入力し、そのパスワードを確認します。

3- **[OK]** をクリックして新しいパスワードを保存します。

### <a name="scale-updown-by-changing-compute-units"></a>コンピューティング ユニットを変更して スケール アップまたはダウンする

1- サーバー ブレードで、**[設定]** 内の **[価格レベル]** をクリックし、Azure Database for MySQL サーバーの [価格レベル] ブレードを開きます。

2- 「**Azure Database for MySQL サーバーの作成**」の手順 4 に従って、同じ価格レベルのコンピューティング ユニットを変更します。

## <a name="delete-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの削除

1- サーバーの **[概要]** ブレードで、**[削除]** コマンド ボタンをクリックし、[削除中] 確認ブレードを開きます。

2- もう一度確認するため、ブレードの入力ボックスに正しいサーバー名を入力します。

3- **[削除]** ボタンをもう一度クリックし、削除中の動作を確認して通知バーに “Deleting success (削除完了)” の表示が出るまで待ちます。

## <a name="list-the-azure-database-for-mysql-databases"></a>Azure Database for MySQL データベースの一覧表示
サーバーの **[概要]** ブレードで、下にスクロールして下部にあるデータベースのタイルを表示します。 すべてのデータベースが表に一覧表示されます。 **[削除]** コマンド ボタンをクリックし、[削除中] 確認ブレードを開きます。

![データベースの表示](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの詳細を表示する
サーバー ブレードの **[設定]** 内にある **[プロパティ]** をクリックして、**[プロパティ]** ブレードを開きます。 ここで、そのサーバーについてのすべての詳細情報を確認できます。

## <a name="next-steps"></a>次のステップ

[クイックスタート: Azure Portal を使用した Azure Database for MySQL サーバーの作成](./quickstart-create-mysql-server-database-using-azure-portal.md)

