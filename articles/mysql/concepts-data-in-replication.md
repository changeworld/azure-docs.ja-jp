---
title: データイン レプリケーション - Azure Database for MySQL
description: データイン レプリケーションを使用して、外部のサーバーから Azure Database for MySQL サービスに同期する方法について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 83ad2d4f392afb6bb33c99a5449b9bc8ceeaa058
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312809"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>データを Azure Database for MySQL にレプリケートする

データイン レプリケーションでは、外部の MySQL サーバーから Azure Database for MySQL サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。 データイン レプリケーションは、MySQL ネイティブであるバイナリ ログ (binlog) ファイル位置ベースまたは GTID ベースのレプリケーションに基づきます。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか

データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MySQL の間でデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MySQL と、別のクラウド プロバイダー 間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。

移行シナリオについては、[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) を使用してください。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ

ソース サーバー上の ["*mysql システム データベース*"](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) はレプリケートされません。 さらに、ソース サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 ソース サーバー上にアカウントを作成し、このアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー上に同じアカウントを手動で作成します。 システム データベースに含まれているテーブルの詳細については、[MySQL のマニュアル](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)を参照してください。

### <a name="filtering"></a>フィルター処理

(オンプレミス、仮想マシン、あるいは他のクラウド プロバイダーによってホストされているデータベース サービスでホストされている) ソース サーバーからのテーブル複製をスキップするために、`replicate_wild_ignore_table` パラメーターがサポートされています。 任意で、[Azure portal](howto-server-parameters.md) または [Azure CLI](howto-configure-server-parameters-using-cli.md) を使用し、Azure でホストされているレプリカ サーバー上でこのパラメーターを更新します。

このパラメーターの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table)を参照してください。

## <a name="supported-in-general-purpose-or-memory-optimized-tier-only"></a>General Purpose または Memory Optimized (メモリ最適化) のレベルでのみサポートされる

データイン レプリケーションは、General Purpose と Memory Optimized (メモリ最適化) の価格レベルでのみサポートされます。

### <a name="requirements"></a>必要条件

- ソース サーバーのバージョンは、MySQL バージョン 5.6 以上である必要があります。
- ソース サーバーとレプリカ サーバーのバージョンは同じである必要があります。 たとえば、両方が MySQL 5.6 バージョンであるか、両方が MySQL バージョン 5.7 である必要があります。
- 各テーブルには主キーが必要です。
- ソース サーバーでは、MySQL InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とソース サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。
- ソース サーバーで SSL が有効になっている場合は、ドメインに対して提供されている SSL CA 証明書が `mysql.az_replication_change_master` または `mysql.az_replication_change_master_with_gtid` のストアド プロシージャで指定されていることを確認します。 次の[例](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)と `master_ssl_ca` パラメーターを参照してください。
- ソース サーバーの IP アドレスが Azure Database for MySQL レプリカ サーバーのファイアウォール規則に追加されていることを確認します。 [Azure portal](./howto-manage-firewall-using-portal.md) または [Azure CLI](./howto-manage-firewall-using-cli.md) を使用してファイアウォール規則を更新します。
- ソース サーバーをホストしているマシンのポート 3306 で受信と送信の両方のトラフィックが許可されていることを確認します。
- ソース サーバーに **パブリック IP アドレス** があるか、DNS がパブリックにアクセス可能か、またはソース サーバーに完全修飾ドメイン名 (FQDN) があることを確認します。

## <a name="next-steps"></a>次のステップ

- [データイン レプリケーションを設定する](howto-data-in-replication.md)方法を確認する
- [読み取りレプリカを使用した Azure でのレプリケート](concepts-read-replicas.md)について確認する
- [DMS を使用して、最小限のダウンタイムでデータを移行する](howto-migrate-online.md)方法を確認する
