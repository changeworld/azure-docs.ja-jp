---
title: 'チュートリアル: 最小限のダウンタイムで Azure Database for MySQL - 単一サーバーを Azure Database for MySQL - フレキシブル サーバーに移行する'
description: この記事では、Azure Database for MySQL - 単一サーバーから Azure Database for MySQL - フレキシブル サーバーへの移行を最小限のダウンタイムで実行する方法について説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: 3bc4ee8d7d7491e577fba2e29d905262e37764cb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339544"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>チュートリアル: 最小限のダウンタイムで Azure Database for MySQL - 単一サーバーを Azure Database for MySQL - フレキシブル サーバーに移行する

mydumper や myloader などのオープンソース ツールとデータイン レプリケーションを組み合わせて使用することで、アプリケーションのダウンタイムを最小限に抑えながら、Azure Database for MySQL - 単一サーバーのインスタンスを Azure Database for MySQL - フレキシブル サーバーに移行できます。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

データイン レプリケーションは、バイナリ ログ ファイルの配置方法に基づいてソース サーバーへのデータ変更を宛先サーバーにレプリケートする技術です。 このシナリオでは、ソース (データベースの変更が行われる元の場所) として動作する MySQL インスタンスが更新と変更を "イベント" としてバイナリ ログに書き込みます。 バイナリ ログ内の情報は、記録されるデータベースの変更に応じてさまざまなログ形式で保存されます。 レプリカは、ソースからバイナリ ログを読み取り、レプリカのローカル データベース上でバイナリ ログのイベントを実行するように構成されます。

Azure Database for MySQL のあるインスタンスから別のインスタンスにデータを同期するようにデータイン レプリケーションを設定する場合、プライマリ (ソース データベース) からレプリカ (ターゲット データベース) へのアプリケーションの選択的なカットオーバーを実行できます。

このチュートリアルでは、mydumper および myloader とデータイン レプリケーションを使用して、サンプル データベース ([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx)) を Azure Database for MySQL - 単一サーバーのインスタンスから Azure Database for MySQL - フレキシブル サーバーのインスタンスに移行し、データを同期します。

このチュートリアルでは、以下の内容を学習します。

* さまざまなシナリオでデータイン レプリケーションのネットワーク設定を構成する。
* プライマリとレプリカの間でデータイン レプリケーションを構成する。
* レプリケーションをテストする。
* カットオーバーして移行を完了する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* バージョン 5.7 または 8.0 を実行している Azure Database for MySQL 単一サーバーのインスタンス。
    > [!Note]
    > Azure Database for MySQL 単一サーバー バージョン 5.6 を実行している場合、インスタンスを 5.7 にアップグレードしてからデータイン レプリケーションを構成してください。 詳細については、「[Azure Database for MySQL 単一サーバーでのメジャー バージョンのアップグレード](how-to-major-version-upgrade.md)」を参照してください。
* Azure Database for MySQL フレキシブル サーバーのインスタンス。 詳細については、[Azure Database for MySQL フレキシブル サーバーでのインスタンスの作成](./flexible-server/quickstart-create-server-portal.md)に関する記事を参照してください。
    > [!Note]
    > ゾーン冗長高可用性サーバーでのデータイン レプリケーションの構成は、サポートされていません。 ターゲット サーバーでゾーン冗長 HA を使用したい場合は、これらの手順を実行してください。
    >
    > 1. ゾーン冗長 HA が有効なサーバーを作成します
    > 2. HA を無効にします
    > 3. 記事に従ってデータイン レプリケーションを設定します
    > 4. カットオーバー後に、データイン レプリケーション構成を削除します
    > 5. HA を有効にします
    >
    > "*ソースとターゲットのサーバーで **[GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid)** の設定が同じになるようにしてください。* "

* MySQL Workbench を使用して接続し、データベースを作成する。 詳細については、[MySQL Workbench を使用した接続とデータ クエリ](./flexible-server/connect-workbench.md)に関する記事を参照してください。
* Linux を実行し、自分のソースおよびターゲット データベースをホストする Azure VM を、同じリージョン (またはプライベート アクセスを使用する同じ VNet 上) に配置するようにする。
* mysql クライアントまたは MySQL Workbench (クライアント ツール) を自分の Azure VM にインストールする。 プライマリとレプリカの両方のサーバーに接続できるようにしてください。 この記事では、mysql クライアントがインストールされています。
* mydumper または myloader を自分の Azure VM にインストールする。 詳細については、[mydumper および myloader](concepts-migrate-mydumper-myloader.md) に関する記事を参照してください。
* [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip) データベースのサンプル データベース スクリプトをダウンロードして、ソース サーバーで実行する。

## <a name="configure-networking-requirements"></a>ネットワーク要件の構成

データイン レプリケーションを構成するには、ターゲットがポート 3306 経由でソースに接続できるようにする必要があります。 ソースのエンドポイント設定の種類に応じて、次の手順のうち適切な方を実行してください。

* ソースでパブリック エンドポイントが有効な場合は、ファイアウォール規則で [Azure サービスへのアクセスを許可] を有効にしてターゲットがソースに接続できるようにします。 詳細については、[Azure Database for MySQL のファイアウォール規則](./concepts-firewall-rules.md#connecting-from-azure)に関する記事を参照してください。 
* ソースでプライベート エンドポイントと "[パブリック アクセスの拒否](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql)" が有効な場合は、ターゲットをホストする同じ VNet にプライベート リンクをインストールします。 詳細については、[Azure Database for MySQL の Private Link](concepts-data-access-security-private-link.md) に関する記事を参照してください。

## <a name="configure-data-in-replication"></a>データイン レプリケーションの構成

データイン レプリケーションを構成するには、次の手順を実行します。

1. mysql クライアント ツールをインストールした Azure VM にサインインします。

2. mysql クライアント ツールを使用してソースとターゲットに接続します。

3. mysql クライアント ツールを使用し、次のコマンドを実行して、log_bin がソースで有効かどうかを判定します。

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > これは、最大 16 TB をサポートする大容量ストレージを備えた Azure Database for MySQL 単一サーバーでは既定で有効です。

    > [!Tip]
    > これは、最大 4 TB をサポートする Azure Database for MySQL 単一サーバーでは既定で有効になっていません。 ただし、ソース サーバーの[読み取りレプリカ](howto-read-replicas-portal.md)を昇格してから読み取りレプリカを削除すると、パラメーターが ON に設定されます。

4. ソース サーバーの SSL 強制に基づいて、適切なコマンドを実行してレプリケーション アクセス許可付きでソース サーバーにユーザーを作成します。

    SSL を使用している場合は、次のコマンドを実行します。

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    SSL を使用していない場合は、次のコマンドを実行します。

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. mydumper を使用してデータベースをバックアップするには、mydumper または myloader をインストールした Azure VM で次のコマンドを実行します。

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > オプション **--trx-consistency-only** は、バックアップを作成する際にトランザクションの一貫性を確保するために必要です。
    >
    > * mydumper において、mysqldump の --single-transaction に相当します。
    > * すべてのテーブルが InnoDB のときに役立ちます。
    > * "main" スレッドでは、"dump" スレッドがトランザクションを開始するまでの間だけ、グローバル ロックを保持する必要があります。
    > * 最短期間のグローバル ロックを提供します

    "main" スレッドでは、"dump" スレッドがトランザクションを開始するまでの間だけ、グローバル ロックを保持する必要があります。

    このコマンドの変数は以下で説明します。

    * **--host:** プライマリ サーバーの名前
    * **--user:** ユーザーの名前 (プライマリ サーバーが Azure Database for MySQL - 単一サーバーを実行しているため、username@servername の形式)。 サーバー管理者、または SELECT と RELOAD のアクセス許可を持っているユーザーを使用できます。
    * **--Password:** 上記のユーザーのパスワード

    mydumper の使用について詳しくは、[mydumper および myloader](concepts-migrate-mydumper-myloader.md) に関する記事を参照してください

6. 次のコマンドを実行して、メタデータ ファイルを読み取り、バイナリ ログ ファイル名とオフセットを特定します。

    ```bash
    $ cat ./backup/metadata 
    ```

    このコマンドの **./backup** は、前の手順のコマンドで使用された出力ディレクトリを参照します。

    次の画像に示すような結果が表示されるはずです。

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="Azure Database Migration Service での継続的同期":::

    バイナリ ファイルの名前はこの後の手順で使用するため、必ずメモしておいてください。

7. 次のコマンドを実行し、myloader を使用してデータベースを復元します。

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    このコマンドの変数は以下で説明します。

    * **--host:** レプリカ サーバーの名前
    * **--user:** ユーザーの名前。 サーバー管理者、またはスキーマとデータをデータベースに復元できる読み取り/書き込みアクセス許可を持つユーザーを使用できます
    * **--Password:** 上記のユーザーのパスワード

8. プライマリ サーバーの SSL 強制に応じて、mysql クライアント ツールを使用してレプリカ サーバーに接続し、次の手順を実行します。

    * SSL 強制が有効な場合は、次のようになります。

        i. SSL 経由で Azure Database for MySQL サーバーと通信するために必要な証明書を[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)からダウンロードします。

        ii. メモ帳でファイルを開いて、内容を "PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE" セクションに貼り付けます。

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. データイン レプリケーションを構成するために、次のコマンドを実行します。

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > 手順 6 で取得した情報から位置とファイル名を特定してください。

    * SSL 強制が有効でない場合は、次のコマンドを実行します。

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '<username>@<primary_server>', '<Password>', 3306, '<File_Name>', <Position>, ‘’);
        ```

9. レプリカ サーバーからレプリケーションを開始するには、下のストアド プロシージャを呼び出します。

    ```sql
    call  mysql.az_replication_start;
    ```

10. レプリケーションの状態をチェックするには、レプリカ サーバーで次のコマンドを実行します。

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > MySQL Workbench を使用している場合は、\G 修飾子は不要です。

    *Slave_IO_Running* と *Slave_SQL_Running* の状態が Yes で、*Seconds_Behind_Master* の値が 0 だと、レプリケーションは正常に機能しています。 Seconds_Behind_Master は、レプリカの遅延を示します。 0 を超える値の場合は、レプリカが更新を処理しています。

## <a name="testing-the-replication-optional"></a>レプリケーションのテスト (オプション)

データイン レプリケーションが適切に動作していることを確かめるために、プライマリのテーブルに対する変更がレプリカにレプリケートされたことを確認できます。

1. テストに使用するテーブル (たとえば customers テーブル) を特定します。次に、そこに含まれているエントリの数がプライマリとレプリカのサーバーで同じであることを確認するために、各サーバーで次のコマンドを実行します。

    ```
    select count(*) from customers;
    ```

2. 後で比較するために、エントリの数をメモします。

    レプリケーションをテストするために、プライマリ サーバーの customers テーブルへのデータの追加を試行して、新しいデータがレプリケートされたことを確認します。 この場合、プライマリ サーバーのテーブルに 2 つの行を追加してから、それらがレプリカ サーバーにレプリケートされたことを確認します。

3. 次のコマンドを実行して、プライマリ サーバー上の customers テーブルに行を挿入します。

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. レプリケーションの状態をチェックするには、*show slave status \G* を呼び出して、レプリケーションが想定どおりに動作していることを確認します。

5. 数が同じあることを確認するには、レプリカ サーバーで次のコマンドを実行します。

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>カットオーバーを確実に成功させる

カットオーバーを確実に成功させるために、次のタスクを実行します。

1. サーバーレベルの適切なファイアウォールと仮想ネットワーク規則を構成して、ターゲット サーバーに接続します。 ポータルから[ソース](howto-manage-firewall-using-portal.md)と[ターゲット](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created)のファイアウォール規則を比較できます。
2. ターゲット サーバーで適切なログインとデータベース レベルのアクセス許可を構成します。 比較対象のソースおよびターゲット サーバーで *SELECT * FROM mysql.user;* を実行できます。
3. Azure Database for MySQL 単一サーバーへの受信接続がすべて停止されるようにします。
    > [!Tip]
    > Azure Database for MySQL 単一サーバーを読み取りのみに設定できます。
4. *show slave status \G* を実行し、*Seconds_Behind_Master* パラメーターの値が 0 であることを確認して、レプリカがプライマリに同期されたのを確かめます。
5. クライアントとクライアント アプリケーションを Azure Database for MySQL フレキシブル サーバーのターゲット インスタンスにリダイレクトします。
6. mysql.az_replication_stop ストアド プロシージャを実行して最終的なカットオーバーを実行します。これで、レプリカ サーバーからのレプリケーションが停止します。
7. "*mysql.az_replication_remove_master を呼び出して*" データイン レプリケーション構成を削除します。

この時点で、アプリケーションが新しい Azure Database for MySQL フレキシブル サーバーに接続されて、ソースの変更がターゲットにレプリケートされなくなります。

## <a name="next-steps"></a>次のステップ

* データイン レプリケーションの詳細については、「[データを Azure Database for MySQL フレキシブル サーバーにレプリケートする](flexible-server/concepts-data-in-replication.md)」および [Azure Database for MySQL フレキシブル サーバーのデータイン レプリケーションを構成する](./flexible-server/how-to-data-in-replication.md)方法に関する記事を参照してください
* [Azure Database for MySQL の一般的なエラーのトラブルシューティング](howto-troubleshoot-common-errors.md)について詳しく学習します。
* [Azure Database Migration Service を使用して MySQL から Azure Database for MySQL にオフラインで移行する](../dms/tutorial-mysql-azure-mysql-offline-portal.md)方法について詳しく学習します。
