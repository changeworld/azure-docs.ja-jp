---
title: データイン レプリケーションを使用して Amazon RDS for MySQL を Azure Database for MySQL に移行する
description: この記事では、データイン レプリケーションを使用して、Amazon RDS for MySQL を Azure Database for MySQL に移行する方法を説明します。
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647159"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>データイン レプリケーションを使用して Amazon RDS for MySQL を Azure Database for MySQL に移行する

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

MySQL データベースを Azure Database for MySQL に移行するには、MySQL のダンプと復元、MySQL Workbench Export/Import、Azure Database Migration Service (DMS) など、さまざまな方法を使用できます。 ただし、オープンソース ツール (MySQL のダンプと復元、mydumper、myloader など) にデータイン レプリケーションを組み合わせて使用すると、最小限のダウンタイムでワークロードを移行することができます。

データイン レプリケーションは、バイナリ ログ ファイルの配置方法に基づいてソース サーバーへのデータ変更を宛先サーバーにレプリケートする技術です。 このシナリオでは、ソース (データベースの変更が行われる元の場所) として動作する MySQL インスタンスが更新と変更を "イベント" としてバイナリ ログに書き込みます。 バイナリ ログ内の情報は、記録されるデータベースの変更に応じてさまざまなログ形式で保存されます。 レプリカは、ソースからバイナリ ログを読み取り、レプリカのローカル データベース上でバイナリ ログのイベントを実行するように構成されます。

ソース MySQL サーバーからターゲット MySQL サーバーにデータを同期するように[データイン レプリケーション](/azure/mysql/flexible-server/concepts-data-in-replication)を設定する場合、プライマリ (ソース データベース) からレプリカ (ターゲット データベース) へのアプリケーションの選択的なカットオーバーを実行できます。

このチュートリアルでは、Amazon RDS for MySQL を実行するソース サーバーと Azure Database for MySQL を実行するターゲット サーバーとの間でデータイン レプリケーションを設定する方法について説明します。

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

このチュートリアルを開始する前に必ず、操作の実行に使用するクライアント コンピューターの場所と容量がパフォーマンスに与える影響を考慮してください。

### <a name="client-location"></a>クライアントの場所

ダンプと復元の操作は、データベース サーバーと同じ場所で起動したクライアント コンピューターから実行します。

- Azure Database for MySQL サーバーについては、ターゲット データベース サーバーと同じ VNet、同じ可用性ゾーンにクライアント マシンが存在する必要があります。
- ソース Amazon RDS データベース インスタンスについては、ソース データベース サーバーと同じ Amazon Virtual Private Cloud (Amazon VPC) および可用性ゾーンにクライアント インスタンスが存在する必要があります。
以上のケースでは、クライアント マシン間でファイル転送プロトコル (FTP、SFTP など) を使用してダンプ ファイルを移動したり、それらを Azure Blob Storage にアップロードしたりすることができます。 移行の総所要時間を短縮するために、ファイルを転送する前に圧縮できます。

### <a name="client-capacity"></a>クライアントの容量

クライアント コンピューターの場所に関係なく、要求された操作を実行するためには、適切なコンピューティング、I/O、ネットワーク容量が必要です。 一般的な推奨事項は次のとおりです。

- ダンプまたは復元にデータのリアルタイム処理 (圧縮、展開など) が伴う場合、ダンプと復元のスレッドごとに少なくとも 1 つの CPU コアを備えたインスタンス クラスを選択します。
- クライアント インスタンスに十分なネットワーク帯域幅を確保します。 高速ネットワーク機能がサポートされるインスタンス タイプの使用をお勧めします。 詳細については、[Azure 仮想マシン ネットワーク ガイド](/azure/virtual-network/create-vm-accelerated-networking-cli)の高速ネットワークに関するセクションを参照してください。
- 読み取り/書き込みに期待される容量がクライアント マシンのストレージ レイヤーに備わっていることを確認します。Premium SSD ストレージを備えた Azure 仮想マシンをお勧めします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- ターゲット Azure Database for MySQL サーバーにダンプを作成して復元操作を実行する [mysqlclient](https://dev.mysql.com/downloads/) をクライアント コンピューターにインストールします。
- データベースが大きい場合は、データベースのダンプと復元を並列処理する [mydumper と myloader](https://centminmod.com/mydumper.html) をインストールします。

    > [!NOTE]
    > mydumper は Linux ディストリビューションでのみ実行できます。[mydumper のインストール方法](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)に関するセクションを参照してください。

- バージョン 5.7 または 8.0 を実行する Azure Database for MySQL サーバーのインスタンスを作成します。

    > [!IMPORTANT]
    > ゾーン冗長による高可用性を備えた Azure Database for MySQL フレキシブル サーバーがターゲットである場合、この構成では、データイン レプリケーションがサポートされないことに注意してください。 回避策として、サーバーの作成中、次の手順に従ってゾーン冗長 HA を設定してください。
    >
    > 1. ゾーン冗長 HA が有効なサーバーを作成します。
    > 2. HA を無効にします。
    > 3. 記事に従ってデータイン レプリケーションを設定します。
    > 4. カットオーバー後に、データイン レプリケーション構成を削除します。
    > 5. HA を有効にします。

また、いくつかのパラメーターと機能が、以下の記述のとおりに適切に構成、設定されているか確かめることをお勧めします。

- 互換性に関する理由から、ソースとターゲットのデータベース サーバーは同じ MySQL バージョンに揃えることをお勧めします。
- それぞれのテーブルにプライマリ キーを設定します。 テーブルにプライマリ キーがないと、レプリケーション プロセスの速度が低下する場合があります。
- ソースとターゲットのデータベースの文字セットが同じであることを確認します。
- インポートまたは移行するデータまたはワークロードの量に合わせて、`wait_timeout` パラメーターを適切な時間に設定します。
- Azure Database for MySQL サーバーでは InnoDB ストレージ エンジンだけをサポートしているため、すべてのテーブルで InnoDB を使用していることを確認してください。
- セカンダリ インデックスが多いテーブルやサイズがきわめて大きいテーブルの場合、復元時のパフォーマンス オーバーヘッドの影響が特に顕著です。`CREATE TABLE` ステートメントにセカンダリ キーの定義が含まれないようダンプ ファイルを変更してください。データのインポート後にセカンダリ インデックスを再作成することで、復元処理時のパフォーマンス ペナルティを回避できます。

最後に、データイン レプリケーションの準備を行います。

- ターゲット Azure Database for MySQL サーバーがポート 3306 経由でソース Amazon RDS for MySQL サーバーに接続できることを確認します。
- ソース Amazon RDS for MySQL サーバーのポート 3306 で受信と送信の両方のトラフィックが許可されていることを確認します。
- [ExpressRoute](/azure/expressroute/expressroute-introduction) または [VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways) のどちらかを使用して、ソース サーバーへの[サイト間接続](/azure/vpn-gateway/tutorial-site-to-site-portal)が提供されていることを確認します。 仮想ネットワークの作成方法の詳細については、「[Virtual Network のドキュメント](/azure/virtual-network/)」を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。
- ターゲット Azure Database for MySQL サーバーの IP アドレスを許可するようにソース データベース サーバーのネットワーク セキュリティ グループを構成します。

> [!IMPORTANT]
> ソース Amazon RDS for MySQL インスタンスの GTID_mode がオンに設定されている場合、Azure Database for MySQL - フレキシブル サーバーのターゲット インスタンスも GTID_mode がオンに設定されている必要があります。

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Azure Database for MySQL のターゲット インスタンスを構成する

Azure Database for MySQL のターゲット インスタンス (データイン レプリケーションのターゲット) を構成するには、次の手順を実行します。

1. `max_allowed_packet` パラメーターを最大値である **1073741824** (1 GB) に設定し、長い行に関連したオーバーフローの問題を防止します。
2. 移行中は、クエリのログに関連したオーバーヘッドをなくすために、`slow_query_log`、`general_log`、`audit_log_enabled`、`query_store_capture_mode` の各パラメーターを **OFF** に設定します。
3. ソース サーバーからデータベース ダンプを復元する際のコンピューティング リソースを補強するために、ターゲット Azure Database for MySQL サーバーのコンピューティング サイズをその最大である 64 仮想コアにスケールアップします。

    移行の完了後はいつでも、アプリケーションの需要を満たすようにコンピューティングをスケールバックしてかまいません。

4. 移行中の IOPS (移行の最大 IOPS) を高めるために、ストレージ サイズをスケールアップします。

   > [!NOTE]
   > 使用できる最大 IOPS はコンピューティング サイズによって決まります。 詳細については、「[Azure Database for MySQL のコンピューティングとストレージのオプション - フレキシブル サーバー](/azure/mysql/flexible-server/concepts-compute-storage#iops)」の「IOPS」セクションを参照してください。

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>ソース Amazon RDS for MySQL サーバーを構成する

Amazon RDS をホストとする MySQL サーバー (データイン レプリケーションの "ソース") を準備、構成するには、次の手順を実行します。

1. ソース Amazon RDS for MySQL サーバーで、自動化されたバックアップが有効になっているか調べ、バイナリ ログが有効になっていることを確認します。または、ソース Amazon RDS for MySQL サーバーの読み取りレプリカが存在することを確認します。

2. ソース サーバーのバイナリ ログ ファイルは、Azure Database for MySQL のターゲット インスタンスに変更が適用されるまで確実に保持するようにします。

    データイン レプリケーションでは、レプリケーション プロセスが Azure Database for MySQL によって管理されません。

3. ソース Amazon RDS サーバーでバイナリ ログの保有期間を確認して、バイナリ ログが保有される時間数を調べるには、"mysql.rds_show_configuration" ストアド プロシージャを呼び出します。

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
4. バイナリ ログの保有期間を構成するために、`rds_set_configuration` ストアド プロシージャを実行して、必要な期間、ソース サーバーにバイナリ ログが保有されるようにします。次に例を示します。

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    これは、ダンプの作成と復元を行っていて、変更の差分をすぐに反映する場合に役立ちます。

   > [!NOTE]
   > 定義した保有期間に応じて、バイナリ ログを格納できるだけの十分なディスク領域がソース サーバーに存在することを確認してください。

ソース Amazon RDS for MySQL サーバーからデータのダンプを取得する方法は 2 つあります。 1 つはデータのダンプをソース サーバーから直接取得する方法で、もう 1 つは Amazon RDS for MySQL の読み取りレプリカからダンプをキャプチャする方法です。

- データのダンプをソース サーバーから直接キャプチャする場合は、次の手順を実行します。

    1. データのダンプにトランザクション整合性を確保するため、数分間、アプリケーションからの書き込みを停止してください。

       データのダンプをキャプチャしているときに書き込みが処理されないよう、一時的に、`read_only` パラメーターの値を **1** に設定してもかまいません。

    2. ソース サーバーへの書き込みを停止したら、`Mysql> Show master status;` コマンドを実行して、バイナリ ログのファイル名とオフセットを収集します。
    3. それらの値を保存して、Azure Database for MySQL サーバーからレプリケーションを開始します。
    4. データのダンプを作成するには、mysqldump を実行し、次のコマンドを実行します。

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- ソース サーバーへの書き込みを停止できない場合や、ソース サーバーでのデータ ダンプのパフォーマンスを許容できない場合は、次の手順を実行してレプリカ サーバーでダンプをキャプチャします。

    1. ソース サーバーと同じ構成で Amazon MySQL 読み取りレプリカを作成し、そこでダンプを作成します。
    2. Amazon RDS for MySQL の読み取りレプリカをソース Amazon RDS for MySQL サーバーと同期した状態にします。
    3. 読み取りレプリカのレプリカ ラグが **0** に達したら、`mysql.rds_stop_replication` ストアド プロシージャを呼び出してレプリケーションを停止します。

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. レプリケーションが停止したら、レプリカに接続し、`SHOW SLAVE STATUS` コマンドを実行して、**Relay_Master_Log_File** フィールドから最新のバイナリ ログ ファイル名を、**Exec_Master_Log_Pos** フィールドからログ ファイル位置を取得します。
    5. それらの値を保存して、Azure Database for MySQL サーバーからレプリケーションを開始します。
    6. Amazon RDS for MySQL の読み取りレプリカからデータのダンプを作成するために、次のコマンドで mysqldump を実行します。

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > mydumper を使用して、ソース Amazon RDS for MySQL データベースからデータのダンプを並列処理で取得することもできます。 詳細については、[mydumper または myloader を使用して大規模なデータベースを Azure Database for MySQL に移行する](/azure/mysql/concepts-migrate-mydumper-myloader)方法に関するページを参照してください。

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>ソースとレプリカのサーバーをリンクしてデータイン レプリケーションを開始する

1. mysql のネイティブの復元を使用してデータベースを復元するには、次のコマンドを実行します。

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > 代わりに myloader を使用する場合は、[mydumper または myloader を使用して大規模なデータベースを Azure Database for MySQL に移行する](/azure/mysql/concepts-migrate-mydumper-myloader)方法に関するページを参照してください。

2. ソース Amazon RDS for MySQL サーバーにサインインし、レプリケーション ユーザーを設定して、必要な権限をこのユーザーに付与します。

    - SSL を使用している場合は、次のコマンドを実行します。

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - SSL を使用していない場合は、次のコマンドを実行します。

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    データイン レプリケーションの機能は、すべてストアド プロシージャによって実現されています。 すべてのプロシージャについての情報は、「[データイン レプリケーションのストアド プロシージャ](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures)」でご覧いただけます。 これらのストアド プロシージャは、MySQL シェルまたは MySQL Workbench で実行できます。

3. Amazon RDS for MySQL ソース サーバーと Azure Database for MySQL ターゲット サーバーをリンクするために、ターゲット Azure Database for MySQL サーバーにサインインし、次のコマンドを実行して、Amazon RDS for MySQL サーバーをソース サーバーとして設定します。

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. ソース Amazon RDS for MySQL サーバーとターゲット Azure Database for MySQL サーバーとの間でレプリケーションを開始するために、次のコマンドを実行します。

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. レプリケーションの状態をチェックするために、レプリカ サーバーで次のコマンドを実行します。

    ```
    Mysql> show slave status\G
    ```

    `Slave_IO_Running` および `Slave_SQL_Running` パラメーターの状態が **Yes** の場合、レプリケーションは開始されており、実行中の状態です。

6. `Seconds_Behind_Master` パラメーターの値を調べて、ターゲット サーバーの時間差を確認します。

    この値が **0** の場合、ソース サーバーからの更新内容はすべて、ターゲット側で処理済みです。 値が **0** 以外の場合、ターゲット サーバーがまだ更新内容を処理しています。

## <a name="ensure-a-successful-cutover"></a>カットオーバーを確実に成功させる

カットオーバーを確実に成功させるために、次のタスクを実行します。

1. ターゲット Azure Database for MySQL サーバーに適切なログインとデータベース レベルのアクセス許可を構成します。
2. ソース Amazon RDS for MySQL サーバーへの書き込みを停止します。
3. ターゲット Azure Database for MySQL サーバーにソース サーバーが反映され、`show slave status` の `Seconds_Behind_Master` 値が **0** になっていることを確認します。
4. すべての変更がターゲット Azure Database for MySQL サーバーにレプリケートされたので、ストアド プロシージャ `mysql.az_replication_stop` を呼び出してレプリケーションを停止します。
5. `mysql.az_replication_remove_master` を呼び出してデータイン レプリケーション構成を削除します
6. クライアントとクライアント アプリケーションをターゲット Azure Database for MySQL サーバーにリダイレクトします。

この時点で移行は完了です。アプリケーションは、Azure Database for MySQL を実行するサーバーに接続されています。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL へのデータベースの移行については、「[データベース移行ガイド](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)」をご覧ください。
- [MySQL/PostgreSQL アプリを Azure マネージド サービスに簡単に移行する方法](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201)のビデオをご覧ください。MySQL アプリを Azure Database for MySQL に移行する方法を示すデモが含まれています。
