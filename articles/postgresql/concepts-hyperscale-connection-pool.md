---
title: 接続のプール - Hyperscale (Citus) - Azure Database for PostgreSQL
description: クライアント データベース接続のスケーリング
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 8331cd2f0fa0df52f550acfdac1d8d3506e415f2
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123318613"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-connection-pooling"></a>Azure Database for PostgreSQL – Hyperscale (Citus) 接続のプール

新しい接続の確立には時間がかかります。 これは、短時間の接続を多数要求するほとんどのアプリケーションの妨げになります。 アイドル状態のトランザクションを減らす一方で、既存の接続を再利用するために、接続プーラーを使用することをお勧めします。 詳細については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)を参照してください。

独自の接続プーラーを実行することも、Azure で管理される PgBouncer を使用することもできます。

## <a name="managed-pgbouncer"></a>マネージド PgBouncer

PgBouncer などの接続プーラーを使用すると、より多くのクライアントが同時にコーディネーター ノードに接続できます。 アプリケーションではプーラーに接続し、プーラーでは宛先データベースにコマンドを中継します。

クライアントで PgBouncer を介して接続する場合、データベースでアクティブに実行できる接続の数は変わりません。 代わりに、PgBouncer では余分な接続をキューに登録して、データベースの準備が整ったときに実行します。

Hyperscale (Citus) では、サーバー グループ用の PgBouncer のマネージド インスタンスが提供されるようになりました。 最大 2,000 の同時クライアント接続がサポートされます。  PgBouncer 経由で接続するには、次の手順に従います。

1. Azure portal でサーバー グループの **[接続文字列]** ページにアクセスします。
2. **[PgBouncer の接続文字列]** チェック ボックスをオンにします。 (一覧表示される接続文字列が変わります)。

   > [!IMPORTANT]
   >
   > このチェック ボックスが存在しない場合、PgBouncer はサーバー グループに対してまだ有効になっていません。 マネージド PgBouncer は、[サポートされているすべてのリージョン](concepts-hyperscale-configuration-options.md#regions)に展開されています。  リージョンで有効にすると、[スケジュール済みメンテナンス](concepts-hyperscale-maintenance.md) イベント中、リージョンの既存サーバー グループに追加されます。

3. 新しい文字列を使用して接続するようにクライアント アプリケーションを更新します。

## <a name="next-steps"></a>次のステップ

Hyperscale (Citus) の[制限と制約事項](concepts-hyperscale-limits.md)に関する詳細を確認してください。
