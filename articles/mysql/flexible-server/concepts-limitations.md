---
title: 制限 - Azure Database for MySQL - フレキシブル サーバー
description: この記事では、接続数やストレージ エンジンのオプションなど、Azure Database for MySQL - フレキシブル サーバーでの制限について説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: 85718f5c9f3705d5530a2528c07a5a5c40a971c5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468387"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーでの制限

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、Azure Database for MySQL フレキシブル サーバー サービスで制限事項について説明します。 MySQL データベース エンジンでの[一般的な制限事項](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html)も適用されます。 リソースの制限 (コンピューティング、メモリ、ストレージ) の詳細については、[コンピューティングとストレージ](concepts-compute-storage.md)に関する記事を参照してください。

## <a name="server-parameters"></a>サーバー パラメーター

> [!NOTE]
> `max_connections` や `innodb_buffer_pool_size` など、サーバー パラメーターの最小/最大値を求めている場合、この情報はサーバー パラメーターの概念 ([サーバー パラメーター](./concepts-server-parameters.md)に関する記事) に移動しました。

Azure Database for MySQL では、サーバー パラメーターの値のチューニングがサポートされています。 一部のパラメーターの最小値と最大値 ( `max_connections`、`join_buffer_size`、`query_cache_size` など) は、サーバーのコンピューティング レベルとコンピューティング サイズによって決まります。 これらの制限の詳細については、[サーバー パラメーター](./concepts-server-parameters.md)に関する記事を参照してください。

## <a name="storage-engines"></a>ストレージ エンジン

MySQL では多くのストレージ エンジンがサポートされています。 Azure Database for MySQL フレキシブル サーバーでサポートされている、およびサポートされていないストレージ エンジンの一覧は次のとおりです。

### <a name="supported"></a>サポートされています
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>サポートされていない
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>特権とデータ操作のサポート

多くのサーバー パラメーターおよび設定によって、誤ってサーバー パフォーマンスを低下させたり、MySQL サーバーの ACID プロパティを負数にしてしまったりする恐れがあります。 製品レベルでサービスの整合性と SLA を維持するため、このサービスでは複数のロールは公開されていません。

MySQL サービスでは、基になるファイル システムに直接アクセスすることはできません。 一部のデータ操作コマンドはサポートされていません。

### <a name="unsupported"></a>サポートされていない

次のものはサポートされていません。
- DBA ロール:制限付き。 または、管理者ユーザー (新しいサーバーの作成時に作成されます) を使用して、ほとんどの DDL ステートメントと DML ステートメントを実行できます。
- SUPER 権限:同様に、[SUPER 権限](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super)は制限されています。
- DEFINER: 作成するには SUPER 権限が必要であり、制限されています。 バックアップを使用してデータをインポートする場合、mysqldump の実行時に `CREATE DEFINER` コマンドを手動で、または `--skip-definer` コマンドを使用して削除します。
- システム データベース:[mysql システム データベース](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)は読み取り専用であり、さまざまな PaaS 機能をサポートするために使用されます。 `mysql` システム データベースを変更することはできません。
- `SELECT ... INTO OUTFILE`:サービスではサポートされていません。

### <a name="supported"></a>サポートされています
- `LOAD DATA INFILE` はサポートされていますが、`[LOCAL]` パラメーターで UNC パス (SMB を介してマウントされた Azure ストレージ) を指定する必要があります。 さらに、MySQL クライアント バージョン 8.0 以上を使用している場合は、接続文字列に `-–local-infile=1` パラメーターを含める必要があります。

## <a name="functional-limitations"></a>機能制限

### <a name="zone-redundant-ha"></a>ゾーン冗長 HA
- この構成は、サーバーの作成時にのみ設定できます。
- バースト可能なコンピューティング レベルではサポートされていません。

### <a name="networking"></a>ネットワーキング
- サーバーの作成後に接続方法を変更することはできません。 サーバーが "*プライベート アクセス (VNet 統合)* " で作成されている場合、作成後に "*パブリック アクセス (許可された IP アドレス)* " に変更することはできません。逆も同様です。

### <a name="stopstart-operation"></a>停止/開始操作
- 読み取りレプリカ構成 (ソースとレプリカの両方) ではサポートされていません。

### <a name="scale-operations"></a>スケール操作
- プロビジョニング済みのサーバー ストレージを減らすことはできません。

### <a name="read-replicas"></a>読み取りレプリカ
- ゾーン冗長 HA 構成 (プライマリとスタンバイの両方) ではサポートされていません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行はサポートされていません。 メジャー バージョンをアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](../concepts-migrate-dump-restore.md)します。

### <a name="restoring-a-server"></a>サーバーの復元
- ポイントインタイム リストアでは、基になるソース サーバーと同じコンピューティング構成とストレージ構成で、新しいサーバーが作成されます。 サーバーの作成後に、新しく復元されたサーバーのコンピューティングをスケールダウンできます。
- 削除されたサーバーの復元はサポートされていません。

## <a name="features-available-in-single-server-but-not-yet-supported-in-flexible-server"></a>シングル サーバーでは使用できるが、フレキシブル サーバーではまだサポートされていない機能
Azure Database for MySQL 単一サーバーで使用できるすべての機能が、フレキシブル サーバーで使用できるわけではまだありません。 単一サーバーとフレキシブル サーバーの機能の比較の詳しい一覧については、[Azure での適切な MySQL サーバー オプションの選択](../select-right-deployment-type.md#comparing-the-mysql-deployment-options-in-azure)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure での適切な MySQL サーバー オプションの選択](../select-right-deployment-type.md)について学習する
- [フレキシブル サーバーで使用可能なコンピューティングとストレージのオプション](concepts-compute-storage.md)について理解する
- [サポートされている MySQL のバージョンについて](concepts-supported-versions.md)学習する
- クイックスタート: [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](quickstart-create-server-portal.md)
