---
title: Azure Database for PostgreSQL サーバーのクエリ統計コレクションを最適化する
description: この記事では、Azure Database for PostgreSQL サーバーのクエリ統計コレクションを最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 076442d85d7f628504cca95c36f3e99f4d0c5117
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966689"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Azure database for PostgreSQL サーバーのクエリ統計コレクションを最適化する 
この記事では、Azure Database for PostgreSQL サーバーのクエリ統計コレクションの最適化について説明します。

## <a name="using-pgstatsstatements"></a>pg_stats_statements の使用
**pg_stat_statements** は、Azure Database for PostgreSQL で既定で有効になる PostgreSQL 拡張機能です。 この拡張機能では、サーバーで実行されるすべての SQL ステートメントの実行統計を追跡する手段が提供されます。 ただし、このモジュールでは、すべてのクエリの実行がフックされるため、些細とは言えないパフォーマンス コストが発生します。 **pg_stat_statements** を有効にすると、ディスク上のファイルにクエリ テキストが強制的に書き込まれます。

クエリ テキストが長い独自のクエリを使用しているか、**pg_stat_statements** を積極的に監視していないお客様には、最大限のパフォーマンスを得るために `pg_stat_statements.track = NONE` を設定することで **pg_stat_statements** を無効にすることをお勧めします。

一部のお客様のワークロードでは、**pg_stat_statements** を無効にすることで、パフォーマンスが最大 50 パーセント向上したことが確認されています。 ただし、pg_stat_statements の無効化によるトレードオフとして、パフォーマンスに関する問題をトラブルシューティングできなくなります。

`pg_stat_statements.track = NONE` を設定するには:

- Azure portal で、[、[PostgreSQL リソースの管理] ページに移動し、[サーバー パラメーター] ブレードを選択します](howto-configure-server-parameters-using-portal.md)。

![[PostgreSQL サーバー パラメーター] ブレード](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用して、az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE` を実行します。

## <a name="using-query-store"></a>クエリ ストアの使用 
Azure Database for PostgreSQL の[クエリ ストア](concepts-query-store.md)機能では、クエリ統計を追跡するためのパフォーマンスが高い方法を提供しています。この方法を *pg_stats_statements* の代わりに使用することをお勧めします。 

## <a name="next-steps"></a>次の手順
[Azure Portal](howto-configure-server-parameters-using-portal.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用して、`pg_stat_statements.track = NONE` を設定することを検討します。

詳細については、「[クエリ ストアの使用シナリオ](concepts-query-store-scenarios.md)」と「[クエリ ストア関連のベスト プラクティス](concepts-query-store-best-practices.md)」を参照してください。 
