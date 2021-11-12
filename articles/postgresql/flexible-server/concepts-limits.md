---
title: 制限 - Azure Database for PostgreSQL - フレキシブル サーバー
description: この記事では、接続数やストレージ エンジンのオプションなど、Azure Database for PostgreSQL - フレキシブル サーバーの制限について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/26/2021
ms.openlocfilehash: c28e3b84251c645ff690098950066b3bb653f3df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467533"
---
# <a name="limits-in-azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL - フレキシブル サーバーの制限


次のセクションでは、データベース サービス容量と機能の制限について説明します。 リソース (コンピューティング、メモリ、ストレージ) レベルの詳細については、[コンピューティングとストレージ](concepts-compute-storage.md)の記事を参照してください。

## <a name="maximum-connections"></a>最大接続数

価格レベルと仮想コアごとの最大接続数は以下のとおりです。 Azure システムには、Azure Database for PostgreSQL - フレキシブル サーバーを監視する 3 つの接続が必要です。

| SKU 名             | 仮想コア | メモリ サイズ | 最大接続数 | 最大ユーザー接続 |
|----------------------|--------|-------------|-----------------|----------------------|
| **バースト可能**        |        |             |                 |                      |
| B1ms                 | 1      | 2 GiB       | 50              | 47                   |
| B2s                  | 2      | 4 GiB       | 100             | 97                   |
| **汎用**  |        |             |                 |                      |
| D2s_v3  / D2ds_v4    | 2      | 8 GiB       | 859             | 856                  |
| D4s_v3  / D4ds_v4    | 4      | 16 GiB      | 1719            | 1716                 |
| D8s_v3  / D8ds_V4    | 8      | 32 GiB      | 3438            | 3435                 |
| D16s_v3 / D16ds_v4   | 16     | 64 GiB      | 5000            | 4997                 |
| D32s_v3 / D32ds_v4   | 32     | 128 GiB     | 5000            | 4997                 |
| D48s_v3 / D48ds_v4   | 48     | 192 GiB     | 5000            | 4997                 |
| D64s_v3 / D64ds_v4   | 64     | 256 GiB     | 5000            | 4997                 |
| **メモリ最適化** |        |             |                 |                      |
| E2s_v3  / E2ds_v4    | 2      | 16 GiB      | 1719            | 1716                 |
| E4s_v3  / E4ds_v4    | 4      | 32 GiB      | 3438            | 3433                 |
| E8s_v3  / E8ds_v4    | 8      | 64 GiB      | 5000            | 4997                 |
| E16s_v3 / E16ds_v4   | 16     | 128 GiB     | 5000            | 4997                 |
| E20ds_v4             | 20     | 160 GiB     | 5000            | 4997                 |
| E32s_v3 / E32ds_v4   | 32     | 256 GiB     | 5000            | 4997                 |
| E48s_v3 / E48ds_v4   | 48     | 384 GiB     | 5000            | 4997                 |
| E64s_v3 / E64ds_v4   | 64     | 432 GiB     | 5000            | 4997                 |

接続数が制限を超えると、次のエラーが表示される場合があります。
> FATAL:  sorry, too many clients already. (致命的: 申し訳ありません。クライアントが多すぎます。)

> [!IMPORTANT]
> 最適なエクスペリエンスを得るために、pgBouncer のような接続プール マネージャーを使用して、接続を効率的に管理することをお勧めします。 Azure Database for PostgreSQL - フレキシブル サーバーでは、[組み込みの接続プールの管理ソリューション](concepts-pgbouncer.md)として PgBouncer が提供されます。 

PostgreSQL 接続はアイドル状態であっても、約 10 MB のメモリを占有する可能性があります。 また、新しい接続の作成には時間もかかります。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 接続プールを使用すると、アイドル状態の接続の数を削減し、既存の接続を再利用して問題を回避できます。 詳細については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)を参照してください。

## <a name="functional-limitations"></a>機能制限

### <a name="scale-operations"></a>スケール操作

- サーバー ストレージをスケーリングするには、サーバーを再起動する必要があります。
- サーバー ストレージは 2 倍ずつでのみスケーリングできます。詳細については、[コンピューティングとストレージ](concepts-compute-storage.md)に関する記事を参照してください。
- 現在、サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード

- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。 次のメジャー バージョンにアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](../howto-migrate-using-dump-and-restore.md)します。

### <a name="storage"></a>ストレージ

- ストレージのサイズは、一度構成すると縮小できません。 必要なストレージ サイズで新しいサーバーを作成し、手動[ダンプと復元](../howto-migrate-using-dump-and-restore.md)を実行して、データベースを新しいサーバーに移行する必要があります。
- 現時点では、ストレージの自動拡張機能は使用できません。 使用状況を監視し、ストレージのサイズを大きくしてください。 
- ストレージの使用率が 95% に達した場合、または使用可能な容量が 5 GiB 未満の場合は、ディスクがいっぱいという状況に関連するエラーを回避するために、サーバーが **読み取り専用モード** に自動で切り替わります。 
- ストレージ サイズの増加などの処置を事前に行えるように、`storage used` または `storage percent` が特定のしきい値を超えた場合のアラート ルールを設定することをお勧めします。 たとえば、ストレージの使用率の割合が 80% を超えた場合のアラートを設定できます。
  
### <a name="networking"></a>ネットワーキング

- VNET との間での移動は、現在はサポートされていません。
- VNET 内のデプロイとパブリック アクセスの組み合わせは、現在はサポートされていません。
- ファイアウォール規則は VNET ではサポートされていません。代わりに、ネットワーク セキュリティ グループを使用できます。
- パブリック アクセス データベース サーバーでは、 `postgres_fdw` などを使用してパブリック インターネットに接続できます。このアクセスを制限することはできません。 VNET ベースのサーバーでは、ネットワーク セキュリティ グループを使用して、送信アクセスを制限することができます。

### <a name="high-availability-ha"></a>高可用性 (HA)

- [ゾーン冗長 HA の制限事項に関するドキュメント](concepts-high-availability.md#zone-redundant-high-availability---limitations) ページを参照してください。

### <a name="availability-zones"></a>可用性ゾーン

- 別の可用性ゾーンにサーバーを手動で移動することは現在、サポートされていません。
- HA スタンバイ サーバーの可用性ゾーンは手動では構成できません。

### <a name="postgres-engine-extensions-and-pgbouncer"></a>Postgres エンジン、拡張機能、PgBouncer

- Postgres 10 以前はサポートされていません。 以前のバージョンの Postgres が必要な場合は、 [単一サーバー](../overview-single-server.md) オプションを使用することをお勧めします。
- 現時点では、拡張機能のサポートは Postgres `contrib` 拡張機能に限定されています。
- 現時点では、バースト可能のサーバーには、組み込みの PgBouncer 接続プーラーを使用できません。
- 組み込みの PgBouncer を使用した接続では、SCRAM 認証はサポートされていません。

### <a name="stopstart-operation"></a>停止/開始操作

- 7 日間を超えてサーバーを停止したままにすることはできません。

### <a name="scheduled-maintenance"></a>予定メンテナンス

- 既に予定されているアップグレードまでの時間が 5 日未満の時点でメンテナンス期間を変更しても、そのアップグレードには影響しません。 変更が有効になるのは、予定された次回のメンテナンス時になります。

### <a name="backing-up-a-server"></a>サーバーのバックアップ

- バックアップはシステムによって管理されます。現時点では、これらのバックアップを手動で実行する方法はありません。 代わりに `pg_dump` を使用することをお勧めします。
- バックアップは常に (差分バックアップではなく) スナップショット ベースの完全バックアップであるため、バックアップ ストレージの使用率が高くなる可能性があります。 トランザクション ログ (先書きログ - WAL) は、完全/差分バックアップとは別のものであり、継続的にアーカイブされることに注意してください。

### <a name="restoring-a-server"></a>サーバーの復元

- ポイントインタイム リストア機能の使用時には、その基になるサーバーと同じコンピューティング構成とストレージ構成で、新しいサーバーが作成されます。
- VNET ベースのデータベース サーバーは、バックアップから復元すると、同じ VNET に復元されます。
- 復元中に作成される新しいサーバーには、元のサーバーに存在するファイアウォール規則はありません。 この新しいサーバー用にファイアウォール規則を別に設定する必要があります。
- 削除されたサーバーへの復元はサポートされていません。
- リージョン間の復元はサポートされていません。

### <a name="other-features"></a>その他の機能

* Azure AD 認証は、まだサポートされていません。 Azure AD 認証が必要な場合は、 [単一サーバー](../overview-single-server.md) オプションを使用することをお勧めします。
* 読み取りレプリカは、まだサポートされていません。 読み取りレプリカが必要な場合は、 [単一サーバー](../overview-single-server.md) オプションを使用することをお勧めします。
* 別のサブスクリプションへのリソースの移動はサポートされていません。 


## <a name="next-steps"></a>次のステップ

- [コンピューティングとストレージのオプションに使用可能なもの](concepts-compute-storage.md)について理解する
- [サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)について学習します
- [Azure Portal を使用して Azure Database for PostgreSQL でサーバーをバックアップして復元する方法](how-to-restore-server-portal.md)を確認します
