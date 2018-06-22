---
title: Azure Database for MySQL データイン レプリケーションのストアド プロシージャ
description: この記事では、データイン レプリケーションに使用されるすべてのストアド プロシージャを紹介します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266110"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Azure Database for MySQL データイン レプリケーションのストアド プロシージャ

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MySQL サーバーから Azure Database for MySQL サービスにデータを同期することができます。

次のストアド プロシージャは、プライマリとレプリカ間のデータイン レプリケーションの設定または削除に使用されます。

|**ストアド プロシージャ名**|**入力パラメーター**|**出力パラメーター**|**使用上の注意**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_host<br/>master_log_file<br/>master_log_file<br/>master_ssl_ca|該当なし|SSL モードを使用してデータを転送するには、CA 証明書のコンテキストを master_ssl_ca パラメーターに渡します。 </br><br>SSL を使用せずにデータを転送するには、空の文字列を master_ssl_ca パラメーターに渡します。|
|*mysql.az_replication _start*|該当なし|該当なし|レプリケーションを開始します。|
|*mysql.az_replication _stop*|該当なし|該当なし|レプリケーションを停止します。|
|*mysql.az_replication _remove_primary*|該当なし|該当なし|プライマリとレプリカ間のレプリケーション関係を削除します。|
|*mysql.az_replication_skip_counter*|該当なし|該当なし|1 つのレプリケーション エラーをスキップします。|

Azure Database for MySQL 内でプライマリとレプリカの間のデータイン レプリケーションを設定するには、 [データイン レプリケーションを構成する方法](howto-data-in-replication.md)を参照してください。