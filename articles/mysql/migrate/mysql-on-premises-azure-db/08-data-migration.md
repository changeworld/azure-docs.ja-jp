---
title: 'オンプレミスの MySQL を Azure Database for MySQL へ移行する: データ移行'
description: アップグレードまたはデータを移行する前に、MySQL Workbench を使用するか、手動で mysqldump コマンドを使用して、データベースをエクスポートしてからアップグレードします。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 9e53243424f4cb0b289c0ebba9c82cb9fb4853cc
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084943"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration"></a>オンプレミスの MySQL を Azure Database for MySQL へ移行する: データ移行

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[パフォーマンス ベースライン](07-performance-baselines.md)

## <a name="back-up-the-database"></a>データベースをバックアップする

アップグレードまたはデータを移行する前に、MySQL Workbench を使用するか、手動で `mysqldump` コマンドを使用して、データベースをエクスポートしてからアップグレードします。

## <a name="offline-vs-online"></a>オフラインとオンライン

移行ツールを選択する前に、移行がオンラインまたはオフラインのどちらであるかを判断する必要があります。

  - **オフライン移行** では、移行の実行中にシステムがダウンします。 このオプションを使用すると、トランザクションが発生せず、Azure で復元される際のデータの状態は正確に予想できます。

  - **オンライン移行** では、ほぼリアルタイムでデータが移行されます。 このオプションは、データ ワークロードを使用するユーザーまたはアプリケーションのダウンタイムがほとんどない場合に適しています。 このプロセスでは、`binlog` などのレプリケーション方法を使用してデータをレプリケートします。

WWI の場合、環境には複雑なネットワークとセキュリティの要件がいくつかあるので、目標の移行期間内に受信接続と送信接続に適切な変更を適用できません。 これらの複雑さや要件によって、基本的にはオンライン アプローチが考慮されなくなります。

> [!NOTE]
> オフラインとオンラインの移行の詳細については、計画と評価のセクションを参照してください。

## <a name="data-drift"></a>データ ドリフト

オフライン移行戦略では、データ ドリフトが発生する可能性があります。 データ ドリフトは、新しく変更されたソース データが移行されたデータと同期しなくなった場合に発生します。 この場合は、完全エクスポートまたは差分エクスポートが必要です。 この問題を軽減するには、データベースへのすべてのトラフィックを停止してからエクスポートを実行します。 すべてのデータ変更トラフィックを停止できない場合は、ドリフトを考慮する必要があります。

移行する必要のあるすべてのテーブルに、数値ベースの主キーや、変更と作成の日付などの列が含まれていない場合は、変更の特定が複雑になることがあります。

たとえば、数値ベースの主キーが存在し、移行が並べ替え順序でインポートされている場合、インポートが停止した場所を特定し、その位置から再開するのは比較的簡単です。 数値ベースのキーが存在しない場合は、変更と作成の日付を利用し、この場合も並び替えてインポートして、ターゲットに存在する最後の日付から移行を再開できる可能性があります。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

### <a name="exporting"></a>エクスポート

  - mydumper などのマルチスレッド モードで実行できるエクスポート ツールを使用します。

  - MySQL 8.0 を使用する場合は、必要に応じて[パーティション テーブル](https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html)を使用して、エクスポートの速度を向上させることができます。

### <a name="importing"></a>インポート

  - データを読み込んだ後に、クラスター化インデックスと主キーを作成します。 主キーの順序でデータを読み込むか、または主キーの日付列 (変更日や作成日など) を並び替えた順に読み込みます。

  - データの読み込みが完了するまでセカンダリ インデックスの作成を遅らせます。 読み込み後に、すべてのセカンダリ インデックスを作成します。

  - 読み込み前に外部キー制約を無効にします。 外部キーのチェックを無効にすると、パフォーマンスが大幅に向上します。 読み込み後に制約を有効にし、データを検証して参照整合性を確認します。

  - データを並列で読み込みます。 リソースの競合が発生するような過剰な並列処理を避け、Azure portal で使用可能なメトリックを使用してリソースを監視します。

## <a name="perform-the-migration"></a>移行する

  - データベースをバックアップする

  - Azure ランディング ゾーンを作成して確認する

  - ソース サーバーのパラメーターを構成する

  - ターゲット サーバーのパラメーターを構成する

  - データベース オブジェクト (スキーマ、ユーザーなど) をエクスポートする

  - データをエクスポートする

  - データベース オブジェクトをインポートする

  - データをインポートする

  - 検証

  - ターゲット サーバーのパラメーターをリセットする

  - アプリケーションを移行する

## <a name="common-steps"></a>一般的な手順

どのようなパスを使用するかにかかわらず、次のような一般的な手順を実行する必要があります。

  - Azure MySQL をサポートされているバージョンにアップグレードする

  - データベース オブジェクトのインベントリを実行する

  - ユーザーとアクセス許可をエクスポートする

## <a name="migrate-to-latest-mysql-version"></a>最新の MySQL バージョンに移行する

WWI の Conference データベースでは 5.5 が実行されているので、アップグレードを行う必要があります。 CIO は、最新バージョンの MySQL (現在は 8.0) にアップグレードするように求めています。

8\.0 にアップグレードするには次の 2 つの方法があります。

  - インプレース

  - エクスポート/インポート

アップグレードを行う場合は、**アップグレード チェッカー** ツールを実行して、競合が発生するかどうかを判断することが重要です。 たとえば、MySQL Server 8.0 にアップグレードする場合、ツールによって次の競合が確認されます。

  - MySQL 8.0 の予約語と競合するデータベース オブジェクト名

  - utf8mb3 文字セットの使用

  - ZEROFILL/表示長型属性の使用

  - 8\.0 のテーブルと競合するテーブル名

  - 一時的な型の使用

  - 64 文字を超える外部キー制約名

アップグレード チェッカーで問題が報告されない場合は、MySQL バイナリを置き換えることによってインプレース アップグレードを行っても問題はありません。 問題がある場合は、データベースをエクスポートして問題に対処する必要があります。

## <a name="wwi-scenario"></a>WWI のシナリオ

MySQL インスタンスを 8.0 に正常に移行した後、WWI 移行チームは、元の [Database Migration Service (DMS)](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql?step=1) 移行パスを使用できなくなったことに気付きました。DMS ツールが現在サポートしているのは、5.6 と 5.7 のみであるためです。 DMS にはネットワーク アクセスが必要です。 WWI 移行チームは、複雑なネットワークの問題に対処する準備ができていませんでした。 これらの環境の問題により、移行ツールの選択が MySQL Workbench に限定されました。

## <a name="database-objects"></a>データベース オブジェクト

テスト計画のセクションで説明したように、すべてを移行したことを確認するには、移行の前後にデータベース オブジェクトのインベントリを実行する必要があります。

ストアド プロシージャを実行してこの情報を生成する場合は、次のようなものを使用できます。

```
DELIMITER // 
CREATE PROCEDURE `Migration_PerformInventory`(IN schemaName CHAR(64)) 
BEGIN 

        DECLARE finished INTEGER DEFAULT 0; 
          DECLARE tableName varchar(100) DEFAULT ""; 

        #get all tables 
            DECLARE curTableNames 
                CURSOR FOR 
                    SELECT TABLE_NAME FROM information_schema.tables where TABL
E_SCHEMA = schemaName; 
        
            -- declare NOT FOUND handler 
            DECLARE CONTINUE HANDLER 
                FOR NOT FOUND SET finished = 1; 
        
            DROP TABLE IF EXISTS MIG_INVENTORY; 

                CREATE TABLE MIG_INVENTORY 
                ( 
                      REPORT_TYPE VARCHAR(1000), 
                      OBJECT_NAME VARCHAR(1000), 
                  PARENT_OBJECT_NAME VARCHAR (1000), 
                      OBJECT_TYPE VARCHAR(1000), 
                      COUNT INT
                ) 
                ROW_FORMAT=DYNAMIC, 
                ENGINE='InnoDB';
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT
                     'OBJECTCOUNT', 'TABLES', 'TABLES', COUNT(*)
              FROM 
                     information_schema.tables 
                where 
                     TABLE_SCHEMA = schemaName;
                #### Constraints
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'STATISTICS', 'STATISTICS', COUNT(*) 
                FROM 
                      information_schema.STATISTICS 
                WHERE 
                      TABLE_SCHEMA = schemaName; 
                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'VIEWS', 'VIEWS', COUNT(*) 
                FROM 
                      information_schema.VIEWS 
                WHERE 
                      ROUTINE_TYPE = 'FUNCTION' and 
                      ROUTINE_SCHEMA = schemaName;

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'PROCEDURES', 'PROCEDURES', COUNT(*) 
                FROM 
                      information_schema.ROUTINES 
                WHERE 
                      ROUTINE_TYPE = 'PROCEDURE' and 
                      ROUTINE_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'EVENTS', 'EVENTS', COUNT(*) 
                FROM 
                       information_schema.EVENTS 
                WHERE 
                       EVENT_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'USER DEFINED FUNCTIONS', 'USER DEFINED FUNCTIONS'
        , COUNT(*) 
                FROM 
                        mysql.func; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                        'OBJECTCOUNT', 'USERS', 'USERS', COUNT(*) 
                FROM 
                        mysql.user 
                WHERE 
                        user <> '' order by user; 

                OPEN curTableNames; 
        
                getTableName: LOOP 
                        FETCH curTableNames INTO tableName; 
                        IF finished = 1 THEN 
                              LEAVE getTableName; 
                        END IF; 

                   SET @s = CONCAT('SELECT COUNT(*) into @TableCount FROM ', schemaName, 
'.', tableName); 
        #SELECT @s; 
            PREPARE stmt FROM @s; 
        EXECUTE stmt;
        INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
            
                SELECT 
                    'TABLECOUNT', tableName, 'TABLECOUNT', @TableCount; 
        DEALLOCATE PREPARE stmt; 
        
     END LOOP getTableName; 
     CLOSE curTableNames; 
          
   SELECT * FROM MIG_INVENTORY; 
END // 

DELIMITER ; 

CALL Migration_PerformInventory('reg_app');
```

  - ソース DB でこのプロシージャを呼び出すと、次のようになります (切り捨てられた出力)。

![切り捨てられた出力](./media/image4.jpg)

  - 移行完了後、ターゲット データベースのプロシージャの結果は次の図のようになります。 DB に関数がないことに注意してください。移行の前に関数が削除されました。

![DB 関数](./media/image5.jpg)

## <a name="users-and-permissions"></a>ユーザーとアクセス許可

移行を成功させるには、関連付けられているユーザーとアクセス許可をターゲット環境に移行する必要があります。

次の PowerShell スクリプトを使用して、すべてのユーザーとその許可をエクスポートします。

```
$username = "yourusername"; 
$password = "yourpassword"; 
mysql -u$username -p$password --skip-column-names -A -e"SELECT CONCAT('SHOW G
RANTS FOR ''',user,'''@''',host,''';') FROM mysql.user WHERE user<>''" > Show
Grants.sql; 

$lines = get-content "ShowGrants.sql" 

foreach ($line in $lines) 
{ 
mysql -u$username -p$password --skip-column-names -A -e"$line" >> AllGrants.sql 
}
```

  - PowerShell ISE を使用して新しい PowerShell スクリプトを作成します (セットアップ ドキュメントを参照してください)。

  - **your username** を root に設定し、**your password** を root ユーザーのパスワードに設定します

その後、新しい Azure Database for MySQL を対象とした `AllGrants.sql` スクリプトを実行できます。

```
$username = "yourusername"; 
$password = "yourpassword"; 
$server = "serverDNSname"; 
$lines = get-content "AllGrants.sql" 

foreach ($line in $lines)
{ 
mysql -u$username -p$password -h$server --ssl-ca=c:\temp\BaltimoreCyberTrus
tRoot.crt.cer --skip-column-names -A -e"$line" 
}
```

PowerShell を使用して Azure Database for MySQL でユーザーを作成することもできます (/ja-jp/azure/mysql/howto-create-users)。

## <a name="execute-migration"></a>移行を実行する

基本的な移行コンポーネントが配置されたので、データの移行を続行できます。 以前に導入されたツールと方法がいくつかありました。 WWI の場合、MySQL Workbench パスを使用してデータをエクスポートしてから、Azure Database for MySQL にインポートします。

## <a name="data-migration-checklist"></a>データ移行チェックリスト

  - 環境の複雑さと、オンラインでのアプローチが可能かどうかを理解します。

  - データ ドリフトを考慮します。 データベース サービスを停止すると、データ ドリフトが生じる可能性がなくなります。

  - 高速エクスポートのためのソース パラメーターを構成します。

  - 高速インポートのためのターゲット パラメーターを構成します。

  - ソースのバージョンがターゲットと異なる移行をテストします。

  - ユーザー名や特権など、データベース以外のオブジェクトを移行します。

  - 移行実行時に、すべてのタスクが文書化され、チェック済みであることを確認します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [MySQL Workbench を使用したデータ移行](./09-data-migration-with-mySQL-workbench.md)