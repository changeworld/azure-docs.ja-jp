---
title: "クイック スタート: Azure Database for MySQL サーバーの作成 - Azure Portal | Microsoft Docs"
description: "この記事では、Azure Portal を使用して、5 分程度ですばやくサンプルの Azure Database for MySQL サーバーを作成する手順を説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: hero-article
ms.date: 06/14/2017
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 3e0aa1ecccdb55b75dd332818e09b21431716fdc
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal を使用した Azure Database for MySQL サーバーの作成
この記事では、Azure Portal を使用して、5 分程度で Azure Database for MySQL サーバーを作成する手順を説明します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン
Web ブラウザーを開いて、[Microsoft Azure Portal](https://portal.azure.com/) にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2. **[新規]** ページで **[データベース]** を選択し、**[データベース]** ページで **[Azure Database for MySQL]** を選択します。 [新規] ページの検索ボックスに「**MySQL**」と入力すれば、サービスをすばやく探せます。
![Azure Portal - 新規 - データベース - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 前の画像で示されているように、新規サーバーの詳細フォームに次の情報を入力します。

| **設定** | **推奨値** | **フィールドの説明** |
|---|---|---|
| *[サーバー名]* | myserver4demo  | サーバー名はグローバルに一意である必要があります。 |
| *サブスクリプション* | mysubscription | ドロップダウン リストからサブスクリプションを選択します。 |
| *[リソース グループ]* | myresourcegroup | リソース グループを作成するか、既存のものを使用します。 |
| *[サーバー管理者ログイン]* | myadmin | MySQL エンジンで管理者となるアカウント名を指定します。 |
| *パスワード* |  | 強力な管理者アカウントのパスワードを設定します。 |
| *パスワードの確認* |  | 管理者アカウントのパスワードを確認します。 |
| *場所* |  | 使用可能なリージョンを選択します。 |
| *バージョン* | 5.7 | 最新バージョンを選択します。 |
| *価格レベル* | Basic、50 コンピューティング ユニット、50 ストレージ (GB)  | **価格レベル**、**コンピューティング ユニット**、**ストレージ (GB)** を選択して **[OK]** をクリックします。 |
| *ダッシュボードにピン留めする* | ○ | サーバーを後で簡単に見つけることができるので、このチェック ボックスをオンすることをお勧めします。 |

   **[価格レベル]** をクリックして、新しいデータベースの価格レベルとパフォーマンス レベルを指定します。 このクイック スタートでは、Basic レベル、50 個のコンピューティング ユニット、および 50 GB のストレージを選択します。 **[OK]** をクリックして価格レベルを保存します。
   
   ![Azure Portal - フォームの必須項目を入力して MySQL を作成](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

   **[作成]**をクリックします。 1 ～ 2 分で、新しい Azure Database for MySQL サーバーがクラウドで実行されます。 ツール バーの **[通知]** ボタン (ベル型のアイコン) をクリックすると、デプロイ プロセスを監視できます。

## <a name="configure-the-firewall"></a>ファイアウォールの構成
初めて Azure Database for MySQL に接続する前に、ファイアウォールを構成し、クライアントのパブリック ネットワーク IP アドレス (またはその範囲) をホワイトリストに追加します。

1. デプロイが完了したら、左側のメニューの **[すべてのリソース]** をクリックし、サーバー名「**myserver4demo**」を入力して、新しく作成したサーバーを検索します。 検索結果に示されたサーバー名をクリックします。 サーバーの [概要] ページが開き、さらに多くの構成オプションが表示されます。

2. サーバーのブレードで、**[接続のセキュリティ]** を選択します。

3. **[自分の IP を追加]** をクリックして、ご使用のローカル コンピューターの IP アドレスを追加するか、IP アドレスの範囲を構成します。 規則を作成したら、忘れずに **[保存]** をクリックしてください。
  ![Azure Portal - ファイアウォール規則の追加と保存](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

## <a name="get-connection-information"></a>接続情報の取得
Azure Portal で、Azure MySQL サーバーの完全修飾ドメイン名を取得します。 この完全修飾ドメイン名は、**mysql.exe** コマンド ライン ツールでサーバーに接続する際に使用します。

1.  [Azure Portal](https://portal.azure.com/) の左側のメニューにある **[すべてのリソース]** をクリックし、Azure Database for MySQL サーバーをクリックします。

2.  **[プロパティ]**をクリックします。 **[サーバー名]** と **[サーバー管理者ログイン]** の値をメモしておきます。
この例では、サーバー名は *myserver4demo.mysql.database.azure.com*、サーバー管理者ログインは *myadmin@myserver4demo* です。

## <a name="connect-to-the-server-using-mysqlexe-command-line-tool"></a>mysqlexe コマンドライン ツールを使用したサーバーへの接続
[mysql コマンドライン ツール](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)を使用して、Azure Database for MySQL サーバーへの接続を確立します。 mysql コマンドライン ツールは、Azure Cloud Shell を使用してブラウザーで実行するか、ローカルにインストールされている mysql ツールを使用して自分のコンピューターから起動できます。 Azure Cloud Shell を起動するには、この記事のコード ブロックにある `Try It` ボタンをクリックするか、[Azure Portal](https://portal.azure.com) にアクセスして右上のツールバーにある `>_` アイコンをクリックします。 

1. 接続するためのコマンドを入力します。
```azurecli-interactive
mysql -h myserver4demo.mysql.database.azure.com -u myadmin@myserver4demo -p
```

2. サーバーの状態を見て正常に接続していることを確認します。 接続したら、mysql> プロンプトで「`status`」と入力します。
```sql
status
```

   ![コマンド プロンプト - mysql コマンド ラインの例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

   > [!TIP]
   > その他のコマンドについては、「[MySQL 5.7 リファレンス マニュアル - 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)」を参照してください。

3. mysql> プロンプトで「`CREATE DATABASE`」と入力して空のデータベースを作成します。

   ```sql
   CREATE DATABASE quickstartdb;
   ```

   Azure Database for MySQL サーバー内には 1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを利用するか、複数のデータベースを作成してリソースを共有することができます。 作成できるデータベース数に制限はありませんが、複数のデータベースは同一のサーバー リソースを共有します。  

4. mysql> プロンプトで `SHOW DATABASES` コマンドを入力してデータベースをリストします。

   ```sql
   SHOW DATABASES;
   ```

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI ツールを使用したサーバーへの接続
1.  クライアント コンピューターで MySQL Workbench アプリケーションを起動します。 MySQL Workbench は[ここ](https://dev.mysql.com/downloads/workbench/)からダウンロードしてインストールできます。

2.  **[Setup New Connection]** ダイアログ ボックスで、次の情報を **[Parameters]** タブに入力します。

   ![新しい接続のセットアップ](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

| **設定** | **推奨値** | **フィールドの説明** |
|---|---|---|
|   *Connection Name* | Demo Connection| この接続のラベルを指定します。 |
| *Connection Method* | Standard (TCP/IP) | Standard (TCP/IP) で十分です。 |
| *Hostname* | myserver4demo.mysql.database.azure.com | サーバーの完全修飾サーバー名を使用します。 |
| *ポート* | 3306 | 既定のポート 3306 を使用します。 |
| *ユーザー名* | myadmin@myserver4demo  | 前にメモしておいたサーバー管理者ログインに @ 文字とサーバー名を付加したものを使用します。 |
| *パスワード* | パスワード | [Store in Vault \(コンテナーに保存\)] ボタンをクリックしてパスワードを保存します。 |

**[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。 [OK] をクリックして接続を保存します。 

> [!NOTE]
> ご使用のサーバーには SSL が既定で適用されるため、正常に接続するためには追加の構成が必要です。 「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。  このクイック スタートでは SSL を無効にしたい場合は、Azure Portal にアクセスし、[接続のセキュリティ] ページをクリックして、[強制 SSL 接続] トグル ボタンをオフにして無効にします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を削除して、クイックスタートで作成したすべてのリソースをクリーンアップします。

> [!TIP]
> このコレクションの他のクイック スタートは、このクイック スタートに基づいています。 引き続きクイック スタートの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure ポータルで削除してください。

1.  Azure ポータルの左側のメニューから、**[リソース グループ]**、**[myResourceGroup]** の順にクリックします。
2.  リソース グループのページで **[削除]** をクリックし、テキスト ボックスに「**myResourceGroup**」と入力してから [削除] をクリックします。

新しく作成したサーバーを削除する場合は、次のようにします。
1.  Azure ポータルの左側のメニューにある PostgreSQL サーバーをクリックし、作成したばかりのサーバーを検索します。
2.  [概要] ページで、上部のウィンドウの [削除] ボタンをクリックします。![Azure Database for MySQL - サーバーの削除](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  削除するサーバーの名前を確認し、影響を受けるデータベースをその下に表示します。 テキスト ボックスに「**myserver4demo**」と入力し、[削除] をクリックします。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の Azure Database for MySQL データベースを設計する](./tutorial-design-database-using-portal.md)


