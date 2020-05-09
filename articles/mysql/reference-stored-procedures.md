---
title: 管理ストアド プロシージャ - Azure Database for MySQL
description: データイン レプリケーションの構成、タイムゾーンの設定、クエリの終了を行う場合に役立つ Azure Database for MySQL のストアド プロシージャについて説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 6a3fa40eaae174d3616fd0318f81576b7c59eac7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067698"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Azure Database for MySQL の管理ストアド プロシージャ

ストアド プロシージャは、MySQL サーバーの管理に役立つ Azure Database for MySQL サーバーで使用できます。 これには、サーバーの接続管理、クエリ、データイン レプリケーションの設定が含まれます。  

## <a name="data-in-replication-stored-procedures"></a>データイン レプリケーションのストアド プロシージャ

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MySQL サーバーから Azure Database for MySQL サービスにデータを同期することができます。

次のストアド プロシージャは、マスターとレプリカ間のデータイン レプリケーションの設定または削除に使用されます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_host<br/>master_log_file<br/>master_log_file<br/>master_ssl_ca|該当なし|SSL モードを使用してデータを転送するには、CA 証明書のコンテキストを master_ssl_ca パラメーターに渡します。 </br><br>SSL を使用せずにデータを転送するには、空の文字列を master_ssl_ca パラメーターに渡します。|
|*mysql.az_replication _start*|該当なし|該当なし|レプリケーションを開始します。|
|*mysql.az_replication _stop*|該当なし|該当なし|レプリケーションを停止します。|
|*mysql.az_replication _remove_master*|該当なし|該当なし|マスターとレプリカ間のレプリケーション関係を削除します。|
|*mysql.az_replication_skip_counter*|該当なし|該当なし|1 つのレプリケーション エラーをスキップします。|

Azure Database for MySQL 内でマスターとレプリカの間のデータイン レプリケーションを設定するには、[データイン レプリケーションを構成する方法](howto-data-in-replication.md)を参照してください。

## <a name="other-stored-procedures"></a>その他のストアド プロシージャ

Azure Database for MySQL では、以下のストアド プロシージャがサーバー管理に使用できます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|該当なし|[`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で実行されているステートメントを終了した後、指定された processlist_id に関連する接続を終了します。|
|*mysql.az_kill_query*|processlist_id|該当なし|[`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) コマンドと同等です。 接続で現在実行されているステートメントを終了します。 接続自体をアクティブのままにします。|
|*mysql.az_load_timezone*|該当なし|該当なし|タイム ゾーン テーブルを読み込み、パラメーター `time_zone` を名前付きの値に設定できるようにします (例 "US/Pacific")。|

## <a name="next-steps"></a>次のステップ
- [データイン レプリケーション](howto-data-in-replication.md) を設定する方法について確認する
- [タイム ゾーン テーブル](howto-server-parameters.md#working-with-the-time-zone-parameter) を使用する方法について確認する
