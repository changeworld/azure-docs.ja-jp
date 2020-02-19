---
title: 制限事項 - Azure Database for MariaDB
description: この記事では、Azure Database for MariaDB の制限 (接続数やストレージ エンジンのオプションなど) について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: df44cbefaec943a2df483f4804650b939c796cb5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191149"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB の制限事項
以降のセクションでは、容量、ストレージ エンジンのサポート、権限のサポート、データ操作ステートメントのサポート、およびデータベース サービスの機能に関する制限事項について説明します。

## <a name="maximum-connections"></a>最大接続数
価格レベルと仮想コアごとの最大接続数は次のとおりです。

|**価格レベル**|**仮想コア数**| **最大接続数**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|General Purpose| 2| 600|
|General Purpose| 4| 1250|
|General Purpose| 8| 2500|
|General Purpose| 16| 5000|
|General Purpose| 32| 10000|
|General Purpose| 64| 20000|
|メモリ最適化| 2| 800|
|メモリ最適化| 4| 2500|
|メモリ最適化| 8| 5000|
|メモリ最適化| 16| 10000|
|メモリ最適化| 32| 20000|

接続数が制限を超えると、次のエラーが表示される場合があります。
> ERROR 1040 (08004):Too many connections (接続が多すぎます)

> [!IMPORTANT]
> 最適なエクスペリエンスを得るために、ProxySQL のような接続プーラーを使用して、接続を効率的に管理することをお勧めします。

MariaDB への新しいクライアント接続を作成するには時間がかかり、一度確立されると、アイドル状態のときでも、これらの接続によってデータベース リソースが消費されます。 ほとんどのアプリケーションでは、短時間の接続を多数要求します。これにより、この状況が悪化します。 結果として、実際のワークロードに使用できるリソースが少なくなるため、パフォーマンスが低下します。 アイドル状態の接続を減らして既存の接続を再利用する接続プーラーは、これを回避するのに役立ちます。 ProxySQL の設定については、[ブログ投稿](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)を参照してください。

## <a name="storage-engine-support"></a>ストレージ エンジンのサポート

### <a name="supported"></a>サポートされています
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>サポートされていない
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>権限のサポート

### <a name="unsupported"></a>サポートされていない
- DBA ロール:多くのサーバー パラメーターおよび設定によって、誤ってサーバー パフォーマンスを低下させたり、DBMS の ACID プロパティを負数にしてしまったりする恐れがあります。 そのため、製品レベルのサービス整合性と SLA を維持するために、このサービスでは、DBA ロールを公開していません。 新しいデータベース インスタンスの作成時に構成される既定のユーザー アカウントによって、ユーザーは管理データベース インスタンスでほとんどの DDL および DML ステートメントを実行できます。
- SUPER 権限:同様に、[SUPER 権限](https://mariadb.com/kb/en/library/grant/#global-privileges)も制限されています。
- DEFINER: 作成するには SUPER 権限が必要であり、制限されています。 バックアップを使用してデータをインポートする場合、mysqldump の実行時に `CREATE DEFINER` コマンドを手動で、または `--skip-definer` コマンドを使用して削除します。

## <a name="data-manipulation-statement-support"></a>データ操作ステートメントのサポート

### <a name="supported"></a>サポートされています
- `LOAD DATA INFILE` はサポートされていますが、`[LOCAL]` パラメーターで UNC パス (SMB を介してマウントされた Azure ストレージ) を指定する必要があります。

### <a name="unsupported"></a>サポートされていない
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>機能制限

### <a name="scale-operations"></a>スケール操作
- Basic 価格レベルとの間の動的スケーリングは現在サポートされていません。
- サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
- PITR 機能を使うと、基になっているサーバーと同じ構成で新しいサーバーが作成されます。
- 削除されたサーバーへの復元はサポートされていません。

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間での事前作成されたサーバーの動的な移動は現在サポートされていません。

### <a name="vnet-service-endpoints"></a>VNet サービス エンドポイント
- VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

### <a name="storage-size"></a>ストレージ サイズ
- 価格レベルごとのストレージ サイズの上限については、[価格レベル](concepts-pricing-tiers.md)に関するページを参照してください。

## <a name="current-known-issues"></a>現時点での既知の問題
- MariaDB サーバー インスタンスでは、接続が確立された後に不正なサーバー バージョンが表示されます。 正確なサーバー インスタンス エンジンのバージョンを取得するには、`select version();` コマンドを使用します。

## <a name="next-steps"></a>次のステップ
- [各サービス レベルで使用できる内容について](concepts-pricing-tiers.md)
- [サポートされている MariaDB Database バージョン](concepts-supported-versions.md)
