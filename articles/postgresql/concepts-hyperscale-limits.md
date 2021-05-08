---
title: 制限と制約事項 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) サーバー グループの現在の制限
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023850"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – Hyperscale (Citus) の制限と制約事項

次のセクションでは、Hyperscale (Citus) サービスの容量と機能の制限について説明します。

## <a name="maximum-connections"></a>最大接続数

すべての PostgreSQL 接続は (アイドル接続であっても) 10 MB 以上のメモリを使用するため、同時接続数を制限することが重要です。 ノードを正常に保つために Microsoft が選択した制限は次のとおりです。

* コーディネーター ノード
   * 最大接続数: 該当なし
   * 最大ユーザー接続数: 297
* ワーカー ノード
   * 最大接続数: 600
   * 最大ユーザー接続数: 597

> [!NOTE]
> [プレビュー機能](hyperscale-preview-features.md)が有効になっているサーバー グループでは、コーディネーターに対する接続の制限が若干異なります。
>
> * コーディネーター ノードの最大接続数
>    * 0 から 3 の仮想コアの場合は 300
>    * 4 から 15 の仮想コアの場合は 500
>    * 16 以上の仮想コアの場合は 1000

これらの制限を超えて接続しようとすると、エラーが発生して失敗します。 ノードの監視用に 3 つの接続がシステムによって予約されます。そのため、ユーザー クエリに使用できる接続が接続の合計数よりも 3 つ少なくなります。

### <a name="connection-pooling"></a>接続のプール

新しい接続の確立には時間がかかります。 これは、短時間の接続を多数要求するほとんどのアプリケーションの妨げになります。 アイドル状態のトランザクションを減らす一方で、既存の接続を再利用するために、接続プーラーを使用することをお勧めします。 詳細については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717)を参照してください。

独自の接続プーラーを実行することも、Azure で管理される PgBouncer を使用することもできます。

#### <a name="managed-pgbouncer-preview"></a>マネージド PgBouncer (プレビュー)

> [!IMPORTANT]
> Hyperscale (Citus) のマネージド PgBouncer 接続プーラーは、現在プレビューの段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
>
> その他の新機能については、[Hyperscale (Citus) のプレビュー機能](hyperscale-preview-features.md)に関するページで全一覧をご覧いただけます。

PgBouncer などの接続プーラーを使用すると、より多くのクライアントが同時にコーディネーター ノードに接続できます。 アプリケーションではプーラーに接続し、プーラーでは宛先データベースにコマンドを中継します。

クライアントで PgBouncer を介して接続する場合、データベースでアクティブに実行できる接続の数は変わりません。 代わりに、PgBouncer では余分な接続をキューに登録して、データベースの準備が整ったときに実行します。

Hyperscale (Citus) では、サーバー グループ (プレビュー) 用の PgBouncer のマネージド インスタンスが提供されるようになりました。 最大 2,000 の同時クライアント接続がサポートされます。
PgBouncer 経由で接続するには、次の手順に従います。

1. Azure portal でサーバー グループの **[接続文字列]** ページにアクセスします。
2. **[PgBouncer の接続文字列]** チェック ボックスをオンにします。 (一覧表示される接続文字列が変わります)。
3. 新しい文字列を使用して接続するようにクライアント アプリケーションを更新します。

## <a name="storage-scaling"></a>ストレージのスケーリング

コーディネーターおよびワーカー ノードのストレージは、スケールアップ (拡張) できますが、スケールダウン (縮小) することはできません。

## <a name="storage-size"></a>ストレージ サイズ

コーディネーターおよびワーカー ノードでは、最大 2 TiB のストレージがサポートされます。 ノードとクラスターのサイズについては、[上記](concepts-hyperscale-configuration-options.md#compute-and-storage)の使用可能なストレージ オプションと IOPS 計算を参照してください。

## <a name="database-creation"></a>データベースの作成

Azure portal では、Hyperscale (Citus) サーバー グループごとに 1 つのデータベース (`citus` データベース) のみに接続するための資格情報が提供されます。 現在、別のデータベースを作成することはできないため、CREATE DATABASE コマンドはエラーで失敗します。

## <a name="columnar-storage"></a>カラム型ストレージ

Hyperscale (Citus) には現在、[カラム型テーブル](concepts-hyperscale-columnar.md)に次の制約事項があります。

* 圧縮は、メモリ内ではなくディスク上にあります
* 追加のみ (更新や削除のサポートなし)
* 領域回復なし (たとえば、ロールバックされたトランザクションによってディスク領域が引き続き消費される可能性があります)
* インデックスのサポート、インデックス スキャン、またはビットマップ インデックス スキャンなし
* Tidscan なし
* サンプル スキャンなし
* TOAST サポートなし (大きな値はインラインでサポート)
* ON CONFLICT ステートメントのサポートなし (ターゲットが指定されていない DO NOTHING アクションを除く)。
* タプル ロックのサポートなし (SELECT ... FOR SHARE、SELECT ... FOR UPDATE)
* シリアル化可能分離レベルのサポートなし
* PostgreSQL サーバー バージョン 12 以降のみのサポート
* 外部キー、一意の制約、または除外制約のサポートなし
* 論理デコードのサポートなし
* ノード内並列スキャンのサポートなし
* AFTER ... FOR EACH ROW トリガーのサポートなし
* UNLOGGED カラム型テーブルなし
* TEMPORARY カラム型テーブルなし

## <a name="next-steps"></a>次のステップ

[ポータルで Hyperscale (Citus) サーバー グループを作成する](quickstart-create-hyperscale-portal.md)方法について学習します。
