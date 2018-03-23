---
title: Azure SQL データ同期 (プレビュー) | Microsoft Docs
description: この概要では、Azure SQL データ同期 (プレビュー) について説明します。
services: sql-database
author: douglaslms
manager: craigg
ms.service: sql-database
ms.custom: data-sync
ms.topic: article
ms.date: 11/13/2017
ms.author: douglasl
ms.reviewer: douglasl
ms.openlocfilehash: 5abe3e5a56dfca263f109b86f473ac490da3eac7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync-preview"></a>SQL データ同期 (プレビュー) を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する

SQL データ同期は、Azure SQL Database 上に構築されているサービスであり、選択したデータを複数の SQL データベースや SQL Server インスタンスの間で双方向に同期させることができます。

データ同期は、同期グループの概念に基づいています。 同期グループは、同期するデータベースのグループです。

同期グループには、次のプロパティがあります。

-   **同期スキーマ**は、どのデータが同期されるかについて説明します。

-   **同期の方向**は、双方向にするか、一方向だけのフローにすることができます。 つまり、同期の方向は、"*[Hub to Member]\(ハブからメンバーへ\)*" または "*[Member to Hub]\(メンバーからハブへ\)*"、あるいはその両方にすることができます。

-   **同期間隔**は、同期が発生する頻度です。

-   **競合解決ポリシー**は、グループ レベルのポリシーであり、*[ハブ側に合わせる]* と *[Member wins]\(メンバー側に合わせる\)* のどちらかにすることができます。

データ同期は、データを同期するために、ハブとスポークのトポロジを使用します。 グループ内のいずれかのデータベースを、ハブ データベースとして定義します。 他のデータベースは、メンバー データベースです。 同期は、ハブと個々のメンバー間でのみ発生します。
-   **ハブ データベース**は、Azure SQL データベースにする必要があります。
-   **メンバー データベース**は、SQL データベース、オンプレミス SQL Server データベース、または Azure 仮想マシン上の SQL Server インスタンスにすることができます。
-   **同期データベース**には、データ同期のメタデータとログが含まれています。同期データベースは、ハブ データベースと同じリージョンにある Azure SQL データベースである必要があります。 同期データベースは、お客様が作成し、所有します。

> [!NOTE]
> オンプレミスのデータベースを使っている場合は、[ローカル エージェントを構成する](sql-database-get-started-sql-data-sync.md#add-on-prem)必要があります。

![データベース間でのデータの同期](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>データ同期を使用する場合

データ同期は、いくつかの Azure SQL データベースや SQL Server データベースにわたるデータを最新の状態に保つ必要がある場合に便利です。 以下に、データ同期の主なユース ケースを示します。

-   **ハイブリッド データ同期:** データ同期を使用すると、オンプレミス データベースと Azure SQL データベースの間でデータの同期を維持し、ハイブリッド アプリケーションを実現できます。 この機能は、クラウドへの移行を検討していて、一部のアプリケーションを Azure に配置しようとしているお客様にとって利点となる場合があります。

-   **分散アプリケーション:** 多くの場合、ワークロードが異なるのであればデータベースも分離しておくと便利です。 たとえば、大規模な運用データベースがあり、このデータに対するレポートまたは分析ワークロードを実行する必要がある場合は、この追加のワークロードのために 2 つ目のデータベースを用意すると便利です。 そうすることで、運用ワークロードのパフォーマンスへの影響を最低限にすることができます。 データ同期を使えば、この 2 つのデータベースの同期を維持することができます。

-   **グローバル分散アプリケーション:** 多くの企業は、複数のリージョン、さらには複数の国にわたって展開しています。 ネットワーク待ち時間を最小限にするには、データは最寄りのリージョンに配置するのが一番です。 データ同期では、世界中のリージョンのデータベースを簡単に同期させることができます。

データ同期は次のシナリオには適していません。

-   ディザスター リカバリー

-   読み取りスケール

-   ETL (OLTP から OLAP へ)

-   オンプレミス SQL Server から Azure SQL Database への移行

## <a name="how-does-data-sync-work"></a>データ同期のしくみ 

-   **データ変更の追跡:** データ同期では、挿入、更新、および削除の 3 種類のトリガーを使用して変更を追跡します。 変更はユーザー データベースのサイド テーブルに記録されます。

-   **データの同期:** データ同期は、ハブとスポークのモデルで設計されています。 ハブは、各メンバーと個別に同期されます。 ハブからの変更がメンバーにダウンロードされ、次にメンバーからの変更がハブにアップロードされます。

-   **競合の解決:** データ同期には、競合を解決するために、*[ハブ側に合わせる]* と *[Member wins]\(メンバー側に合わせる\)* という 2 つのオプションが用意されています。
    -   *[ハブ側に合わせる]* を選択すると、ハブでの変更が常にメンバーでの変更を上書きします。
    -   *[Member wins]\(メンバー側に合わせる\)* を選択すると、メンバーでの変更がハブでの変更を上書きします。 複数のメンバーがある場合、最終的な値は、どのメンバーが最初に同期されるかによって異なります。

## <a name="sync-req-lim"></a> 要件と制限

### <a name="general-considerations"></a>一般的な考慮事項

#### <a name="eventual-consistency"></a>最終的な一貫性
データ同期はトリガー ベースであるため、トランザクションの一貫性は保証されません。 Microsoft では、最終的にはすべての変更が行われることと、データ同期でデータ損失が発生しないことを保証しています。

#### <a name="performance-impact"></a>パフォーマンスへの影響
データ同期では、挿入、更新、および削除の 3 種類のトリガーを使用して変更を追跡します。 これにより、ユーザー データベース内に変更追跡のためのサイド テーブルが作成されます。 これらの変更追跡アクティビティは、データベースのワークロードに影響します。 サービス層を評価し、必要な場合はアップグレードします。

### <a name="general-requirements"></a>一般的な要件

-   各テーブルには主キーが必要です。 どの行の主キーも値を変更しないでください。 主キーの値を変更する必要がある場合は、行を削除し、新しい主キー値で作成し直してください。 

-   スナップショット分離を有効にする必要があります。 詳しくは、「[SQL Server でのスナップショット分離](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server)」をご覧ください。

### <a name="general-limitations"></a>一般的な制限事項

-   テーブルに、主キー以外の ID 列を設けることはできません。

-   オブジェクト (データベース、テーブル、および列) の名前には、印刷可能な文字のピリオド (.)、左角かっこ ([)、または右角かっこ (]) を使用できません。

-   Azure Active Directory 認証はサポートされていません。

#### <a name="unsupported-data-types"></a>サポートされていないデータ型

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (XML サポート)

-   Cursor、Timestamp、Hierarchyid

#### <a name="limitations-on-service-and-database-dimensions"></a>サービスとデータベースの数量に関する制限

| **数量**                                                      | **制限**              | **対処法**              |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| データベースが属することができる同期グループの最大数。       | 5                      |                             |
| 1 つの同期グループ内のエンドポイントの最大数              | 30                     | 複数の同期グループを作成する |
| 1 つの同期グループ内のオンプレミス エンドポイントの最大数。 | 5                      | 複数の同期グループを作成する |
| データベース、テーブル、スキーマ、および列名の文字数                       | 名前 1 件あたり 50 文字 |                             |
| 同期グループ内のテーブル数                                          | 500                    | 複数の同期グループを作成する |
| 同期グループ内のテーブルの列数                              | 1,000                   |                             |
| テーブルでのデータ行のサイズ                                        | 24 Mb                  |                             |
| 最小同期間隔                                           | 5 分              |                             |
|||

## <a name="faq-about-sql-data-sync"></a>SQL データ同期に関する FAQ

### <a name="how-much-does-the-sql-data-sync-preview-service-cost"></a>SQL データ同期 (プレビュー) サービスのコストはどれくらいですか?

プレビュー中は、SQL データ同期 (プレビュー) サービス自体に料金はかかりません。  ただし、SQL Database インスタンスへの、またはインスタンスからのデータ移動には、データ転送の料金がかかります。 詳しくは、「[SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/)」をご覧ください。

### <a name="what-regions-support-data-sync"></a>データ同期はどのリージョンでサポートされていますか?

SQL データ同期 (プレビュー) は、すべてのパブリック クラウド リージョンで利用できます。

### <a name="is-a-sql-database-account-required"></a>SQL Database アカウントは必要ですか? 

はい。 ハブ データベースをホストするには、SQL Database アカウントが必要です。

### <a name="can-i-use-data-sync-to-sync-between-sql-server-on-premises-databases-only"></a>データ同期を使用してオンプレミスの SQL Server データのみの間で同期できますか? 
直接無効にすることはできません。 しかし、Azure でハブ データベースを作成し、オンプレミスデータベースを同期グループに追加することで、オンプレミスの SQL Server データベース間で間接的に同期できます。
   
### <a name="can-i-use-data-sync-to-seed-data-from-my-production-database-to-an-empty-database-and-then-keep-them-synchronized"></a>データ同期を使用して、実稼働データベースからデータを空のデータベースにデータを挿入し、それらの同期を維持することはできますか? 
はい。 元のデータベースからスクリプトを作成することで、新しいデータベース内にスキーマを手動で作成します。 スキーマを作成した後で、同期グループにテーブルを追加し、データをコピーして同期を維持します。

### <a name="should-i-use-sql-data-sync-to-back-up-and-restore-my-databases"></a>データベースをバックアップおよび復元するには、SQL データ同期を使う必要がありますか?

SQL データ同期 (プレビュー) を使ってデータのバックアップを作成することはお勧めできません。 SQL データ同期 (プレビュー) の同期はバージョン管理されていないため、バックアップして特定の時点に復元することはできません。 さらに、SQL データ同期 (プレビュー) では、ストアド プロシージャなどの他の SQL オブジェクトはバックアップされず、復元操作に相当する処理は迅速に行われません。

お勧めするバックアップ方法の 1 つについては、「[Azure SQL Database のコピー](sql-database-copy.md)」をご覧ください。

### <a name="is-collation-supported-in-sql-data-sync"></a>SQL データ同期では照合順序はサポートされていますか?

はい。 SQL データ同期は、次のシナリオで照合順序をサポートします。

-   選んだ同期スキーマ テーブルがハブまたはメンバー データベースにまだない場合、同期グループを展開すると、サービスは空の展開先データベースで選ばれている照合順序の設定を使って、対応するテーブルと列を自動的に作成します。

-   同期対象のテーブルがハブとメンバー データベースの両方に既に存在する場合は、SQL データ同期で同期グループを正常に展開するには、ハブとメンバー データベースで主キー列の照合順序が同じである必要があります。 主キー列以外の列に対して照合順序の制限はありません。

### <a name="is-federation-supported-in-sql-data-sync"></a>SQL データ同期ではフェデレーションはサポートされていますか?

フェデレーション ルート データベースは、SQL データ同期 (プレビュー) サービスで制限なしに使うことができます。 現在のバージョンの SQL データ同期 (プレビュー) には、フェデレーション データベースのエンドポイントを追加できません。

## <a name="next-steps"></a>次の手順

SQL データ同期の詳細については、以下を参照してください。

-   [Azure SQL データ同期のセットアップ](sql-database-get-started-sql-data-sync.md)
-   [Azure SQL データ同期のベスト プラクティス](sql-database-best-practices-data-sync.md)
-   [OMS Log Analytics を使用した Azure SQL データ同期の監視](sql-database-sync-monitor-oms.md)
-   [Troubleshoot issues with Azure SQL Data Sync (Azure SQL データ同期に関する問題のトラブルシューティング)](sql-database-troubleshoot-data-sync.md)

-   SQL データ同期を構成する方法を示す完全な PowerShell の例
    -   [PowerShell を使用した複数の Azure SQL データベース間の同期](scripts/sql-database-sync-data-between-sql-databases.md)
    -   [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](scripts/sql-database-sync-data-between-azure-onprem.md)

-   [SQL データ同期 REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

SQL Database の詳細については、以下を参照してください。

-   [SQL Database の概要](sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
