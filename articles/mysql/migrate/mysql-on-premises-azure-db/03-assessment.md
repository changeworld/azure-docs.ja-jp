---
title: 'オンプレミスの MySQL を Azure Database for MySQL に移行する: 評価'
description: MySQL ワークロードの移行に進む前に、多くのことについて評価を行う必要があります。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 4510cbe04181da7badb10c61bd510bed084580e8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284264"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-assessment"></a>オンプレミスの MySQL を Azure Database for MySQL に移行する: 評価

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>前提条件

[代表的なユース ケース](02-representative-use-case.md)

## <a name="overview"></a>概要

MySQL ワークロードの移行に進む前に、多くのことについて評価を行う必要があります。 これには、データ、ホスティング環境、アプリケーションのワークロードを分析し、間もなく移行されるワークロードをホストするために Azure ランディング ゾーンの構成と準備が適切に行われていることを検証することが含まれます。

## <a name="limitations"></a>制限事項

Azure Database for MySQLは、サービスとしてのプラットフォームとして実行される MySQL コミュニティ エディションの完全にサポートされているバージョンです。 ただし、初期評価を行うときによく理解しておく必要のある[いくつかの制限](../../concepts-limits.md)があります。

最も重要なものは次のとおりです。

  - `InnoDB` と `Memory` だけであるストレージ エンジンのサポート

  - 制限されている `Privilege` のサポート (`DBA`、`SUPER`、`DEFINER`)

  - 無効なデータ操作ステートメント (`SELECT ... INTO OUTFILE`、`LOAD DATA INFILE`)

  - 大がかりな自動データベース移行 (5.6 から 5.7、5.7 から 8.0)

  - [MySQL Server User-Defined Function (UDF)](https://dev.mysql.com/doc/refman/5.7/en/server-udfs.html) を使用する場合、実行可能なホスティング オプションは Azure ホステッド VM のみです。`so` または `dll` コンポーネントを Azure Database for MySQL にアップロードする機能はありません。

その他の項目の多くは、運用データのワークロード ライフサイクル管理の一環として管理者が理解する必要がある運用に関する事柄です。 このガイドでは、このような運用に関する事柄の多くは、「移行後の管理」セクションで説明されています。

## <a name="mysql-versions"></a>MySQL のバージョン

MySQL には、1995 年から始まる長い歴史があります。 それ以降、広く使用されるデータベース管理システムとして進化してきました。 Azure Database for MySQL は、MySQL バージョン 5.6 のサポートから開始し、5.7 を経て、最近は 8.0 になっています。 最新の Azure Database for MySQL バージョンのサポートについては、「[サポートされている Azure Database for MySQL サーバー バージョン](../../concepts-supported-versions.md)」を参照してください。 「移行後の管理」セクションでは、アップグレード (5.7.20 から 5.7.21 など) が Azure の MySQL インスタンスにどのように適用されるのかを確認します。

> [!NOTE]
> 5\.x から 8.0 にジャンプしたのは、主に、Oracle による MySQL の買収のためでした。 MySQL の履歴の詳細については、[MySQL の Wiki ページ](https://en.wikipedia.org/wiki/MySQL)を参照してください。

移行元の MySQL のバージョンを知ることは不可欠です。 システムを使用するアプリケーションで、そのバージョンに固有のデータベース オブジェクトと機能が使用されている可能性があります。 データベースを下位のバージョンに移行すると、互換性の問題が発生したり機能が失われたりする可能性があります。 また、新しいバージョンに移行する前に、データとアプリケーション インスタンスを十分にテストすることもお勧めします。導入された機能によって、アプリケーションが破損する可能性があります。

移行パスとバージョンに影響する可能性がある例を次に示します。

  - 5.6: ミリ秒を正しく格納するための TIMESTAMP 列と、フルテキスト検索

  - 5.7: ネイティブ JSON データ型のサポート

  - 8.0: NoSQL ドキュメント ストア、アトミック、クラッシュ セーフな DDL および JSON テーブル関数のサポート

    > [!NOTE]
    > 2021 年 2 月以降、MySQL 5.6 は一般的にサポートされなくなります。 MySQL ワークロードは、MySQL バージョン 5.7 以降に移行する必要があります。

MySQL サーバーのバージョンを確認するには:

```
SHOW VARIABLES LIKE "%version%";
```

### <a name="database-storage-engines"></a>データベース ストレージ エンジン

Azure Database for MySQL でサポートされているデータベース ストレージ エンジンは、[InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html) と [Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html) のみです。 [MyISAM](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html) などの他のストレージ エンジンは、サポートされているストレージ エンジンに移行する必要があります。 MyISAM と InnoDB の違いは、運用機能とパフォーマンス出力です。 上位レベルのテーブルとスキーマの構造については、通常、エンジン間で違いはありませんが、インデックスとテーブルの列の型は、ストレージとパフォーマンス上の理由で異なる可能性があります。 InnoDB は大きなデータ ファイル サイズを持つことで知られていますが、これらのストレージの詳細は Azure Database for MySQL サービスによって管理されます。

#### <a name="migrating-from-myisam-to-innodb"></a>MyISAM から InnoDB への移行

MyISAM のデータベースとテーブルは、InnoDB のテーブルに変換する必要があります。 変換後、アプリケーションで互換性とパフォーマンスをテストする必要があります。 テストするには、ほとんどの場合、DDL ステートメントを使用して、テーブルを再作成し、ターゲット ストレージ エンジンを変更する必要があります。 この変更は、Azure ターゲットでのスキーマ作成の間に行われるので、移行の間に実行する必要はほとんどありません。 詳細については、MySQL の Web サイトで[テーブルの変換に関する開発者向けドキュメント](https://dev.mysql.com/doc/refman/5.6/en/converting-tables-to-innodb.html)を参照してください。

役に立つテーブル情報を見つけるには、次のクエリを使用します。

```dotnetcli
    SELECT 
        tab.table_schema,
        tab.table_name,
        tab.engine as engine_type,
        tab.auto_increment,
        tab.table_rows,
        tab.create_time,
        tab.update_time,
        tco.constraint_type
    FROM information_schema.tables tab
    LEFT JOIN information_schema.table_constraints tco
        ON (tab.table_schema = tco.table_schema
            AND tab.table_name = tco.table_name
            )
    WHERE  
        tab.table_schema NOT IN ('mysql', 'information_schema', 'performance_
schema', 'sys')  
        AND tab.table_type = 'BASE TABLE';
```

> [!NOTE]
> 複数のデータベースで INFORMATION\_SCHEMA に対するクエリを実行すると、パフォーマンスに影響する可能性があります。 あまり使用されていないときに実行してください。

MyISAM から InnoDB にテーブルを変換するには、次のコマンドを実行します。

```
ALTER TABLE {table\_name} ENGINE=InnoDB;
```

> [!NOTE]
> この変換方法を使用すると、テーブルがロックされ、すべてのアプリケーションが操作の完了を待機する可能性があります。 テーブルのロックにより、データベースは短時間オフラインになります。

代わりに、構造は同じでもストレージ エンジンが異なるテーブルを作成できます。 作成した後、新しいテーブルに行をコピーします。

```
INSERT INTO {table\_name} SELECT * FROM {myisam\_table} ORDER BY {primary\_key\_columns}
```

> [!NOTE]
> この方法を成功させるには、元のテーブルを削除してから、新しいテーブルの名前を変更する必要があります。 このタスクにより、短いダウンタイム期間が発生します。

### <a name="database-data-and-objects"></a>データベースのデータとオブジェクト

データは、データベース移行の 1 つのコンポーネントです。 アプリケーションが確実に実行し続けるようにするには、データベース サポート オブジェクトを移行して検証する必要があります。

移行の前と後にインベントリを実行する必要がある項目の一覧を次に示します。

  - テーブル (スキーマ)

  - 主キー、外部キー

  - Indexes

  - 関数

  - 手順

  - トリガー

  - ビュー

### <a name="user-defined-functions"></a>ユーザー定義関数

MySQL では、外部コードを呼び出す関数を使用できます。 残念ながら、外部コードでユーザー定義関数 (UDF) を使用するデータ ワークロードは、Azure Database for MySQL に移行できません。 これは、MySQL 関数のバックにある必要な so または dll のコードを、Azure サーバーにアップロードできないためです。

インストールされている可能性のある UDF を調べるには、次のクエリを実行します。

```
SELECT * FROM mysql.func;
```

## <a name="source-systems"></a>移行元システム

移行の準備の量は、移行元システムとその場所によって異なる場合があります。 データベース オブジェクトに加えて、移行元システムから移行先システムにデータを取得する方法を検討します。 移行元と移行先の間にファイアウォールや他のネットワーク コンポーネントがある場合、データの移行が困難になる可能性があります。

さらに、インターネット経由でデータを移動すると、Azure への専用回線を使用するより遅くなる場合があります。 そのため、ギガバイト、テラバイト、ペタバイト単位のデータを大量に移動する場合は、移行元ネットワークと Azure ネットワークの間に [ExpressRoute](../../../expressroute/expressroute-introduction.md) 接続を設定することを検討します。

ExpressRoute が既に存在する場合は、他のアプリケーションによって接続が使用されている可能性があります。 既存のルートで移行を実行すると、ネットワークのスループットに負荷がかかり、ネットワークを使用する移行と他のアプリケーションの両方で、パフォーマンスに大きく影響する可能性があります。

最後に、ディスク領域を評価する必要があります。 大きなデータベースをエクスポートするときは、データのサイズを考慮します。 ツールが実行されていて、最終的なエクスポートの場所であるシステムに、エクスポート操作を実行するのに十分なディスク領域があることを確認します。

### <a name="cloud-providers"></a>クラウド プロバイダー

アマゾン ウェブ サービス (AWS) などのクラウド サービス プロバイダーからデータベースを移行するには、クラウドでホストされている MySQL インスタンスにアクセスするために、追加のネットワーク構成手順が必要になる場合があります。 Data Migration Services などの移行ツールでは、外部の IP 範囲からアクセスできる必要があり、そうでないとブロックされる可能性があります。

### <a name="on-premises"></a>オンプレミス

クラウド プロバイダーと同様に、MySQL のデータ ワークロードがファイアウォールまたは他のネットワーク セキュリティ層の内側にある場合は、オンプレミスのインスタンスと Azure Database for MySQL の間にパスを作成する必要があります。

## <a name="tools"></a>ツール

多くのツールと方法を使用して、MySQL のデータ ワークロードと環境を評価できます。 ツールごとに、提供されている評価と移行の機能のセットが異なります。 このガイドでは、MySQL のデータ ワークロードの評価に最もよく使用されるツールを確認します。

### <a name="azure-migrate"></a>Azure Migrate

[Azure Migrate](../../../migrate/migrate-services-overview.md) では MySQL データベース ワークロードの直接的な移行はサポートされていませんが、仮想マシンまたはハードウェア ベースのコンピューターのどちらでホストされている場合でも、どのようなユーザーやアプリケーションがデータを使用しているのか管理者がわからない場合に使用できます。 [依存関係の分析](../../../migrate/concepts-dependency-visualization.md)は、MySQL ワークロードがホストされているマシンに監視エージェントをインストールして実行することで実現できます。 エージェントにより、設定した期間 (1 か月など) についての情報が収集されます。 依存関係データを分析し、データベースに対して行われている不明な接続を見つけることができます。 接続データは、保留中の移行を通知する必要があるアプリケーションの所有者を特定するのに役立つことがあります。

アプリケーションとユーザー接続データの依存関係分析に加えて、Azure Migrate を使用すると、[Hyper-V、VMware、または物理サーバー](../../../migrate/migrate-appliance-architecture.md)を分析し、適切な移行先環境を提案するのに役立つデータベース ワークロードの使用パターンを提供することもできます。

### <a name="telgraf-for-linux"></a>Linux 用 Telgraf

Linux ワークロードでは、[Microsoft Monitoring Agent (MMA)](../../../azure-monitor/agents/agent-linux.md) を利用して、仮想マシンと物理コンピューター上のデータを収集できます。 さらに、[Telegraf エージェント](../../../azure-monitor/essentials/collect-custom-metrics-linux-telegraf.md)とその広範なプラグインを使用して、パフォーマンス メトリックを収集することを検討します。

### <a name="service-tiers"></a>サービス階層

移行ユーザーは、評価情報 (CPU、メモリ、ストレージなど) を入手した後、使用を開始する Azure Database for MySQL の[価格レベル](../../concepts-pricing-tiers.md)を決定します。

現在は、次の 3 つのレベルがあります。

  - **Basic**: 低負荷なコンピューティングと I/O パフォーマンスを必要とするワークロード。

  - **General Purpose**: バランスのとれたコンピューティングおよびメモリとスケーラブルな I/O スループットが必要なほとんどのビジネス ワークロード。

  - **メモリ最適化**: トランザクション処理時間の短縮とコンカレンシーの向上のためにインメモリ パフォーマンスが必要なハイパフォーマンス データベース ワークロード。

レベルの決定は、データ ワークロードの RTO と RPO の要件によって影響を受ける可能性があります。 データ ワークロードで 4 TB を超えるストレージが必要な場合は、追加の手順が必要です。 最大 16 TB のストレージを[サポートするリージョン](../../concepts-pricing-tiers.md#storage)を確認して選択します。

> [!NOTE]
> 自分のストレージ要件がサポートされていないリージョンについては、MySQL チーム (AskAzureDBforMySQL@service.microsoft.com) にお問い合わせください。

通常、意思決定を行うときは、ストレージと IOPS (1 秒あたりの入力/出力操作数) のニーズに注目します。 したがって、移行先システムには、常に、少なくとも移行元システムと同量のストレージが必要です。 さらに、IOPS の割り当ては 3/GB なので、IOPS のニーズを最終的なストレージ サイズに合わせることが重要です。

| 考慮すべき要素 | レベル |
|---------|------|
| **Basic** | 開発用コンピューター。1 TB 未満のストレージで高パフォーマンスを必要としない場合。 |
| **汎用** | Basic レベルで提供できるものより多くの IOPS が必要だが、ストレージは 16 TB 未満、メモリは 4 GB 未満の場合。 |
| **メモリ最適化** | 多くのメモリまたはキャッシュ、および高コンカレンシーの innodb_buffer_pool_instances、大きな BLOB サイズ、多くのレプリケーション コピーがあるシステムなどのバッファー関連サーバー構成を利用するデータ ワークロード。 |

### <a name="costs"></a>コスト

WWI MySQL データ ワークロード全体を評価した後、WWI は少なくとも 4 つの仮想コアと 20 GB のメモリ、および IOP 容量 450 IOPS を備えた 100 GB 以上のストレージ スペースが必要であると判断しました。 450 IOPS の要件と、[Azure Database for MySQL の IOPS 割り当て方法](../../concepts-pricing-tiers.md#storage)により、150 GB 以上のストレージを割り当てる必要があります。 また、バックアップ ストレージと 1 つの読み取りレプリカとして、プロビジョニングされたサーバー ストレージの少なくとも 100% が必要です。 5 GB を超える送信エグレスは想定されていません。

[Azure Database for MySQL 料金計算ツール](https://azure.microsoft.com/pricing/details/mysql/)を使用することで、WWI は Azure Database for MySQL インスタンスのコストを判断できました。 2020 年 9 月現在の WWI Conference データベースの総保有コスト (TCO) を次の表に示します。

| リソース | 説明 | Quantity | [コスト] |
|----------|-------------|----------|------|
| **コンピューティング (汎用)** | 4 仮想コア、20 GB                  | 1 @ $0.351/時                                              | $3074.76/年 |
| **Storage**                   | 5 GB                             | 12 x 150 @ $0.115                                          | $207/年     |
| **Backup**                    | プロビジョニングされたストレージの最大 100%| プロビジョニングされたサーバー ストレージの 100% までは追加コストなし     | $0.00/年    |
| **読み取りレプリカ**              | 1 秒のリージョン レプリカ          | コンピューティング + ストレージ                                          | $3281.76/年 |
| **Network**                   | < 5 GB/月エグレス               | Free                                                       |               |
| **合計**                     |                                  |                                                            | $6563.52/年 |

初期コストを確認した後、WWI の CIO は、3 年よりはるかに長い期間 Azure を使用することを確認しました。 追加の年間 \~$4K を節約するため、3 年間の[予約インスタンス](../../concept-reserved-pricing.md)を使用することを決定しました。

| リソース | 説明 | Quantity | [コスト] |
|----------|-------------|----------|------|
| **コンピューティング (汎用)** | 4 仮想コア                          | 1 @ $0.1375/時                                              | $1204.5/年 |
| **Storage**                   | 5 GB                              | 12 x 150 @ $0.115                                           | $207/年    |
| **Backup**                    | プロビジョニングされたストレージの最大 100% | プロビジョニングされたサーバー ストレージの 100% までは追加コストなし      | $0.00/年   |
| **Network**                   | < 5 GB/月エグレス                | Free                                                        |              |
| **読み取りレプリカ**              | 1 秒のリージョン レプリカ           | コンピューティング + ストレージ                                           | $1411.5/年 |
| **合計**                     |                                   |                                                             | $2823/年   |

上の表に示すように、総保有コスト (TCO) ではバックアップ、ネットワーク エグレス、読み取りレプリカを考慮する必要があります。 追加されるデータベースが多いほど、ストレージと生成されるネットワーク トラフィックだけが、考慮すべき追加のコストベースの要因になります。

> [!NOTE]
> 上記の見積もりには、[ExpressRoute](../../../expressroute/expressroute-introduction.md)、[Azure App Gateway](../../../application-gateway/overview.md)、[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)、アプリケーション層の [App Service](../../../app-service/overview.md) のコストは含まれていません。
>
> 上記の価格は、常に変わる可能性があり、リージョンによって異なります。

### <a name="application-implications"></a>アプリケーションへの影響

Azure Database for MySQL に移行する場合、Secure Sockets Layer (SSL) ベースの通信への変換は、アプリケーションにとって最も重要な変更の 1 つになる可能性があります。 Azure Database for MySQL では SSL は既定で有効になり、オンプレミスのアプリケーションとデータ ワークロードは、SSL を使用して MySQL に接続するように設定されていない可能性があります。 SSL を有効にして使用すると余分な処理オーバーヘッドが増加するので、監視する必要があります。

> [!NOTE]
> SSL は既定で有効になりますが、無効にするオプションがあります。

この強力な認証パスをサポートするようにアプリケーションを再構成するには、「[Azure Database for MySQL に安全に接続するためにアプリケーションで SSL 接続を構成する](../../howto-configure-ssl.md)」のアクティビティに従います。

最後に、アプリケーションの接続文字列でサーバー名を変更するか、新しい Azure Database for MySQL サーバーを指すように DNS を切り替えます。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI は、次の表に示すように、MySQL のデータ資産に関する情報を収集することで評価を開始しました。

| 名前 | source | DB エンジン | サイズ | IOPS | Version | 所有者 | ダウンタイム |
|------|--------|-----------|------|------|---------|-------|----------|
| **WwwDB**        | AWS (PaaS)  | InnoDB | 1 GB  | 150 | 5.7 | マーケティング部門 | 1 時間  |
| **BlogDB**       | AWS (PaaS)  | InnoDB | 1 GB  | 100 | 5.7 | マーケティング部門 | 4 時間 |
| **ConferenceDB** | オンプレミス | InnoDB | 5 GB  | 50  | 5.5 | 営業部門     | 4 時間 |
| **CustomerDB**   | オンプレミス | InnoDB | 10 GB | 75  | 5.5 | 営業部門     | 2 時間 |
| **SalesDB**      | オンプレミス | InnoDB | 20 GB | 75  | 5.5 | 営業部門     | 1 時間  |

各データベース所有者は、許容されるダウンタイム期間の決定を求められました。 許容されるデータベースのダウンタイムに基づいて、計画と、移行方法の選択が行われました。

最初のフェーズでは、WWI は ConferenceDB データベースのみに焦点を当てました。 チームには、進められるデータ ワークロードの移行に役立つ移行の経験が必要でした。 ConferenceDB データベースが選択されたのは、データベースの構造が単純で、許容されるダウンタイムが大きいためです。 そのデータベースを移行した後、チームは、セキュリティで保護された Azure ランディング ゾーンへのアプリケーションの移行に注目しました。

## <a name="assessment-checklist"></a>評価チェックリスト

  - 移行先システムでワークロードが正常に実行されることをテストします。

  - 移行のための適切なネットワーク コンポーネントがあることを確認します。

  - データ ワークロードのリソース要件を把握します。

  - 総コストを見積もります。

  - ダウンタイムの要件を把握します。

  - アプリケーションの変更に対応します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Planning](./04-planning.md)