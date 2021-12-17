---
title: データイン レプリケーションを構成する - Azure Database for MySQL フレキシブル サーバー
description: この記事では、Azure Database for MySQL フレキシブル サーバーのデータイン レプリケーションをセットアップする方法について説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: ee81fa18ac0f6bae6a3aefbfef60333991001dff
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812139"
---
# <a name="how-to-configure-azure-database-for-mysql-flexible-server-data-in-replication"></a>Azure Database for MySQL フレキシブル サーバーのデータイン レプリケーションを構成する方法

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、ソースとレプリカのサーバーを構成することによって、Azure Database for MySQL フレキシブル サーバーで[データイン レプリケーション](concepts-data-in-replication.md)を設定する方法について説明します。 この記事は、MySQL サーバーとデータベースに関して、ある程度の使用経験があることを前提としています。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

Azure Database for MySQL フレキシブル サービスでレプリカを作成するために、[データイン レプリケーション](concepts-data-in-replication.md)によって、オンプレミス、仮想マシン (VM)、またはクラウド データベース サービスのソース MySQL サーバーからデータが同期されます。 データイン レプリケーションは、バイナリ ログ (binlog) ファイルの位置ベースに基づいています。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。

この記事の手順を実行する前に、データイン レプリケーションの[制限事項と要件](concepts-data-in-replication.md#limitations-and-considerations)を確認してください。

## <a name="create-an-azure-database-for-mysql-flexible-server-instance-to-use-as-a-replica"></a>レプリカとして使用する Azure Database for MySQL フレキシブル サーバー インスタンスを作成する

1. Azure Database for MySQL フレキシブル サーバーの新しいインスタンスを作成します (例: `replica.mysql.database.azure.com`)。 サーバーの作成については、「[Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](quickstart-create-server-portal.md)」を参照してください。 このサーバーは、データイン レプリケーション用の "レプリカ" サーバーです。

2. 同じユーザー アカウントとそれに対応する権限を作成します。

   レプリカ サーバーにソース サーバーのユーザー アカウントはレプリケートされません。 レプリカ サーバーへのアクセス権をユーザーに与えることを検討している場合、この新しく作成した Azure Database for MySQL フレキシブル サーバーに、すべてのアカウントとそれらに対応する権限を手動で作成する必要があります。

## <a name="configure-the-source-mysql-server"></a>ソース MySQL サーバーを構成する

次の手順では、仮想マシンでオンプレミスでホストされる MySQL サーバーを準備して構成するか、データイン レプリケーションのために他のクラウド プロバイダーによってホストされるデータベース サービスを準備して構成します。 このサーバーは、データイン レプリケーション用の "ソース" となります。

1. 続行する前に、[ソース サーバーの要件](concepts-data-in-replication.md#requirements)をご確認ください。

2. ネットワーク要件 
    * ソース サーバーでポート 3306 での受信と送信の両方のトラフィックが許可されていて、それに対して **パブリック IP アドレス** が割り当てられている、または DNS にパブリックにアクセス可能である、あるいは完全修飾ドメイン名 (FQDN) があることを確かめます。

    * プライベート アクセスが使用されている場合は、ソース サーバーと、レプリカ サーバーがホストされている Vnet との間に接続が確立されていることを確認します。 
    * [ExpressRoute](../../expressroute/expressroute-introduction.md) または [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) のどちらかを使用して、オンプレミスのソース サーバーへのサイト間接続が提供されていることを確認します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。
    * レプリカ サーバーでプライベート アクセスが使用され、ソースが Azure VM の場合は、VNet 間接続が確立されていることを確認します。 VNet 間のピアリングがサポートされています。 異なるリージョンにわたる VNet 間で通信するために、VNet 間接続など他の接続方法を使用することもできます。 詳細については、[VNet 間 VPN ゲートウェイ](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)に関するページを参照してください
    * 仮想ネットワークのネットワーク セキュリティ グループ規則で、送信ポート 3306 がブロックされていないことを確認します (MySQL が Azure VM で実行されている場合は受信ポートも)。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
    * レプリカ サーバーの IP アドレスを許可するようにソース サーバーのファイアウォール規則を構成します。

  
3. バイナリ ログを有効にします。

   ソースでバイナリ ログが有効になっているかどうかを、次のコマンドを実行してチェックします。

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   変数 [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) の戻り値が "ON" であった場合、サーバーでバイナリ ログが有効になっています。

   値 "オフ" で `log_bin` が返されたとき、構成ファイル (my.cnf) にアクセスするオンプレミスまたは仮想マシン上でソース サーバーが実行されている場合、以下の手順をご利用いただけます。
   1. ソース サーバーで MySQL 構成ファイル (my.cnf) を見つけます。 例: /etc/my.cnf
   2. 構成ファイルを開いて編集し、ファイル内の **mysqld** セクションを見つけます。
   3. mysqld セクションで、次の行を追加します。

       ```bash
       log-bin=mysql-bin.log
       ```

   4. 変更を有効にするには、ソース サーバーで MySQL サービスを再起動します (または [再起動])。
   5. サーバーが再起動された後、前と同じクエリを実行して、バイナリ ログが有効になっていることを確認します。

      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```

4. ソース サーバーの設定を構成します。

   データイン レプリケーションでは、ソース サーバーとレプリカ サーバー間でパラメーター `lower_case_table_names` が一貫している必要があります。 Azure Database for MySQL フレキシブル サーバーでは、このパラメーターが既定で 1 になっています。

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. 新しいレプリケーション ロールを作成し、権限を設定します。

   レプリケーションの権限を持つように構成されたユーザー アカウントをソース サーバーに作成します。 この作業は、SQL コマンド、または MySQL Workbench などのツールを使用して行うことができます。 SSL を使用するレプリケートを計画するかどうかを検討してください。これはユーザーを作成するときに指定する必要があります。 ソース サーバーに[ユーザー アカウントを追加](https://dev.mysql.com/doc/refman/5.7/en/user-names.html)する方法については、MySQL のドキュメントを参照してください。

   次のコマンドでは、作成した新しいレプリケーション ロールを使用すると、ソース自体をホストするマシンだけでなく、任意のマシンからソースにアクセスできます。 そのため、create user コマンドには "syncuser@'%'" を指定しています。 [アカウント名の指定](https://dev.mysql.com/doc/refman/5.7/en/account-names.html)について詳しくは、MySQL のドキュメントをご覧ください。

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

   MySQL Workbench でレプリケーション ロールを作成するには、 **[Management]\(管理\)** ウィンドウで **[Users and Privileges]\(ユーザーと権限\)** を選択してから、 **[アカウントの追加]** を選択します。

   :::image type="content" source="./media/how-to-data-in-replication/users-privileges.png" alt-text="[Users and Privileges]\(ユーザーと権限\)":::

   **[Login Name]\(ログイン名\)** フィールドにユーザー名を入力します。

   :::image type="content" source="./media/how-to-data-in-replication/sync-user.png" alt-text="同期ユーザー":::

   **[Administrative Roles]\(管理者ロール\)** パネルを選択して、 **[Global Privileges]\(グローバル権限\)** の一覧から **[Replication Slave]\(レプリケーション スレーブ\)** を選択します。 次に、 **[Apply]\(適用\)** を選択して、レプリケーション ロールを作成します。

   :::image type="content" source="./media/how-to-data-in-replication/replication-slave.png" alt-text="[Replication Slave]\(レプリケーション スレーブ\)":::

6. ソース サーバーを読み取り専用モードに設定します。

   データベースのダンプを開始する前に、サーバーを読み取り専用モードにする必要があります。 読み取り専用モードの間、ソースは書き込みトランザクションを一切処理できなくなります。 業務への影響を見積もり、必要であればピーク時を外して読み取り専用の時間帯をスケジュールしてください。

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. バイナリ ログ ファイルの名前とオフセットを取得します。

   [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) コマンドを実行して、現在のバイナリ ログ ファイルの名前とオフセットを調べます。

   ```sql
    show master status;
   ```
   結果は次のようになります。 バイナリ ファイルの名前はこの後の手順で使用するため、必ずメモしておいてください。

   :::image type="content" source="./media/how-to-data-in-replication/master-status.png" alt-text="master status の結果":::

## <a name="dump-and-restore-the-source-server"></a>ソース サーバーのダンプと復元

1. Azure Database for MySQL フレキシブル サーバーにレプリケートするデータベースとテーブルを決定し、ソース サーバーからダンプを実行します。

    mysqldump を使用して、プライマリ サーバーからデータベースをダンプすることができます。 詳細については、「[ダンプと復元](../concepts-migrate-dump-restore.md)」を参照してください。 MySQL ライブラリとテスト ライブラリをダンプする必要はありません。


3. ソース サーバーを読み取り/書き込みモードに設定します。

   データベースのダンプ後、ソース MySQL サーバーを読み取り/書き込みモードに戻します。

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

4. ダンプ ファイルを新しいサーバーに復元します。

   Azure Database for MySQL フレキシブル サーバー サービスに作成したサーバーにダンプ ファイルを復元します。 ダンプ ファイルを MySQL サーバーに復元する方法については、「[ダンプと復元](../concepts-migrate-dump-restore.md)」を参照してください。 大きいダンプ ファイルは、レプリカ サーバーと同じリージョンにある Azure 内の仮想マシンにアップロードしてください。 そのファイルを仮想マシンから Azure Database for MySQL フレキシブル サーバーに復元します。

>[!Note]
>* ダンプと復元を行うときにデータベースを読み取り専用に設定したくない場合は、[mydumper/mydumper](../concepts-migrate-mydumper-myloader.md) を使用できます。

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>ソースとレプリカのサーバーをリンクしてデータイン レプリケーションを開始する

1. ソース サーバーを設定します。

   データイン レプリケーションの機能は、すべてストアド プロシージャによって実現されています。 「[データイン レプリケーションのストアド プロシージャ](../reference-stored-procedures.md)」で、すべてのプロシージャをご覧いただけます。 これらのストアド プロシージャは、MySQL シェルまたは MySQL Workbench で実行できます。

   2 つのサーバーをリンクさせてレプリケーションを開始するには、Azure DB for MySQL サービスにあるレプリケーション先のレプリカ サーバーにログインし、外部インスタンスをソース サーバーとして設定します。 この設定は、Azure DB for MySQL サーバーで `mysql.az_replication_change_master` ストアド プロシージャを使用して行います。

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', <master_port>, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

 
   - master_host: ソース サーバーのホスト名
   - master_user: ソース サーバーのユーザー名
   - master_password: ソース サーバーのパスワード
   - master_port: ソース サーバーで接続をリッスンしているポート番号 (3306 は MySQL でリッスンする既定のポートです)。
   - master_log_file: `show master status` を実行することによって得たバイナリ ログ ファイルの名前
   - master_log_pos: `show master status` を実行することによって得たバイナリ ログの位置
   - master_ssl_ca:CA 証明書のコンテキスト。 SSL を使用していない場合は、空の文字列を渡します。

     このパラメーターは変数で渡すことをお勧めします。 詳細については、次の例を参照してください。

   > [!NOTE]
   > * ソース サーバーが Azure VM にホストされる場合は、[Azure サービスへのアクセスを許可] を [有効] に設定して、ソースとレプリカのサーバーが相互に通信できるようにします。 この設定は、 **[接続のセキュリティ]** オプションから変更できます。 詳細については、[ポータルを使用したファイアウォール規則の管理](how-to-manage-firewall-portal.md)に関する記事を参照してください。
   > * mydumper/mydumper を使用してデータベースをダンプした場合は、 */backup/metadata* ファイルから master_log_file と master_log_pos を取得できます。 

   **使用例**

   *SSL を使用したレプリケーション*

   変数 `@cert` は、次の MySQL コマンドを実行して作成します。

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   SSL を使用したレプリケーションを、ドメイン "companya.com" にホストされたソース サーバーと Azure Database for MySQL フレキシブル サーバーにホストされたレプリカ サーバーとの間で設定します。 このストアド プロシージャはレプリカで実行します。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *SSL を使用しないレプリケーション*

   SSL を使用しないレプリケーションを、ドメイン "companya.com" にホストされたソース サーバーと Azure Database for MySQL フレキシブル サーバーにホストされたレプリカ サーバーとの間で設定します。 このストアド プロシージャはレプリカで実行します。

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```
2. レプリケーションを開始します。

   `mysql.az_replication_start` ストアド プロシージャを呼び出してレプリケーションを開始します。

   ```sql
   CALL mysql.az_replication_start;
   ```

3. レプリケーションの状態を確認します。

   レプリカ サーバーで [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) コマンドを呼び出して、レプリケーションの状態を確認します。

   ```sql
   show slave status;
   ```

   `Slave_IO_Running` と `Slave_SQL_Running` の状態が "yes" で、`Seconds_Behind_Master` の値が "0" であれば、レプリケーションは正常に機能しています。 `Seconds_Behind_Master` は、レプリカの遅れの程度を示しています。 この値が "0" 以外である場合、レプリカで更新処理が実行されていることを意味します。

## <a name="other-useful-stored-procedures-for-data-in-replication-operations"></a>データイン レプリケーション操作のためのその他の便利なストアド プロシージャ

### <a name="stop-replication"></a>レプリケーションの停止

ソースとレプリカのサーバー間でレプリケーションを停止するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>レプリケーション関係の解除

ソースとレプリカのサーバーの関係を解除するには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>レプリケーション エラーのスキップ

レプリケーション エラーをスキップして、レプリケーションを続行できるようにするには、次のストアド プロシージャを使用します。

```sql
CALL mysql.az_replication_skip_counter;
```

```sql
SHOW BINLOG EVENTS [IN 'log_name'] [FROM pos][LIMIT [offset,] row_count]
```

  :::image type="content" source="./media/how-to-data-in-replication/show-binary-log.png" alt-text="バイナリ ログの結果を表示する":::

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL フレキシブル サーバーの[データイン レプリケーション](concepts-data-in-replication.md)について確認します。
