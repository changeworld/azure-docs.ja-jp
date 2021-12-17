---
title: データイン レプリケーション - Azure Database for MySQL フレキシブル
description: データイン レプリケーションを使用して、外部のサーバーから Azure Database for MySQL フレキシブル サービスに同期する方法について説明します。
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/08/2021
ms.openlocfilehash: 5c0a63d140bb06bb14bb9c81dd17b0b057c3c8f4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422788"
---
# <a name="replicate-data-into-azure-database-for-mysql-flexible--server"></a>データを Azure Database for MySQL フレキシブル サーバーにレプリケートする

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

データイン レプリケーションでは、外部の MySQL サーバーから Azure Database for MySQL フレキシブル サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン内、Azure Database for MySQL Single Server、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。 データイン レプリケーションは、バイナリ ログ (binlog) ファイルの位置ベースに基づいています。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。

> [!Note]
> GTID ベースのレプリケーションは、現在、Azure Database for MySQL フレキシブル サーバーでサポートされていません。<br>
> ゾーン冗長高可用性サーバーでのデータイン レプリケーションの構成は、サポートされていません。

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか

データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MySQL フレキシブル サーバー間のデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MySQL フレキシブル サーバー と、別のクラウド プロバイダー 間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。
- **移行:** お客様は、[MyDumper/MyLoader](https://centminmod.com/mydumper.html) などのオープン ソース ツールとデータイン レプリケーションを使用して、最小限の時間の移行を実行できます。 データイン レプリケーションでは、ソースデータベースから宛先データベースへの実稼働負荷の選択的なカットオーバーが可能です。

移行シナリオについては、[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) を使用してください。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ

ソース サーバー上の ["*mysql システム データベース*"](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) はレプリケートされません。 さらに、ソース サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 ソース サーバー上にアカウントを作成し、このアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー上に同じアカウントを手動で作成します。 システム データベースに含まれているテーブルの詳細については、[MySQL のマニュアル](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)を参照してください。

### <a name="data-in-replication-not-supported-on-ha-enabled-servers"></a>HA 対応のサーバーではサポートされないデータイン レプリケーション
ゾーン冗長高可用性サーバーでのデータイン レプリケーションの構成は、サポートされていません。 HA が有効になっているサーバーでは、レプリケーション `mysql.az_replication_*` のストアド プロシージャを利用できません。

### <a name="filtering"></a>フィルター処理

テーブルのレプリケーション フィルター作成に使用されたパラメーター `replicate_wild_ignore_table` の変更は、Azure Database for MySQL フレキシブル サーバーではサポートされていません。

### <a name="requirements"></a>必要条件

- ソース サーバーのバージョンは、MySQL バージョン 5.7 以上である必要があります。
- ソース サーバーとレプリカ サーバーのバージョンに対して同じバージョンを使用する方法をお勧めしています。 たとえば、両方が MySQL 5.7 バージョンであるか、両方が MySQL バージョン 8.0 である必要があります。
- 各テーブルに主キーを含めることを推奨します。 主キーのないテーブルがある場合は、レプリケーションの速度が低下する可能性があります。
- ソース サーバーでは、MySQL InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とソース サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。
- ソース サーバーで SSL が有効になっている場合は、ドメインに対して指定されている SSL CA 証明書が `mysql.az_replication_change_master` ストアド プロシージャに含まれていることを確認します。 次の[例](./how-to-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)と `master_ssl_ca` パラメーターを参照してください。
- ソース サーバーをホストしているマシンのポート 3306 で受信と送信の両方のトラフィックが許可されていることを確認します。
- ソース サーバーに **パブリック IP アドレス** があるか、DNS がパブリックにアクセス可能か、またはソース サーバーに完全修飾ドメイン名 (FQDN) があることを確認します。
- パブリック アクセスの場合、ソース サーバーにパブリック IP アドレスがあり、DNS がパブリックにアクセス可能であり、またはソース サーバーに完全修飾ドメイン名 (FQDN) があることを確保します。
- プライベート アクセスの場合、ソース サーバー名を解決できることと、Azure Database for MySQL インスタンスが実行されている VNet からアクセスできることを確保します。詳細については「[Azure 仮想ネットワーク内のリソースの名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください

## <a name="next-steps"></a>次のステップ

- [データイン レプリケーションを設定する](how-to-data-in-replication.md)方法を確認する
- [読み取りレプリカを使用した Azure でのレプリケート](concepts-read-replicas.md)について確認する
