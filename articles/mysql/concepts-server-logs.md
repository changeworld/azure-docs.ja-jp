---
title: Azure Database for MySQL のサーバー ログ
description: Azure Database for MySQL で利用できるログと、さまざまなログ記録レベルを有効にするため利用可能なパラメーターについて説明します。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 09/17/2018
ms.openlocfilehash: ac5be20815b552c08e5cd1054bf24d7a10b56498
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124271"
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Azure Database for MySQL のサーバー ログ
Azure Database for MySQL では、ユーザーは低速クエリ ログを使用できます。 トランザクション ログへのアクセスはサポートされていません。 低速クエリ ログは、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するために使用できます。 

MySQL の低速クエリ ログの詳細については、MySQL のリファレンス マニュアルの[低速クエリ ログに関するセクション](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

## <a name="access-server-logs"></a>サーバー ログへのアクセス
Azure Portal と Azure CLI を使用して、Azure Database for MySQL のサーバー ログを一覧表示およびダウンロードできます。

Azure Portal で、ご利用の Azure Database for MySQL サーバーを選択します。 **[監視]** の見出しの下の、**[サーバー ログ]** ページを選択します。

Azure CLI の詳細については、「[Configure and access server logs using Azure CLI (Azure CLI を使用したサーバー ログの構成とアクセス)](howto-configure-server-logs-in-cli.md)」を参照してください。

## <a name="log-retention"></a>ログのリテンション期間
ログは、作成日から最大 7 日間使用できます。 使用可能なログの合計サイズが 7 GB を超える場合は、空き領域を利用できるようになるまで、古いファイルから削除されます。 

ログのローテーションは、24 時間ごとか 7 GB ごとのどちらか早い方のタイミングで行われます。


## <a name="configure-logging"></a>ログの構成 
既定では、低速クエリ ログは無効です。 有効にするには、slow_query_log をオンに設定します。

調整できるその他のパラメーターは次のとおりです。

- **long_query_time**: long_query_time (秒単位) より長いクエリがある場合は、そのクエリが記録されます。 既定値は 10 秒です。
- **log_slow_admin_statements**: オンの場合は、slow_query_log に書き込まれるステートメントに、ALTER_TABLE や ANALYZE_TABLE などの管理ステートメントが含まれます。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリを slow_query_log に記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**: このパラメーターは、低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは、Log_queries_not_using_indexes がオンに設定されている場合に有効です。

低速クエリ ログのパラメーターの完全な説明については、MySQL の[低速クエリ ログのドキュメント](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html)を参照してください。

## <a name="next-steps"></a>次の手順
- [Azure CLI からサーバー ログを構成しアクセスする方法](howto-configure-server-logs-in-cli.md)
