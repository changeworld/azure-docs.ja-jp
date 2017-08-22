---
title: "クイック スタート: Azure Database for MySQL サーバーの作成 - Azure Portal | Microsoft Docs"
description: "この記事では、Azure Portal を使用して、5 分程度ですばやくサンプルの Azure Database for MySQL サーバーを作成する手順を説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Azure Portal を使用した Azure Database for MySQL サーバーの作成
Azure Database for MySQL は、高可用性 MySQL データベースをクラウドで実行、管理、および拡張することができる、管理されたサービスです。 このクイックスタートでは、Azure Portal を使用して Azure Database for MySQL サーバーを作成する方法を説明します。所要時間は約 5 分です。 

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン
Web ブラウザーを開いて、[Microsoft Azure Portal](https://portal.azure.com/) にアクセスします。 資格情報を入力してポータルにサインインします。 既定のビューはサービス ダッシュボードです。

## <a name="create-azure-database-for-mysql-server"></a>Azure Database for MySQL サーバーの作成
Azure Database for MySQL サーバーは、定義済みの一連の[コンピューティング リソースとストレージ リソース](./concepts-compute-unit-and-storage.md)を使って作成されます。 サーバーは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内に作成されます。

Azure Database for MySQL サーバーを作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[新規]** ボタン (+) をクリックします。

2. **[新規]** ページで **[データベース]** を選択し、**[データベース]** ページで **[Azure Database for MySQL]** を選択します。 [新規] ページの検索ボックスに「**MySQL**」と入力すれば、サービスをすばやく探せます。
![Azure Portal - 新規 - データベース - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. 前の画像で示されているように、新規サーバーの詳細フォームに次の情報を入力します。

    **設定** | **推奨値** | **フィールドの説明** 
    ---|---|---
    サーバー名 | myserver4demo | Azure Database for MySQL サーバーを識別する一意の名前を選択します。 ドメイン名 *mysql.database.azure.com* が、接続先のアプリケーションに対して指定するサーバー名に追加されます。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、3 ～ 63 文字にする必要があります。
    [サブスクリプション] | 該当するサブスクリプション | サーバーに使用する Azure サブスクリプション。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となっている適切なサブスクリプションを選択してください。
    リソース グループ | myresourcegroup | 新しいリソース グループ名を作成するか、サブスクリプションの既存のリソース グループ名を選択することができます。
    サーバー管理者のログイン | myadmin | サーバーに接続するときに使用する独自のログイン アカウントを作成します。 管理者のログイン名に "azure_superuser"、"admin"、"administrator"、"root"、"guest"、"public" は使用できません。
    パスワード | *<任意>* | サーバー管理者アカウントの新しいパスワードを作成します。 8 ～ 128 文字である必要があります。 パスワードには、英大文字、英小文字、数字 (0 ～ 9)、英数字以外の文字 (!、$、#、% など) のうち、3 つのカテゴリの文字が含まれている必要があります。
    パスワードの確認 | *<任意>*| 管理者アカウントのパスワードを確認します。
    場所 | *ユーザーに最も近いリージョン*| ユーザーや他の Azure アプリケーションに最も近い場所を選択します。
    バージョン | *最新バージョンを選択します*| 特別な要件がない限り、最新バージョンを選択します。
    価格レベル | **Basic**、**50 コンピューティング ユニット**、**50 GB** | **[価格レベル]** をクリックして、新しいデータベースのサービス レベルとパフォーマンス レベルを指定します。 上部のタブで **Basic レベル**を選択します。 **[コンピューティング ユニット]** スライダーの左の端をクリックして、このクイックスタートで使用可能な最小量に値を調整します。 **[OK]** をクリックして、価格レベルの選択を保存します。 次のスクリーンショットをご覧ください。
    [ダッシュボードにピン留めする] | ○ | **[ダッシュボードにピン留めする]** オプションをオンにすると、Azure Portal のフロント ダッシュボード ページでサーバーを追跡しやすくなります。

    > [!IMPORTANT]
    > ここで指定するサーバー管理者のログイン名とパスワードは、このクイックスタートの後半でサーバーとそのデータベースにログインするために必要です。 後で使用するために、この情報を覚えておくか、記録しておきます。
    > 

    ![Azure Portal - フォームの必須項目を入力して MySQL を作成](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  **[作成]** をクリックしてサーバーをプロビジョニングします。 プロビジョニングには数分から最大で 20 分かかります。
   
5.  ツール バーの **[通知]** (ベル アイコン) をクリックして、デプロイ プロセスを監視します。

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成

Azure Database for MySQL サービスでは、サーバーレベルでファイアウォールが作成されます。 このファイアウォールにより、外部のアプリケーションやツールから、サーバーまたはサーバー上のすべてのデータベースへの接続が禁止されます。接続を許可するためには、特定の IP アドレスに対して、ファイアウォールを開放するファイアウォール規則を作成する必要があります。 

1.  デプロイが完了した後、サーバーを探します。 必要に応じて、検索することができます。 たとえば、左側のメニューの **[すべてのリソース]** をクリックし、サーバー名 (例の *myserver4demo* など) を入力して、新しく作成されたサーバーを検索します。 検索結果に一覧表示されたサーバー名をクリックします。 サーバーの **[概要]** ページが開き、さらに多くの構成オプションが表示されます。

2. サーバーのページで、**[接続のセキュリティ]** を選択します。

3.  **[ファイアウォール規則]** という見出しの下で、**[規則名]** 列の空白のテキスト ボックス内をクリックし、ファイアウォール規則の作成を開始します。 

    このクイックスタートでは、各列のテキスト ボックスに以下の値を入力することで、サーバーへのすべての IP アドレスを許可します。

    規則の名前 | 開始 IP | 終了 IP 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. **[接続のセキュリティ]** ページの上部のツール バーで、**[保存]** をクリックします。 しばらく待って、接続のセキュリティの更新が正常に終了したことを示す通知が表示されたことを確認してから続行してください。

    > [!NOTE]
    > Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続しようとしても、ポート 3306 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。
    > 

## <a name="get-the-connection-information"></a>接続情報の取得
データベース サーバーに接続するには、サーバーの完全な名前と管理者ログイン資格情報を思い出す必要があります。 これらの値は、クイックスタートの記事の前の方でメモしてあると思われます。 メモしていなかった場合は、Azure Portal で、サーバーの **[概要]** ページまたは **[プロパティ]** ページからサーバー名とログイン情報を簡単に見つけることができます。

1. サーバーの **[概要]** ページを開きます。 **[サーバー名]** と **[サーバー管理者ログイン名]** の値を書き留めておきます。 
    各フィールドの上にカーソルを合わせると、テキストの右側にコピー アイコンが表示されます。 必要に応じてコピー アイコンをクリックして値をコピーします。

    この例では、サーバー名は *myserver4demo.mysql.database.azure.com*、サーバー管理者ログインは *myadmin@myserver4demo* です。

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>mysql コマンドライン ツールを使用して MySQL に接続する
Azure Database for MySQL サーバーに接続するために使用できるアプリケーションは多数あります。 まず、サーバーに接続する方法を示すために、[mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) コマンドライン ツールを使用してみましょう。  追加のソフトウェアをインストールしなくても、ここで説明するように、Web ブラウザーと Azure Cloud Shell を使用できます。 自分のマシンに mysql ユーティリティがローカルにインストールしてある場合は、そこからも接続できます。

1. Azure Portal Web ページの右上にあるターミナルのアイコン ( >_ ) で Azure Cloud Shell を起動します。

2. Azure Cloud Shell がブラウザーで開き、bash シェル コマンドを入力できます。

    ![コマンド プロンプト - mysql コマンド ラインの例](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Cloud Shell プロンプトで、緑色のプロンプトに mysql コマンド ラインを入力して Azure Database for MySQL サーバーに接続します。

    mysql ユーティリティで Azure Database for MySQL サーバーに接続するには、次の形式を使用します。
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    たとえば、次のコマンドは、サンプル サーバーに接続します。
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    mysql パラメーター |推奨値|Description
    ---|---|---
    --host | *サーバー名* | 前に Azure Database for MySQL を作成したときに使用したサーバー名の値を指定します。 示されているサンプル サーバーは、myserver4demo.mysql.database.azure.com です。 例で示されているように、完全修飾ドメイン名 (\*.mysql.database.azure.com) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。 
    --user | *サーバー管理者ログイン名* |前に Azure Database for MySQL を作成したときに指定したサーバー管理者ログイン ユーザー名を入力します。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。  形式は *username@servername* です。
    --password | "*プロンプトが表示されるまで待つ*" | コマンドを入力すると、"パスワードの入力" を求めるプロンプトが表示されます。 プロンプトが表示されたら、サーバーの作成時に指定したのと同じパスワードを入力してください。  入力したパスワードの文字は bash プロンプトには表示されないことに注意してください。 認証して接続するには、すべての文字を入力してから Enter キーを押します。

   接続されると、コマンドの入力を求める `mysql>` プロンプトが mysql ユーティリティによって表示されます。 

    mysql の出力例:
    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Azure Cloud Shell の IP アドレスを許可するようにファイアウォールが構成されていない場合、次のエラーが発生します。
    >
    > エラー 2003 (28000): IP アドレス 123.456.789.0 のクライアントはこのサーバーへのアクセスが許可されていません。
    >
    > エラーを解決するには、サーバーの構成が、この記事の「*サーバーレベルのファイアウォール規則の構成*」セクションの手順と一致していることを確認してください。

4. サーバーの状態を見て正常に接続していることを確認します。 接続したら、mysql> プロンプトで「`status`」と入力します。
    ```sql
    status
    ```

   > [!TIP]
   > その他のコマンドについては、「[MySQL 5.7 リファレンス マニュアル - 4.5.1 章](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)」を参照してください。

5.  mysql> プロンプトに次のコマンドを入力して空のデータベースを作成します。
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    このコマンドは、完了するまでに少し時間がかかる場合があります。 

    Azure Database for MySQL サーバー内には 1 つまたは複数のデータベースを作成できます。 サーバーごとに 1 つのデータベースを作成してすべてのリソースを利用するか、複数のデータベースを作成してリソースを共有することができます。 作成できるデータベース数に制限はありませんが、複数のデータベースは同一のサーバー リソースを共有します。 

6. mysql> プロンプトに次のコマンドを入力してデータベースを一覧表示します。

    ```sql
    SHOW DATABASES;
    ```

7.  「`\q`」と入力し、Enter キーを押して mysql ツールを終了します。 終了したら Azure Cloud Shell を閉じることができます。

これで、Azure Database for MySQL に接続し、空のユーザー データベースを作成することができました。 次のセクションに進み、広く使われているもう 1 つのツール、MySQL Workbench で同じサーバーに接続する同様の練習をしましょう。

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI ツールを使用したサーバーへの接続
GUI ツール MySQL Workbench を使用して Azure MySQL サーバーに接続する手順は次のとおりです。

1.  クライアント コンピューターで MySQL Workbench アプリケーションを起動します。 MySQL Workbench は[ここ](https://dev.mysql.com/downloads/workbench/)からダウンロードしてインストールできます。

2.  **[Setup New Connection]** ダイアログ ボックスで、次の情報を **[Parameters]** タブに入力します。

    ![新しい接続のセットアップ](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **設定** | **推奨値** | **フィールドの説明** |
    |---|---|---|
    |   接続名 | Demo Connection | この接続のラベルを指定します。 |
    | 接続方法 | Standard (TCP/IP) | Standard (TCP/IP) で十分です。 |
    | ホスト名 | *サーバー名* | 前に Azure Database for MySQL を作成したときに使用したサーバー名の値を指定します。 示されているサンプル サーバーは、myserver4demo.mysql.database.azure.com です。 例で示されているように、完全修飾ドメイン名 (\*.mysql.database.azure.com) を使用します。 サーバー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。  |
    | Port | 3306 | Azure Database for MySQL に接続する場合は、常にポート 3306 を使用します。 |
    | ユーザー名 |  *サーバー管理者ログイン名* | 前に Azure Database for MySQL を作成したときに指定したサーバー管理者ログイン ユーザー名を入力します。 この例のユーザー名は myadmin@myserver4demo です。 ユーザー名を覚えていない場合は、前のセクションの手順に従って接続情報を取得します。 形式は *username@servername* です。
    | パスワード | パスワード | [Store in Vault \(コンテナーに保存\)] ボタンをクリックしてパスワードを保存します。 |

    **[Test Connection] \(接続のテスト)** をクリックして、すべてのパラメーターが正しく構成されているかをテストします。 [OK] をクリックして接続を保存します。 

    > [!NOTE]
    > ご使用のサーバーには SSL が既定で適用されるため、正常に接続するためには追加の構成が必要です。 「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](./howto-configure-ssl.md)」を参照してください。  このクイックスタートで SSL を無効にしたい場合は、Azure Portal にアクセスし、[接続のセキュリティ] ページをクリックして、[強制 SSL 接続] トグル ボタンをオフにして無効にします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ
クイックスタートで作成したリソースをクリーンアップするには、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md) (そのリソース グループ内のすべてのリソースを含む) を削除する方法と、1 つのサーバー リソースだけを削除 (他のリソースをそのまま維持) する方法とがあります。

> [!TIP]
> このコレクションの他のクイックスタートは、このクイックスタートに基づいています。 引き続きクイック スタートの作業を行う場合は、このクイック スタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。
>

新しく作成したサーバーを含むリソース グループ全体を削除する手順は次のとおりです。
1.  Azure Portal で目的のリソース グループを探します。 Azure Portal の左側のメニューから **[リソース グループ]** をクリックし、リソース グループの名前 (この例の **myresourcegroup** など) をクリックします。
2.  リソース グループ ページで **[削除]** をクリックします。 次に、リソース グループの名前 (この例の **myresourcegroup** など) をテキスト ボックスに入力して削除の確認を行い、**[削除]** をクリックします。

または、新しく作成したサーバーを削除する場合は、次の手順に従います。
1.  Azure Portal で目的のサーバーを探します (まだ開いていない場合)。 Azure Portal の左側のメニューにある **[すべてのリソース]** をクリックし、自分が作成したサーバーを探します。
2.  **[概要]** ページで、上部のウィンドウの **[削除]** ボタンをクリックします。
![Azure Database for MySQL - サーバーの削除](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  削除するサーバーの名前を確認し、影響を受けるデータベースをその下に表示します。 テキスト ボックスにサーバー名 (この例の **myserver4demo** など) を入力し、**[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [最初の Azure Database for MySQL データベースを設計する](./tutorial-design-database-using-portal.md)


