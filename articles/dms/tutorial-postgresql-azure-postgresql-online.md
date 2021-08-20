---
title: チュートリアル:Azure CLI を介してオンラインで PostgreSQL を Azure Database for PostgreSQL に移行する
titleSuffix: Azure Database Migration Service
description: CLI を介して、Azure Database Migration Service を使用して、オンプレミスの PostgreSQL から Azure Database for PostgreSQL にオンライン移行を実行する方法を説明します。
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 52d5c7a500652b0090cf9b21400a9c45f2bf54e7
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033733"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>チュートリアル:Azure CLI を介して DMS を使用してオンラインで PostgreSQL を Azure DB for PostgreSQL に移行する

Azure Database Migration Service を使用して、最小限のダウンタイムでデータベースをオンプレミスの PostgreSQL インスタンスから [Azure Database for PostgreSQL](../postgresql/index.yml) に移行できます。 つまり、アプリケーションにとって最小限のダウンタイムで移行を実現できます。 このチュートリアルでは、Azure Database Migration Service のオンライン移行アクティビティを使用して、**DVD Rental** サンプル データベースを PostgreSQL 9.6 のオンプレミス インスタンスから Azure Database for PostgreSQL に移行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * pg_dump ユーティリティを使用してサンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

> [!NOTE]
> Azure Database Migration Service を使用してオンライン移行を実行するには、Premium 価格レベルに基づいてインスタンスを作成する必要があります。 移行プロセス中のデータの盗難を防ぐために、ディスクは暗号化します。

> [!IMPORTANT]
> 最適な移行エクスペリエンスのために、ターゲット データベースと同じ Azure リージョンに Azure Database Migration Service のインスタンスを作成することをお勧めします。 リージョンや地域をまたいでデータを移動する場合、移行プロセスが遅くなり、エラーが発生する可能性があります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* [PostgreSQL コミュニティ エディション](https://www.postgresql.org/download/) 9.5、9.6、または 10 をダウンロードしてインストールします。 ソースの PostgreSQL サーバーのバージョンが 9.5.11、9.6.7、10、またはそれ以降のバージョンである必要があります 詳しくは、「[サポートされている PostgreSQL Database バージョン](../postgresql/concepts-supported-versions.md)」の記事をご覧ください。

    また、ターゲットの Azure Database for PostgreSQL のバージョンは、オンプレミスの PostgreSQL バージョンと同じかそれ以降である必要があることに注意してください。 たとえば、PostgreSQL 9.6 は Azure Database for PostgreSQL 9.6、10、または 11 にのみ移行できますが、Azure Database for PostgreSQL 9.5 には移行できません。

* [Azure Database for PostgreSQL のインスタンスを作成](../postgresql/quickstart-create-server-database-portal.md)するか、[Azure Database for PostgreSQL - Hyperscale (Citus) サーバーを作成](../postgresql/quickstart-create-hyperscale-portal.md)します。
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* [データベース エンジン アクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
* Azure Database Migration Service がソース PostgreSQL サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 5432 です。
* ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
* Azure Database for PostgreSQL のサーバーレベルの[ファイアウォール規則](../postgresql/concepts-firewall-rules.md)を作成して、Azure Database Migration Service がターゲット データベースにアクセスできるようにします。 Azure Database Migration Service に使用する仮想ネットワークのサブネット範囲を指定します。
* CLI を呼び出すには、次の 2 つの方法があります。

  * Azure portal の右上隅にある [Cloud Shell] ボタンを選択します。

       ![Azure Portal の [Cloud Shell] ボタン](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * CLI をローカルにインストールして実行します。 この移行に必要な Azure リソースを管理するには、CLI 2.18 以降のバージョンのコマンドライン ツールが必要です。

       CLI をダウンロードするには、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関する記事の手順に従ってください。 その記事には、Azure CLI をサポートするプラットフォームの一覧も示されています。

       Windows Subsystem for Linux (WSL) を設定するには、[Windows 10 インストール ガイド](/windows/wsl/install-win10)の指示に従います。

* postgresql.config ファイルを編集して次のパラメーターを設定することで、ソース サーバーでの論理レプリケーションを有効にします。

  * wal_level = **logical**
  * max_replication_slots = [スロットの数]、**5 スロット** に設定することをお勧めします
  * max_wal_senders = [同時実行タスク数] - max_wal_senders パラメーターでは同時に実行できるタスクの数を設定します、**10 タスク** に設定することをお勧めします

## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを完了するには、ソース データベースからスキーマを抽出し、データベースに適用する必要があります。

1. pg_dump -s コマンドを使用して、データベースのスキーマ ダンプ ファイルを作成します。 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    たとえば、スキーマ ファイル dvdrental データベースをダンプするには:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    pg_dump ユーティリティの使用方法の詳細については、 [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES) チュートリアルの例を参照してください。

2. ターゲット環境に空のデータベース (Azure Database for PostgreSQL) を作成します。

    データベースの接続および作成方法の詳細については、「[Azure portal で Azure Database for PostgreSQL サーバーを作成する](../postgresql/quickstart-create-server-database-portal.md)」または「[Azure portal で Azure Database for PostgreSQL - Hyperscale (Citus) を作成する](../postgresql/quickstart-create-hyperscale-portal.md)」を参照してください。

3. スキーマ ダンプ ファイルを復元して作成したターゲット データベースに、スキーマをインポートします。

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    次に例を示します。

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

  > [!NOTE]
   > 移行サービスにより、外部キーの有効化/無効化が内部的に処理され、信頼性のある堅牢なデータ移行が確保されるようトリガーされます。 そのため、ターゲット データベース スキーマの変更は検討する必要はありません。

## <a name="provisioning-an-instance-of-dms-using-the-azure-cli"></a>Azure CLI を使用した DMS のインスタンスのプロビジョニング

1. dms の同期の拡張機能をインストールします。
   * 次のコマンドを実行して Azure にサインインします。
       ```azurecli
       az login
       ```

   * プロンプトが表示されたら、Web ブラウザーを開き、デバイスを認証するためのコードを入力します。 記載されている手順に従います。

   * 通常の CLI パッケージ (バージョン 2.18.0 以降) で PostgreSQL のオンライン移行を利用できるようになり、`dms-preview` 拡張機能は必要ありません。 拡張機能を過去にインストールした場合は、次の手順を使用して削除できます。
        * `dms-preview` 拡張機能が既にインストールされているかどうかを確認するには、次のコマンドを実行します。
        
            ```azurecli
            az extension list -o table
            ```

        * `dms-preview` 拡張機能がインストールされている場合、それをアンインストールするには、次のコマンドを実行します。
        
            ```azurecli
            az extension remove --name dms-preview
            ```

        * `dms-preview` 拡張機能が正しくアンインストールされたことを確認するには、次のコマンドを実行します。`dms-preview` 拡張機能が一覧に表示されていてはなりません。

            ```azurecli
            az extension list -o table
            ```

      > [!IMPORTANT]
      > `dms-preview` 拡張機能は、Azure DMS でサポートされている他の移行パスにまだ必要である場合があります。 拡張機能が必要かどうかを判断するには、特定の移行パスのドキュメントを確認してください。 このドキュメントでは、Azure Database for PostgreSQL オンラインに対する PostgreSQL に固有の拡張機能の要件について説明します。

   * DMS でサポートされているすべてのコマンドを表示するには、次のコマンドを実行します。

       ```azurecli
       az dms -h
       ```

   * 複数の Azure サブスクリプションがある場合は、次のコマンドを実行して、DMS サービスのインスタンスのプロビジョニングに使用するサブスクリプションを設定します。

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. 次のコマンドを実行して、DMS のインスタンスをプロビジョニングします。

   ```azurecli
   az dms create -l <location> -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   たとえば、次の設定でサービスを作成するには、下記のコマンドを実行します。

   * 場所:米国東部 2
   * サブスクリプション:97181df2-909d-420b-ab93-1bff15acb6b7
   * リソース グループ名:PostgresDemo
   * DMS サービス名:PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   DMS サービスのインスタンスを作成するには、約 10 ～ 12 分かかります。

3. DMS エージェントの IP アドレスを特定して Postgres pg_hba.conf ファイルに追加できるようにするには、次のコマンドを実行します。

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    次に例を示します。

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    次のアドレスのような結果が表示されます。 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Postgres pg_hba.conf ファイルに、DMS エージェントの IP アドレスを追加します。

    * DMS でのプロビジョニングが完了したら、DMS の IP アドレスをメモしておきます。
    * 次のエントリのように、ソース上の pg_hba.conf ファイルに IP アドレスを追加します。

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. 次に、以下のコマンドを実行して、PostgreSQL の移行プロジェクトを作成します。
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    たとえば、次のパラメーターを使用してプロジェクトを作成するには、下記のコマンドを実行します。

    * 場所:米国中西部
    * リソース グループ名:PostgresDemo
    * サービス名:PostgresCLI
    * プロジェクト名:PGMigration
    * ソース プラットフォーム:PostgreSQL
    * ターゲット プラットフォーム:AzureDbForPostgreSql   

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. 次の手順を使用して PostgreSQL 移行タスクを作成します。

    この手順では、接続元 IP、ユーザー ID とパスワード、接続先 IP、ユーザー ID、パスワード、およびタスクの種類を使用して接続を確立します。

    * オプションの完全な一覧を表示するには、次のコマンドを実行します。

        ```azurecli
        az dms project task create -h
        ```

        接続元と接続先の両方について、入力パラメーターはオブジェクトの一覧を含む json ファイルを参照します。

        PostgreSQL の接続に対する接続 JSON オブジェクトの形式。
        
        ```json
        {
            // if this is missing or null, you will be prompted
            "userName": "user name",
            // if this is missing or null (highly recommended) you will  be prompted  
            "password": null,
            "serverName": "server name",
            // if this is missing, it will default to the 'postgres' database
            "databaseName": "database name",
            // if this is missing, it will default to 5432 
            "port": 5432                
        }
        ```

        json オブジェクトを一覧表示するデータベース オプションの json ファイルもあります。 PostgreSQL の場合、データベース オプションの JSON オブジェクトの形式は次のとおりです。

        ```json
        [
            {
                "name": "source database",
                "target_database_name": "target database",
                "selectedTables": [
                    "schemaName1.tableName1",
                    ...n
                ]
            },
            ...n
        ]
        ```

    * ソース接続 json を作成するには、メモ帳を開き、次の json をコピーしてファイルに貼り付けます。 ソース サーバーに従って変更した後、C:\DMS\source.json にファイルを保存します。

        ```json
        {
            "userName": "postgres",    
            "password": null,
            "serverName": "13.51.14.222",
            "databaseName": "dvdrental", 
            "port": 5432                
        }
        ```

    * ターゲット接続 json を作成するには、メモ帳を開き、次の json をコピーしてファイルに貼り付けます。 ターゲット サーバーに従って変更した後、C:\DMS\target.json にファイルを保存します。
    
        ```json
        {
            "userName": " dms@builddemotarget",    
            "password": null,           
            "serverName": " builddemotarget.postgres.database.azure.com",
            "databaseName": "inventory", 
            "port": 5432                
        }
        ```

    * 移行するデータベースのインベントリとマッピングの一覧が含まれるデータベース オプションの json ファイルを作成します。

        * 移行するテーブルの一覧を作成します。または、SQL クエリを使用して、ソース データベースから一覧を生成することもできます。 テーブルの一覧を生成するクエリのサンプルを、例として以下に示します。 このクエリを使用する場合は、最後のテーブル名の末尾にある最後のコンマを削除して、有効な JSON 配列にすることを忘れないでください。 
        
            ```sql
            SELECT
                FORMAT('%s,', REPLACE(FORMAT('%I.%I', schemaname, tablename), '"', '\"')) AS SelectedTables
            FROM 
                pg_tables
            WHERE 
                schemaname NOT IN ('pg_catalog', 'information_schema');
            ```

        * データベースごとに 1 つのエントリが含まれ、ソースとターゲットのデータベース名および移行対象に選択されたテーブルの一覧が指定されている、データベース オプションの json ファイルを作成します。 上の SQL クエリの出力を使用して、 *"selectedTables"* 配列を設定できます。 **選択されたテーブルの一覧が空の場合、サービスにより、一致するスキーマ名とテーブル名を持つすべてのテーブルが移行の含められることに注意してください**。
        
            ```json
            [
                {
                    "name": "dvdrental",
                    "target_database_name": "dvdrental",
                    "selectedTables": [
                        "schemaName1.tableName1",
                        "schemaName1.tableName2",                    
                        ...
                        "schemaNameN.tableNameM"
                    ]
                },
                ... n
            ]
            ```

    * 次のコマンドを実行します。このコマンドは、ソース接続、ターゲット接続、およびデータベース オプション json ファイルを受け取ります。

        ```azurecli
        az dms project task create -g PostgresDemo --project-name PGMigration --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json --task-type OnlineMigration -n runnowtask    
        ```

    この時点で、移行タスクが正常に送信されました。

7. タスクの進行状況を表示するには、次のコマンドを実行します。

    * 一般的なタスクの状態を簡単に確認するには
        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
        ```

    * 移行の進行状況の情報を含む詳細なタスクの状態を確認するには

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output
        ```

    * [JMESPATH](https://jmespath.org/) クエリ形式を使用して、展開出力から migrationState のみを抽出することもできます。

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output --query 'properties.output[].migrationState'
        ```

        出力には、さまざまな移行ステップの進行状況を示すいくつかのパラメーターがあります。 たとえば、次の出力を参照してください。

        ```json
        {
            "output": [
                // Database Level
                {
                    "appliedChanges": 0, // Total incremental sync applied after full load
                    "cdcDeleteCounter": 0, // Total delete operation  applied after full load
                    "cdcInsertCounter": 0, // Total insert operation applied after full load
                    "cdcUpdateCounter": 0, // Total update operation applied after full load
                    "databaseName": "inventory",
                    "endedOn": null,
                    "fullLoadCompletedTables": 2, //Number of tables completed full load
                    "fullLoadErroredTables": 0, //Number of tables that contain migration error
                    "fullLoadLoadingTables": 0, //Number of tables that are in loading status
                    "fullLoadQueuedTables": 0, //Number of tables that are in queued status
                    "id": "db|inventory",
                    "incomingChanges": 0, //Number of changes after full load
                    "initializationCompleted": true,
                    "latency": 0,
                    //Status of migration task
                    "migrationState": "READY_TO_COMPLETE", //READY_TO_COMPLETE => the database is ready for cutover
                    "resultType": "DatabaseLevelOutput",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00"
                }, {
                    "databaseCount": 1,
                    "endedOn": null,
                    "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
                    "resultType": "MigrationLevelOutput",
                    "sourceServer": "138.91.123.10",
                    "sourceVersion": "PostgreSQL",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00",
                    "state": "PENDING",
                    "targetServer": "builddemotarget.postgres.database.azure.com",
                    "targetVersion": "Azure Database for PostgreSQL"
                },
                // Table 1
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 0,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00", //Full load completed time
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00", //Full load started time
                    "fullLoadTotalRows": 10, //Number of rows loaded in full load
                    "fullLoadTotalVolumeBytes": 7056, //Volume in Bytes in full load
                    "id": "or|inventory|public|actor",
                    "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED", //State of migration for this table
                    "tableName": "public.catalog", //Table name
                    "totalChangesApplied": 0 //Total sync changes that applied after full load
                },
                //Table 2
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 50,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
                    "fullLoadTotalRows": 112,
                    "fullLoadTotalVolumeBytes": 46592,
                    "id": "or|inventory|public|address",
                    "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED",
                    "tableName": "public.orders",
                    "totalChangesApplied": 0
                }
            ],
            // DMS migration task state
            "state": "Running", //Running => service is still listening to any changes that might come in
            "taskType": null
        }
        ```

## <a name="cutover-migration-task"></a>一括移行タスク

全体の読み込みが完了すると、データベースの一括移行の準備が整います。 新しいトランザクションによるソース サーバーのビジー状態によっては、DMS タスクで、全体の読み込みが完了した後でも変更が適用されている可能性があります。

すべてのデータを確実に取り込むには、ソース データベースとターゲット データベース間の行数を検証します。 たとえば、状態の出力から次の詳細を検証できます。

```
Database Level
"migrationState": "READY_TO_COMPLETE" => Status of migration task. READY_TO_COMPLETE means database is ready for cutover
"incomingChanges": 0 => Check for a period of 5-10 minutes to ensure no new incoming changes need to be applied to the target server

Table Level (for each table)
"fullLoadTotalRows": 10    => The row count matches the initial row count of the table
"cdcDeleteCounter": 0      => Number of deletes after the full load
"cdcInsertCounter": 50     => Number of inserts after the full load
"cdcUpdateCounter": 0      => Number of updates after the full load
```

1. 次のコマンドを使用して、データベースの一括移行タスクを実行します。

    ```azurecli
    az dms project task cutover -h
    ```

    たとえば、次のコマンドでは、"Inventory" データベースのカットオーバーが開始されます。

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask  --object-name Inventory
    ```

2. 一括移行の進行状況を監視するには、次のコマンドを実行します。

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```
3. データベースの移行状態に **[完了]** と表示されたら、[順序を再作成](https://wiki.postgresql.org/wiki/Fixing_Sequences) (該当する場合) して、アプリケーションを Azure Database for PostgreSQL の新しいターゲット インスタンスに接続します。

## <a name="service-project-task-cleanup"></a>サービス、プロジェクト、タスクのクリーンアップ

DMS タスク、プロジェクト、またはサービスをキャンセルまたは削除する必要がある場合は、次の順序でキャンセルを実行します。

* 実行中のタスクを取り消す
* ジョブを削除する
* プロジェクトを削除する
* DMS サービスを削除する

1. 実行中のタスクをキャンセルするには、次のコマンドを使用します。

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
     ```

2. 実行中のタスクを削除するには、次のコマンドを使用します。
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```

3. 実行中のプロジェクトをキャンセルするには、次のコマンドを使用します。
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. 実行中のプロジェクトを削除するには、次のコマンドを使用します。
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. DMS サービスを削除するには、次のコマンドを使用します。

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>次のステップ

* Azure Database for PostgreSQL へのオンライン移行の実行時の既知の問題と制限事項については、[Azure Database for PostgreSQL のオンライン移行に伴う既知の問題と回避策](known-issues-azure-postgresql-online.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for PostgreSQL の詳細については、「[Azure Database for PostgreSQL とは](../postgresql/overview.md)」を参照してください。