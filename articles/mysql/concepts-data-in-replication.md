---
title: データを Azure Database for MySQL にレプリケートする
description: この記事では、Azure Database for MySQL のデータイン レプリケーションについて説明します。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294906"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>データを Azure Database for MySQL にレプリケートする

データイン レプリケーションでは、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスで実行中の MySQL サーバーから Azure Database for MySQL サービスにデータを同期することができます。 データイン レプリケーションは、MySQL のネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。 

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか
データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MySQL 間のデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MySQL と、別のクラウド プロバイダー 間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ
プライマリ サーバー上の [*mysql システム データベース*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)はレプリケートされません。 プライマリ サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 プライマリ サーバーでアカウントを作成し、そのアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー側で同じアカウントを手動で作成しします。 システム データベースに含まれているテーブルの詳細については、[MySQL のマニュアル](https://dev.mysql.com/doc/refman/5.7/en/system-database.html)を参照してください。

### <a name="requirements"></a>必要条件
- プライマリ サーバーのバージョンは、MySQL version 5.6 以上である必要があります。 
- プライマリ サーバーとレプリカ サーバーのバージョンは同じである必要があります。 たとえば、両方が MySQL 5.6 バージョンであるか、両方が MySQL バージョン 5.7 である必要があります。
- 各テーブルには主キーが必要です。
- プライマリ サーバーでは、MySQL InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とプライマリ サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。

### <a name="other"></a>その他
- グローバル トランザクション ID (GTID) はサポートされていません。

## <a name="next-steps"></a>次の手順
- [データイン レプリケーションを設定する](howto-data-in-replication.md)方法を確認する
