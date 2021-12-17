---
title: Azure Database for MySQL への移行に使用する適切なツールを選択する
description: このトピックでは、お客様が Azure Database for MySQL への移行に使用する適切なツールを選択するのに役立つ意思決定テーブルを示します
ms.service: mysql
author: shriram-muthukrishnan
ms.author: shriramm
ms.reviewer: maghan
ms.topic: how-to
ms.custom: ''
ms.date: 10/12/2021
ms.openlocfilehash: 686874492f44df1171a320aaacf8e1967f8cb060
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269929"
---
# <a name="select-the-right-tools-for-migration-to-azure-database-for-mysql"></a>Azure Database for MySQL への移行に使用する適切なツールを選択する

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>概要

移行は、成功させるのが難しい複数ステップのプロジェクトです。 データベース サーバーをプラットフォーム間で移行するには、データとスキーマを移行するだけでは済みません。 サーバー構成パラメーター、ネットワーク、アクセス制御ルールなど、他にも移動すべきコンポーネントがいくつか存在します。 これらは、移行先の新しいプラットフォームのデータベース サーバーが移行元と同じように機能するために必要です。 

データベースを Azure Database for MySQL に移行する方法の詳細とユース ケースについては、[データベースの移行ガイド](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)をご覧ください。 このドキュメントでは、Azure への MySQL の移行を適切に計画して実行するのに役立つ参照先を示します。 

一般に、移行はオフラインまたはオンラインのいずれかに分類できます。 

- オフライン移行では、移行元サーバーがオフラインになり、移行先サーバーでデータベースのダンプと復元が実行されます。 

- オンライン移行 (移行時のダウンタイムが最小限で済む) では、移行元サーバーの更新が許可されるため、移行ソリューションによって、移行先で最初のダンプと復元を実行しながら、移行元サーバーと移行先サーバー間の継続的な変更のレプリケーションを処理します。 

アプリケーションである程度のダウンタイムを許容できる場合は、シンプルで実行しやすいオフライン移行を選択することを常にお勧めします。 しかし、アプリケーションで許容できるダウンタイムが最小限の場合は、オンライン移行が最適な選択肢です。 大部分の OLTP システム (支払い処理や eコマースなど) の移行は、このカテゴリに分類されます。 

## <a name="decision-table"></a>意思決定テーブル

Azure Database for MySQL への移行に使用する適切なツールを選択するときは、次の表の詳細情報を考慮してください。 

| シナリオ | 推奨されるツール | リンク |
|-------|------|------------|
| 1 TB 以上のデータベースを移動するオフライン移行 | **Mydumper/Myloader** とハイ コンピューティング VM を使用したダンプと復元 | [mydumper または myloader を使用して大規模なデータベースを Azure Database for MySQL に移行する](concepts-migrate-mydumper-myloader.md) <br><br> [大規模なデータベースを Azure Database for MySQL に移行するためのベスト プラクティス](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)|
| 1 TB 未満のデータベースを移動するオフライン移行  | 移行元と移行先の間のネットワーク帯域幅が良好な場合 (高速 Express Route など) は、**Azure DMS** (データベース移行サービス) を使用します  <br><br>   **- または -** <br><br> 移行元と Azure の間のネットワーク帯域幅が低い場合は、**Mydumper/Myloader と高速コンピューティング VM** を使用し、圧縮設定を利用して低速ネットワークで効率的にデータを移動します  <br><br> **- または -** <br><br> **mysqldump** と **MySQL Workbench Export/Import** ユーティリティを使用して、小規模なデータベースのオフライン移行を実行します。  | [チュートリアル: DMS を使用して MySQL を Azure Database for MySQL にオフラインで移行する - Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)<br><br>  [MySQL Workbench を使用して、Amazon RDS for MySQL を Azure Database for MySQL に移行する](how-to-migrate-rds-mysql-workbench.md)<br><br>  [インポートとエクスポート - Azure Database for MySQL](concepts-migrate-import-export.md)|
| オンライン移行 |  **Mydumper/Myloader とデータイン レプリケーションの組み合わせ** <br><br> 小規模なデータベース (100 GB 未満) では、**mysqldump とデータイン レプリケーションの組み合わせ** を検討できます。  これらの方法は、外部とプラットフォーム内のどちらの移行にも適用できます。 | [データイン レプリケーションを構成する - Azure Database for MySQL フレキシブル サーバー](flexible-server/how-to-data-in-replication.md) <br><br> [チュートリアル: 最小限のダウンタイムで Azure Database for MySQL - 単一サーバーを Azure Database for MySQL - フレキシブル サーバーに移行する](howto-migrate-single-flexible-minimum-downtime.md) |
|単一サーバーからフレキシブル サーバーへの移行 |  **オフライン**: [GitHub](https://github.com/Azure/azure-mysql/tree/master/azuremysqltomysqlmigrate) でホストされているカスタム シェル スクリプト。このスクリプトでは、その他のサーバー コンポーネント (セキュリティ設定やサーバー パラメーター構成など) を移動します。 <br><br>**オンライン**: **Mydumper/Myloader とデータイン レプリケーションの組み合わせ** |  [5 つの簡単な手順で Azure Database for MySQL - 単一サーバーからフレキシブル サーバーに移行する](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)<br><br>   [チュートリアル: 最小限のダウンタイムで Azure Database for MySQL - 単一サーバーを Azure Database for MySQL - フレキシブル サーバーに移行する](howto-migrate-single-flexible-minimum-downtime.md)| 

## <a name="next-steps"></a>次のステップ
* [オンプレミスの MySQL を Azure Database for MySQL に移行する](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)

<br><br>
