---
title: Azure Database for PostgreSQL - フレキシブル サーバーでの SCRAM を使用した接続
description: Azure Database for PostgreSQL - フレキシブル サーバーで SCRAM を使用して構成および接続する方法に関する手順と情報。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 10/01/2021
ms.openlocfilehash: ad7cbfc7c06a86cca79930c9745160cc9af4697f
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388871"
---
# <a name="scram-authentication-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーでの SCRAM 認証

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

Salted Challenge Response Authentication Mechanism (SCRAM) は、パスワードベースの相互認証プロトコルです。 これは、いくつかのレベルのセキュリティを追加し、信頼されていない接続でのパスワード スニッフィングを防ぐチャレンジ/レスポンス スキームです。 SCRAM では、高度なセキュリティが提供される暗号学的ハッシュ形式でのサーバーへのパスワードの格納がサポートされています。 

SCRAM 認証方法を使用して PostgreSQL データベース サーバーにアクセスするには、クライアント ライブラリで SCRAM がサポートされている必要があります。  SCRAM をサポートする[ドライバー一覧](https://wiki.postgresql.org/wiki/List_of_drivers)を参照してください。

## <a name="configuring-scram-authentication"></a>SCRAM 認証の構成

1. password_encryption を SCRAM-SHA-256 に変更します。 現在、PostgreSQL では、SHA-256 を使用した SCRAM のみがサポートされています。
        :::image type="content" source="./media/how-to-configure-scram/1-password-encryption.png" alt-text="SCRAM パスワード暗号化を有効にする"::: 
2. 認証方法として SCRAM-SHA-256 を許可します。
        :::image type="content" source="./media/how-to-configure-scram/2-auth-method.png" alt-text="認証方法を選択する"::: 
    >[!Important]
    > SCRAM-SHA-256 メソッドのみを選択して、SCRAM のみの認証を適用することを選択できます。 これにより、MD5 認証を使用するユーザーはサーバーに接続できなくなります。 そのため、SCRAM を適用する前に、すべてのユーザー パスワードを SCRAM-SHA-256 に更新するまで MD5 と SCRAM-SHA-256 の両方を認証方法として使用することをお勧めします。 手順 7 で説明したクエリを使用して、ユーザーの認証の種類を確認できます。
3. 変更を保存します。 これらは動的プロパティのため、サーバーの再起動は必要ありません。
4. Postgres クライアントから Postgres サーバーに接続します。 たとえば、次のように入力します。
   
    ```bash
    psql "host=myPGServer.postgres.database.azure.com port=5432 dbname=postgres user=myDemoUser password=MyPassword sslmode=require"

    psql (12.3 (Ubuntu 12.3-1.pgdg18.04+1), server 12.6)
    SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
    Type "help" for help.
    ```

5. パスワードの暗号化を確認します。
   
    ```SQL
    postgres=> show password_encryption;
     password_encryption
    ---------------------
    scram-sha-256
    (1 row)
    ```

6. その後、ユーザーのパスワードを更新できます。

    ```SQL
    postgres=> \password myDemoUser
    Enter new password:
    Enter it again:
    postgres=>
    ```

7. `azure_roles_authtype()` 関数を使用して、ユーザー認証の種類を確認できます。 

    ``` SQL
    postgres=> SELECT * from azure_roles_authtype();
            rolename          | authtype
    ---------------------------+-----------
    azuresu                   | NOLOGIN
    pg_monitor                | NOLOGIN
    pg_read_all_settings      | NOLOGIN
    pg_read_all_stats         | NOLOGIN
    pg_stat_scan_tables       | NOLOGIN
    pg_read_server_files      | NOLOGIN
    pg_write_server_files     | NOLOGIN
    pg_execute_server_program | NOLOGIN
    pg_signal_backend         | NOLOGIN
    replication               | NOLOGIN
    myDemoUser                | SCRAM-256
    azure_pg_admin            | NOLOGIN
    srtest                    | SCRAM-256
    sr_md5                    | MD5
    (14 rows)
    ```

8. その後、SCRAM 認証をサポートするクライアントからサーバーに接続できます。

> [!Note] 
> 組み込みのマネージド [PgBouncer](concepts-pgbouncer.md) に接続されている場合、SCRAM 認証はサポートされません。

## <a name="next-steps"></a>次のステップ
- [Azure CLI を使用して Azure Database for PostgreSQL - フレキシブル サーバー仮想ネットワークを作成し、管理する](./how-to-manage-virtual-network-cli.md)。
- [Azure Database for PostgreSQL - フレキシブル サーバーのネットワーク](./concepts-networking.md)の詳細を確認する
- [Azure Database for PostgreSQL - フレキシブル サーバーのファイアウォール規則](./concepts-networking.md#public-access-allowed-ip-addresses)について理解を深める
