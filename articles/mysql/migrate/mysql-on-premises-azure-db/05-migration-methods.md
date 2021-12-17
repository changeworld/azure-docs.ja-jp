---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: 移行方法'
description: ソースからターゲットにデータを取得するには、MySQL のツールまたは機能を使用して移行を実行する必要があります。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 6a4b0e2dc084168e1bffa539924ad897ac93961e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085004"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-migration-methods"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: 移行方法

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[Planning](04-planning.md)

## <a name="overview"></a>概要

ソースからターゲットにデータを取得するには、MySQL のツールまたは機能を使用して移行を実行する必要があります。

次のステージを開始する前に、評価と計画のステージ全体を完了することが重要です。 この決定と収集されるデータは、移行パスとツールの選択に依存します。

このセクションでは、一般的に使用される次のツールについて説明します。

  - MySQL Workbench

  - mysqldump

  - mydumper と myloader

  - データイン レプリケーション (binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/) では、開発者と管理者が MySQL インスタンスの設計、開発、管理を行える豊富な GUI エクスペリエンスが提供されます。

最新バージョンの MySQL Workbench では、ソースからターゲットにデータベースを移動するときに、高度な[オブジェクト移行機能](https://www.mysql.com/products/workbench/migrate/)が提供されます。

#### <a name="data-import-and-export"></a>データのインポートとエクスポート

MySQL Workbench では、テーブルとデータベース オブジェクトの全体または一部のエクスポートとインポートを行うウィザード ベースの UI が提供されます。 MySQL Workbench の使用方法の例については、「[インポートとエクスポートを使用した MySQL データベースの移行](../../concepts-migrate-import-export.md)」を参照してください。

### <a name="dump-and-restore-mysqldump"></a>ダンプと復元 (mysqldump)

`mysqldump` は通常、MySQL のインストールの一部として提供されます。 これは、データベースを一定の時点まで再構築するために再生できる一連の SQL ステートメントと同じ論理バックアップを作成するために実行できる[クライアント ユーティリティ](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html)です。 `mysqldump` は、大量のデータをバックアップまたは移行するための高速またはスケーラブルなソリューションとして意図されているものではありません。 インデックスの更新に必要なディスク I/O が原因で、大量の SQL 挿入ステートメントを実行すると、パフォーマンスが低下する可能性があります。 ただし、元のスキーマを必要とする他のツールと組み合わせると、`mysqldump` はデータベース スキーマとテーブル スキーマを生成するための最適なツールです。 スキーマを使用すると、ターゲット ランディング ゾーン環境を作成できます。

`mysqldump`  ユーティリティは、データ移行フェーズ中に便利な機能を提供します。 ユーティリティを実行する前に、パフォーマンスに関する考慮事項を評価する必要があります。 「[パフォーマンスに関する考慮事項](../../concepts-migrate-dump-restore.md#performance-considerations)」を参照してください。

### <a name="mydumper-and-myloader"></a>mydumper と myloader

高速移行を必要とする大規模なデータベースがある環境では、[mydumper と myloader](https://github.com/maxbube/mydumper) を使用する必要があります。 これらのツールは C++ で記述されており、マルチスレッド手法を使用して、ターゲット MySQL インスタンスに可能な限り高速にデータを送信します。 `mydumper` と `myloader` は並列処理を利用し、10 倍以上の速度で移行を高速化できます。

パブリック ダウンロードに使用できるツールのバイナリ リリースは、Linux 用にコンパイルされています。 Windows でこれらのツールを実行するには、オープンソース プロジェクトを再コンパイルする必要があります。 ソース コードのコンパイルとリリースの作成は、ほとんどのユーザーにとって簡単な作業ではありません。

### <a name="data-in-replication-binlog"></a>データイン レプリケーション (binlog)

他のデータベース管理システムと同様に、MySQL では [binlog レプリケーション](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)と呼ばれるログ レプリケーション機能が提供されます。 `binlog` レプリケーション機能は、データの移行と読み取りレプリカの作成に役立ちます。

binlog レプリケーションを利用して、オンライン シナリオで [Azure Database for MySQL にデータを移行](../../concepts-data-in-replication.md)します。 データ レプリケーションは、最終的なターゲット データの変更に必要なダウンタイムを減らすのに役立ちます。

`binlog` レプリケーション機能を使用するには、いくつかのセットアップ[要件](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)があります。

  - マスター サーバーでは、MySQL InnoDB エンジンを使用することをお勧めします。 InnoDB 以外のストレージ エンジンを使用している場合は、そのテーブルを InnoDB に移行する必要があります。

  - 移行ユーザーは、バイナリ ログの構成とマスター サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。

  - マスター サーバーで SSL が有効になっている場合は、ドメインに対して指定されている SSL CA 証明書が mysql.az\_replication\_change\_master ストアド プロシージャに含まれていることを確認します。 次の[例](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)と master\_ssl\_ca パラメーターを参照してください。

  - マスター サーバーの IP アドレスが Azure Database for MySQL レプリカ サーバーのファイアウォール規則に追加されていることを確認します。 Azure portal または Azure CLI を使用してファイアウォール規則を更新します。

  - マスター サーバーをホストしているコンピューターで、ポート 3306 の受信トラフィックと送信トラフィックの両方が確実に許可されているようにします。

  - マスター サーバーが、ソースからターゲットへのアクセス可能な IP アドレス (パブリックまたはプライベート) を持っていることを確認します。

レプリケーションを使用して移行を実行するには、「[Azure Database for MySQL のデータイン レプリケーションを構成する方法](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication)」を参照してください。

`binlog` レプリケーション方法には高い CPU 要件と追加のストレージ要件があります。 移行ユーザーは、オンライン移行中にソース システムにかかる負荷をテストし、それが許容されるかどうかを判断する必要があります。

### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

[Azure Database Migration Service (DMS)](https://azure.microsoft.com/services/database-migration/) は、管理者が移行のさまざまな設定を追跡し、必要に応じて再利用できる Azure クラウドベースのツールです。 DMS は、さまざまなソースと宛先を指す設定を使用して移行プロジェクトを作成することで機能します。 [オフライン移行](../../../dms/tutorial-mysql-azure-mysql-offline-portal.md)がサポートされています。 さらに、オンプレミスのデータ ワークロードと、Amazon Relational Database Service (RDS) MySQL などのクラウドベースのワークロードもサポートしています。

DMS サービスはオンライン ツールですが、タスクを完了するために MySQL の `binlog` レプリケーション機能に依存しています。 現在、DMS ではオフライン移行プロセスが部分的に自動化されています。 DMS では、ターゲット Azure Database for MySQL インスタンス内に一致するスキーマを生成し、適用する必要があります。 `mysqldump` クライアント ユーティリティを使用してスキーマをエクスポートできます。

### <a name="fastestminimum-downtime-migration"></a>最速/最小ダウンタイムの移行

データを移行するには、多くのパスがあります。 選択するパスの決定は、移行チームのスキル セットと、データベースとアプリケーションの所有者が受け入れるダウンタイムの長さの関数です。 一部のツールではマルチスレッドの並列データ移行アプローチがサポートされていますが、他のツールはテーブル データのみを簡単に移行するように設計されています。

最も高速で最も完全なパスは、(MySQL で直接、または DMS を介して) `binlog` レプリケーションを使用する方法ですが、主キーを追加するコストが発生します。

### <a name="decision-table"></a>意思決定テーブル

MySQL ワークロードを移行するために WWI が実行できるパスは多数あります。 可能なパスと、それぞれの長所と短所の表を示します。

| 目標 | 説明 | ツール | 前提条件 | 長所 | 短所 |
|-----------|-------------|------|---------------|------------|---------------|
| **可能な限り最速の移行** | 並列アプローチ| mydumper と myloader | Linux | 高度に並列化 | ターゲットの調整 |
| **オンライン移行** | 可能な限りソースを有効な状態に維持 | binlog | なし | シームレス | 追加の処理とストレージ |
| **オフライン移行** | 可能な限りソースを有効な状態に維持 | Database Migration Service (DMS)| なし | 反復可能なプロセス | データのみに制限、すべての MySQL バージョンをサポート |
| **高度にカスタマイズされたオフライン移行** | オブジェクトを選択的にエクスポート | mysqldump | なし | 高度にカスタマイズ可能 | 手動 |
| **半自動化されたオフライン移行** | UI ベースのエクスポートとインポート | MySQL Workbench | ダウンロードおよびインストールする | 半自動 | 一般的なスイッチのセットのみをサポート |

### <a name="wwi-scenario"></a>WWI のシナリオ

WWI は、最初の移行ワークロードとして会議データベースを選択しました。 このワークロードが選択されたのは、年間の会議スケジュールが途切れることによるリスクが最も少なく、利用可能なダウンタイムが最も長いためです。 さらに、移行チームの評価に基づいて、MySQL Workbench を使用してオフライン移行を試行することを決定しました。

### <a name="migration-methods-checklist"></a>移行方法のチェックリスト

  - ターゲットとソースの環境で適切な方法が選択されていることを確認します。

  - その方法がビジネス要件を満たしていることを確認します。

  - データ ワークロードがその方法をサポートしているかどうかを常に確認します。  


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Test Plans](./06-test-plans.md)