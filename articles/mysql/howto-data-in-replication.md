---
title: Azure Database for MySQL にデータをレプリケートするデータイン レプリケーションを構成する
description: この記事では、Azure Database for MySQL のデータイン レプリケーションをセットアップする方法について説明します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: e099597eae419653a2a40c7f01ee7abbbc4657f0
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294423"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL のデータイン レプリケーションを構成する方法

この記事では、プライマリ サーバーとレプリカ サーバーを構成することによって、Azure Database for MySQL サービスのデータイン レプリケーションをセットアップする方法について説明します。 データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の プライマリ MySQL サーバーから Azure Database for MySQL サービスのレプリカにデータを同期することができます。 

この記事は、少なくとも MySQL サーバーと MySQL データベースに関して、ある程度の使用経験があることを前提としています。

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>レプリカとして使用する MySQL サーバーの作成

1. 新しい Azure Database for MySQL サーバーを作成する

   新しい MySQL サーバーを作成します (例:  "replica.mysql.database.azure.com")。 サーバーの作成については、「[Azure portal を使用した Azure Database for MySQL サーバーの作成](quickstart-create-mysql-server-database-using-azure-portal.md)」を参照してください。 このサーバーは、データイン レプリケーションにおける "レプリカ" サーバーになります。

   > [!IMPORTANT]
   > Azure Database for MySQL サーバーは、汎用とメモリ最適化のどちらかの価格レベルで作成する必要があります。
   > 

2. 同じユーザー アカウントとそれに対応する権限を作成する

   レプリカ サーバーにプライマリ サーバーのユーザー アカウントはレプリケートされません。 レプリカ サーバーへのアクセス権をユーザーに与えることを検討している場合、新しく作成した Azure Database for MySQL サーバーに、すべてのアカウントとそれらに対応する権限を手動で作成する必要があります。

## <a name="configure-the-primary-server"></a>プライマリ サーバーの構成
次の手順では、仮想マシンでオンプレミスでホストされる MySQL サーバーを準備して構成するか、データイン レプリケーションのために他のクラウド プロバイダーによってホストされるデータベース サービスを準備して構成します。 このサーバーは、データイン レプリケーションにおける "プライマリ" サーバーになります。 

1. バイナリ ログを有効にする

   プライマリでバイナリ ログが有効になっているかどうかを、次のコマンドを実行してチェックします。 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   変数 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) の戻り値が "ON" であった場合、サーバーでバイナリ ログが有効になっています。 

   `log_bin` の戻り値が "OFF" の場合は、my.cnf ファイルを編集してバイナリ ログを有効 (`log_bin=ON`) にし、サーバーを再起動して変更を反映します。

2. プライマリ サーバーの設定

   データイン レプリケーションでは、プライマリ サーバーとレプリカ サーバーとの間でパラメーター `lower_case_table_names` を一致させる必要があります。 Azure Database for MySQL では、このパラメーターが既定で 1 になっています。 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 新しいレプリケーション ロールを作成し、権限をセットアップする

   レプリケーションの権限を持つように構成されたユーザー アカウントをプライマリ サーバーに作成します。 この作業には SQL コマンドのほか、MySQL Workbench などのツールを使用することができます。 レプリケートに SSL を使用するかどうかをよく考えておいてください。ユーザーを作成する際に指定する必要があります。 プライマリ サーバーに[ユーザー アカウントを追加](https://dev.mysql.com/doc/refman/5.7/en/adding-users.html)する方法については、MySQL のドキュメントを参照してください。 

   以降のコマンドでは、新たに作成したレプリケーション ロールが、プライマリのホスト マシンに限らず、任意のマシンからプライマリにアクセスできます。 そのため、create user コマンドには "syncuser@'%'" を指定しています。 [アカウント名の指定](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)について詳しくは、MySQL のドキュメントをご覧ください。

   **SQL コマンド**

   *SSL を使用したレプリケーション*

   すべてのユーザー接続に SSL を要求するには、次のコマンドを使用してユーザーを作成します。 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL を使用しないレプリケーション*

   必ずしもすべての接続に SSL が必要でない場合は、次のコマンドを使用してユーザーを作成します。

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   レプリケーション ロールを MySQL Workbench で作成するには、**[Management]\(管理\)** パネルから **[Users and Privileges]\(ユーザーと権限\)** パネルを開きます。 次に、**[Add Account]\(アカウントの追加\)** をクリックします。 
 
   ![[Users and Privileges]\(ユーザーと権限\)](./media/howto-data-in-replication/users_privileges.png)

   **[Login Name]\(ログイン名\)** フィールドにユーザー名を入力します。 

   ![同期ユーザー](./media/howto-data-in-replication/syncuser.png)
 
   **[Administrative Roles]\(管理者ロール\)** パネルをクリックし、**[Global Privileges]\(グローバル権限\)** の一覧から **[Replication Slave]\(レプリケーション スレーブ\)** を選択します。 次に、**[Apply]\(適用\)** をクリックして、レプリケーション ロールを作成します。

   ![[Replication Slave]\(レプリケーション スレーブ\)](./media/howto-data-in-replication/replicationslave.png)


4. プライマリ サーバーを読み取り専用モードに設定する

   データベースのダンプを開始する前に、サーバーを読み取り専用モードにする必要があります。 読み取り専用モードの間、プライマリは書き込みトランザクションを一切処理できなくなります。 業務への影響を見積もり、必要であればピーク時を外して読み取り専用の時間帯をスケジュールしてください。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. バイナリ ログ ファイルの名前とオフセットを取得する

   [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) コマンドを実行して、現在のバイナリ ログ ファイルの名前とオフセットを調べます。
    
   ```sql
   show master status;
   ```
   その結果は次のようになります。 バイナリ ファイルの名前は、この後の手順で使用するので書き留めておいてください。

   ![master status の結果](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-primary-server"></a>プライマリ サーバーのダンプと復元

1. プライマリ サーバーからすべてのデータベースをダンプする

   mysqldump を使用してプライマリからデータベースをダンプすることができます。 詳細については、「[ダンプと復元](concepts-migrate-dump-restore.md)」を参照してください。 MySQL ライブラリとテスト ライブラリをダンプする必要はありません。

2. プライマリ サーバーを読み取り/書き込みモードに設定する

   データベースのダンプ後、プライマリ MySQL サーバーを読み取り/書き込みモードに変更します。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. ダンプ ファイルを新しいサーバーに復元する

   Azure Database for MySQL サービスに作成したサーバーにダンプ ファイルを復元します。 ダンプ ファイルを MySQL サーバーに復元する方法については、「[ダンプと復元](concepts-migrate-dump-restore.md)」を参照してください。 大きいダンプ ファイルは、レプリカ サーバーと同じリージョンにある Azure 内の仮想マシンにアップロードしてください。 そのファイルを仮想マシンから Azure Database for MySQL サーバーに復元します。

## <a name="link-primary-and-replica-servers-to-start-data-in-replication"></a>プライマリ サーバーとレプリカ サーバーをリンクしてデータイン レプリケーションを開始する

1. プライマリ サーバーを設定する

   データイン レプリケーションの機能は、すべてストアド プロシージャによって実現されています。 「[データイン レプリケーションのストアド プロシージャ](reference-data-in-stored-procedures.md)」で、すべてのプロシージャをご覧いただけます。 これらのストアド プロシージャは、MySQL シェルまたは MySQL Workbench で実行できます。 

   2 つのサーバーをリンクさせてレプリケーションを開始するには、Azure DB for MySQL サービスにあるレプリケーション先のレプリカ サーバーにログインし、外部インスタンスをプライマリ サーバーとして設定します。 この設定は、Azure DB for MySQL サーバーで `mysql.az_replication_change_primary` ストアド プロシージャを使用して行います。

   ```sql
   CALL mysql.az_replication_change_primary('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: プライマリ サーバーのホスト名
   - master_user: プライマリ サーバーのユーザー名
   - master_password: プライマリ サーバーのパスワード
   - master_log_file: `show master status` を実行することによって得たバイナリ ログ ファイルの名前
   - master_log_pos: `show master status` を実行することによって得たバイナリ ログの位置
   - master_ssl_ca: CA 証明書のコンテキスト。 SSL を使用していない場合は、空の文字列を渡します。
       - このパラメーターは変数で渡すことをお勧めします。 詳細については、以降の例を参照してください。

   **例**

   *SSL を使用したレプリケーション*

   変数 `@cert` は、次の MySQL コマンドを実行して作成します。 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   SSL を使用したレプリケーションを、ドメイン "companya.com" にホストされたプライマリ サーバーと Azure Database for MySQL にホストされたレプリカ サーバーとの間でセットアップします。 このストアド プロシージャはレプリカで実行します。 

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
   ```
   *SSL を使用しないレプリケーション*

   SSL を使用しないレプリケーションを、ドメイン "companya.com" にホストされたプライマリ サーバーと Azure Database for MySQL にホストされたレプリカ サーバーとの間でセットアップします。 このストアド プロシージャはレプリカで実行します。

   ```sql
   CALL mysql.az_replication_change_primary('primary.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
   ```

2. レプリケーションを開始する

   `mysql.az_replication_start` ストアド プロシージャを呼び出してレプリケーションを開始します。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. レプリケーションの状態をチェックする

   レプリカ サーバーで [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) コマンドを呼び出して、レプリケーションの状態を確認します。
    
   ```sql
   show slave status;
   ```

   `Slave_IO_Running` と `Slave_SQL_Running` の状態が "yes" で、`Seconds_Behind_Master` の値が "0" であれば、レプリケーションは正常に機能しています。 `Seconds_Behind_Master` は、レプリカの遅れの程度を示しています。 この値が "0" 以外である場合、レプリカで更新処理が実行されていることを意味します。 

## <a name="other-stored-procedures"></a>その他のストアド プロシージャ

### <a name="stop-replication"></a>レプリケーションの停止

プライマリ サーバーとレプリカ サーバーとの間のレプリケーションを停止するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>レプリケーション関係の解除

プライマリ サーバーとレプリカ サーバーの関係を解除するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_remove_primary;
```

### <a name="skip-replication-error"></a>レプリケーション エラーのスキップ

レプリケーション エラーをスキップして、レプリケーションを続行するには、次のストアド プロシージャを使用します。
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>次の手順
- Azure Database for MySQL の[データイン レプリケーション](concepts-data-in-replication.md)について確認します。 