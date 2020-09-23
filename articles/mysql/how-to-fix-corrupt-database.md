---
title: データベースの破損を解決する - Azure Database for MySQL
description: Azure Database for MySQL でのデータベース破損の問題を修正する方法について説明します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90931226"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Azure Database for MySQL でのデータベース破損のトラブルシューティング
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

データベースが破損するとアプリケーションのダウンタイムが発生する可能性があり、データの損失を防ぐためにも、問題を解決することが重要です。 データベースの破損が発生すると、サーバー ログに次のエラーが表示されます。"**InnoDB: Database page corruption on disk or a failed**" (ディスク上のデータベース ページの破損または失敗)

このガイドでは、データベースまたはテーブルが破損した場合の修正方法について説明します。 Azure Database for MySQL で使用されている InnoDB エンジンは、自動的に破損をチェックして修復する操作を備えています。 InnoDB により、読み取られたすべてのページでチェックサムが実行されて破損したページが確認され、チェックサムの不一致が見つかった場合は、MySQL サーバーが自動的に停止されます。

データベースの破損の問題を迅速に軽減するには、以下のオプションのいずれかを試してください。

## <a name="restart-your-mysql-server"></a>MySQL サーバーを再起動する

通常、データベースまたはテーブルが破損していることがわかるのは、アプリケーションがそのテーブルまたはデータベースにアクセスしたときです。 InnoDB にはクラッシュ復旧メカニズムがあるため、サーバーの再起動時にほとんどの問題を解決できます。 したがって、サーバーを再起動すると、データベースが異常な状態になる原因になったクラッシュからサーバーを復旧するのに役立つはずです。

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>ダンプと復元の方法を使用してデータの破損を解決する

**ダンプと復元の方法**を使用して、破損の問題を解決することをお勧めします。 その際、破損したテーブルにアクセスし、**mysqldump** ユーティリティを使用してテーブルの論理バックアップを作成して (テーブルの構造と中のデータは保持されます)、テーブルをデータベースに再度読み込みます。

### <a name="backup-your-database-or-tables"></a>データベースまたはテーブルをバックアップする

> [!Important]
> - クライアント コンピューターからサーバーにアクセスするために、ファイアウォール規則が構成されていることを確認します。 [単一サーバーでファイアウォール規則](howto-manage-firewall-using-portal.md)を構成する方法および[フレキシブル サーバーでファイアウォール規則](flexible-server/how-to-connect-tls-ssl.md)を構成する方法に関するページを参照してください。
> - SSL が有効になっている場合は、**mysqldump** で SSL オプション ```--ssl-cert``` を使用します

コマンド ラインで次の mysqldump コマンドを使用してバックアップ ファイルを作成します

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

指定するパラメーターは次のとおりです。
- [ssl-cert=/path/to/pem]: クライアント コンピューター上の SSL 証明書をダウンロードし、コマンドでそのパスを設定します。 SSL が無効になっている場合はこれを使用しないでください。
- [host]: Azure Database for MySQL サーバーです
- [uname]: サーバー管理者のユーザー名です
- [pass]: 管理者ユーザーのパスワードです
- [dbname]: データベースの名前です
- [backupfile.sql]: データベース バックアップのファイル名です

> [!Important]
> - 単一サーバーの場合は、形式 ```admin-user@servername``` を使用して、次のコマンドの ```myserveradmin``` を置き換えます。
> - フレキシブル サーバーの場合は、形式 ```admin-user``` を使用して、次のコマンドの ```myserveradmin``` を置き換えます。

特定のテーブルが破損している場合は、次の例を使用してバックアップするデータベース内の特定のテーブルを選択します
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

1 つ以上のデータベースをバックアップするには、--database スイッチを使用し、スペースで区切ってデータベース名の一覧を指定します。

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>データベースまたはテーブルを復元する

次の手順では、データベースまたはテーブルを復元する方法について説明します。 バックアップ ファイルを作成したら、***mysql** ユーティリティを使用して、テーブルまたはデータベースを復元できます。 次のようにコマンドを実行します。

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
これは、**mysqldump** で作成されたバックアップ ファイルから ```testdb``` を復元する例です。 

> [!Important]
> - 単一サーバーの場合は、形式 ```admin-user@servername``` を使用して、次のコマンドの ```myserveradmin``` を置き換えます。
> - フレキシブル サーバーの場合は、形式 ```admin-user``` を使用して、次のコマンドの ```myserveradmin``` を置き換えます。 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>次の手順
上記の手順を実行しても問題が解決しない場合は、いつでもサーバー全体を復元できます。
- [Azure Database for MySQL 単一サーバーを復元する](howto-restore-server-portal.md)
- [Azure Database for MySQL フレキシブル サーバーを復元する](flexible-server/how-to-restore-server-portal.md)



