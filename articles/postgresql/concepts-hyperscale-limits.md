---
title: 制限と制約事項 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Hyperscale (Citus) サーバー グループの現在の制限
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: 5bf02173d105ab81807bdc4ee68e3b8f9bc8e0a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746318"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Azure Database for PostgreSQL – Hyperscale (Citus) の制限と制約事項

次のセクションでは、Hyperscale (Citus) サービスの容量と機能の制限について説明します。

## <a name="maximum-connections"></a>最大接続数

すべての PostgreSQL 接続は (アイドル接続であっても) 10 MB 以上のメモリを使用するため、同時接続数を制限することが重要です。 ノードを正常に保つために Microsoft が選択した制限は次のとおりです。

* コーディネーター ノード
   * 最大接続数
       * 0 から 3 の仮想コアの場合は 300
       * 4 から 15 の仮想コアの場合は 500
       * 16 以上の仮想コアの場合は 1000
   * 最大ユーザー接続数
       * 0 から 3 の仮想コアの場合は 297
       * 4 から 15 の仮想コアの場合は 497
       * 16 以上の仮想コアの場合は 997
* ワーカー ノード
   * 最大接続数
       * 600

これらの制限を超えて接続しようとすると、エラーが発生して失敗します。 ノードの監視用に 3 つの接続がシステムによって予約されます。そのため、ユーザー クエリに使用できる接続が接続の合計数よりも 3 つ少なくなります。

### <a name="connection-pooling"></a>接続のプール

[コネクション プール](concepts-hyperscale-connection-pool.md)を使用すれば、接続をさらに拡張できます。 Hyperscale (Citus) は、最大 2,000 の同時クライアント接続用に構成されたマネージド pgBouncer コネクション プールを提供しています。

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

* [ポータルで Hyperscale (Citus) サーバー グループを作成する](quickstart-create-hyperscale-portal.md)方法について学習します。
* [コネクション プール](concepts-hyperscale-connection-pool.md)を有効にする方法について説明します。
