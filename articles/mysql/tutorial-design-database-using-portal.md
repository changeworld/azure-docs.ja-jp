---
title: "最初の Azure Database for MySQL データベースを設計する - Azure Portal | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal を使用して、Azure Database for MySQL サーバーとデータベースを作成および管理する方法について説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: portal
ms.date: 05/10/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2958486a0ec055cf1fe334e97389536b2c2bb01f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="design-your-first-azure-database-for-mysql-database"></a>最初の Azure Database for MySQL データベースを設計する

Azure Database for MySQL は、高可用性 MySQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 Azure Portal を使用して、簡単にサーバーを管理し、データベースを設計することができます。

このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明します。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * [mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする
Web ブラウザーを開いて [Microsoft Azure Portal](https://portal.azure.com/) にアクセスします。 資格情報を入力してポータルにログインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使って作成されます。 サーバーは、[Azure リソース グループ](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview)内に作成されます。

1.    **[データベース]** -> **[MySQL]** に移動します。 **[データベース]** カテゴリ内に MySQL Server が見つからない場合は、**[すべて表示]** をクリックして、使用可能なすべてのデータベース サービスを表示します。 検索ボックスに「**MySQL**」と入力すれば、サービスをすばやく探せます。
![2-1 MySQL への移動](./media/tutorial-design-database-using-portal/2_1-Navigate-to-MySQL.png)

2.    **MySQL** アイコンをクリックし、**[作成]** をクリックします。

この例では、Azure Database for MySQL フォームに次の情報を入力します。

| **フォームのフィールド** | **フィールドの説明** |
|----------------|-----------------------|
| *[サーバー名]* | mysqlserver4demo (サーバー名はグローバルに一意) |
| *サブスクリプション* | MySQLaaS (ドロップダウン リストから選択) |
| *[リソース グループ]* | myresource (リソース グループを作成するか、既存のグループを使用する) |
| *サーバー管理者ログイン* | myadmin (管理者アカウントの名前を設定) |
| *パスワード* | 管理者アカウントのパスワードを設定 |
| *パスワードの確認* | 管理者アカウントのパスワードを確認 |
| *場所* | 北ヨーロッパ (**[北ヨーロッパ]** か **[米国西部]** のいずれかを選択) |
| *バージョン* | 5.6 (MySQL サーバーのバージョンを選択) |
| *パフォーマンスの構成* | Basic (**パフォーマンス レベル**、**コンピューティング ユニット**、**ストレージ**を選択し、**[OK]** をクリック) |

**[作成]**をクリックします。 1 ～ 2 分で、クラウドで実行される新しい Azure Database for MySQL が作成されます。 ツール バーの **[通知]** ボタンをクリックすると、デプロイ プロセスを監視できます。

> [!TIP]
> Azure サービスは同じリージョン内に置き、最も近い場所を選択することをお勧めします。 また、**[ダッシュボードにピン留めする]** チェック ボックスをオンにすると、デプロイを追跡しやすくなります。

![2-2 サーバーの作成](./media/tutorial-design-database-using-portal/2_2-Create-server.png)

## <a name="configure-firewall"></a>ファイアウォールの構成
Azure Databases for MySQL は、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 クライアントから初めて Azure Database for MySQL に接続する前に、ファイアウォールを構成し、クライアントのパブリック ネットワーク IP アドレス (または IP アドレスの範囲) をホワイト リストに追加する必要があります。

1.    新しく作成したサーバーをクリックし、**[接続のセキュリティ]** をクリックします。

![3-1 接続のセキュリティ](./media/tutorial-design-database-using-portal/3_1-Connection-security.png)

2.    **自分の IP を追加**するか、またはファイアウォール規則を構成できます。 規則を作成したら、忘れずに **[保存]** をクリックしてください。

これで、mysql コマンド ライン ツールまたは MySQL Workbench GUI ツールを使用してサーバーに接続することができます。

> Azure Database for MySQL サーバーは、ポート 3306 経由で通信します。 企業ネットワーク内から接続しようとしても、ポート 3306 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、Azure MySQL サーバーに接続することはできません。

## <a name="get-connection-information"></a>接続情報の取得
Azure Portal で、Azure MySQL サーバーの完全修飾サーバー名を取得します。 この完全修飾サーバー名は、mysql コマンド ライン ツールでサーバーに接続する際に使用します。

1.    [Azure Portal](https://portal.azure.com/) の左側のメニューにある **[すべてのリソース]** をクリックし、Azure Database for MySQL サーバーをクリックします。

2.    **[プロパティ]**をクリックします。 **[サーバー名]** と **[サーバー管理者ログイン]** の値をメモしておきます。
![4-2 サーバーのプロパティ](./media/tutorial-design-database-using-portal/4_2-server-properties.png)

この例では、サーバー名は *mysql4doc.database.windows.net*、サーバー管理者ログインは *mysqladmin@mysql4doc* です。

## <a name="connect-to-the-server-using-mysql"></a>mysql を使用してサーバーに接続する
[mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 この例では、コマンドは次のようになります。
```cmd
mysql -h mysqlserver4demo.database.windows.net -u myadmin@mysqlserver4demo -p
```

## <a name="create-a-blank-database"></a>空のデータベースの作成
サーバーに接続したら、使用する空のデータベースを作成します。
```sql
CREATE DATABASE mysampledb;
```

プロンプトで次のコマンドを実行し、新しく作成したデータベースに接続を切り替えます。
```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する
Azure Database for MySQL データベースに接続する方法を説明したので、次は基本的なタスクを行う方法をいくつか説明します。

最初に、テーブルを作成してデータを読み込みます。 インベントリ情報を格納するテーブルを作成しましょう。
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む
テーブルを作成したので、次はデータを挿入します。 開いているコマンド プロンプト ウィンドウで、次のクエリを実行してデータ行を挿入します。
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

これで、先ほど作成したテーブルにサンプル データが 2 行挿入されました。

## <a name="query-and-update-the-data-in-the-tables"></a>クエリを実行し、テーブル内のデータを更新する
次のクエリを実行して、データベース テーブルから情報を取得します。
```sql
SELECT * FROM inventory;
```

さらに、テーブル内のデータを更新することもできます。
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

データを取得するとき、それに応じてデータ行が更新されます。
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する
テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure Database for MySQL では、過去最長 35 日間における任意の時点に戻り、新しいデータベースに過去のデータを復元することができます。 この新しいサーバーを使用して、削除されたデータを復元することができます。 次の手順を実行して、テーブルを追加する前の状態にサンプル データベースを復元します。

1- サーバーの Azure Database for MySQL ページで、ツール バーの **[復元]** をクリックします。 **[復元]** ページが開きます。
![10-1 データベースの復元](./media/tutorial-design-database-using-portal/10_1-restore-a-db.png)

2- **[復元]** フォームに必要な情報を入力します。

-    復元ポイント: サーバーが変更される前の日時を選択します。
-    対象サーバー: 復元先の新しいサーバー名を指定します。
-    場所: リージョンを選択することはできません。既定では、ソース サーバーと同じ場所になります。
-    価格レベル: サーバーを復元するときは、この値を変更することはできません。 ソース サーバーと同じレベルになります。
![10 2 [復元] フォーム](./media/tutorial-design-database-using-portal/10_2-restore-form.png)

3- **[OK]** をクリックして、[テーブルが削除される前の状態にサーバーを復元](./howto-restore-server-portal.md)します。 異なる時点にサーバーを復元すると、サービス レベルの保有期間内であれば、指定した時点の元サーバーと同じサーバー内に、新しいサーバーが複製されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Portal を使用して次のことを行う方法を説明しました。

> [!div class="checklist"]
> * Azure Database for MySQL の作成
> * サーバーのファイアウォールの構成
> * [mysql コマンド ライン ツール](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)を使用したデータベースの作成
> * サンプル データの読み込み
> * データのクエリを実行する
> * データの更新
> * データの復元

[Azure Portal を使用した Azure Database for MySQL ファイアウォール規則の作成と管理](./howto-manage-firewall-using-portal.md)

