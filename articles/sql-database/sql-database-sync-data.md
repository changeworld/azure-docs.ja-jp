---
title: "データの同期 (プレビュー) | Microsoft Docs"
description: "この概要では、Azure SQL データ同期 (プレビュー) について説明します。"
services: sql-database
documentationcenter: 
author: douglaslms
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: douglasl
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 075b5563688158289d51f2f0b5da4a3441ddd13a
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="sync-data-across-multiple-cloud-and-on-premises-databases-with-sql-data-sync"></a>複数のクラウドおよびオンプレミス データベースにわたるデータを SQL データ同期で同期します

SQL データ同期は、Azure SQL Database 上に構築されているサービスであり、選択したデータを複数の SQL データベースや SQL Server インスタンスの間で双方向に同期させることができます。

データ同期は、同期グループの概念に基づいています。 同期グループは、同期するデータベースのグループです。

同期グループには、次のようないくつかのプロパティがあります。

-   **同期スキーマ**は、どのデータが同期されるかについて説明します。

-   **同期の方向**は、双方向にするか、一方向だけのフローにすることができます。 つまり、同期の方向は、"*[Hub to Member]\(ハブからメンバーへ\)*" または "*[Member to Hub]\(メンバーからハブへ\)*"、あるいはその両方にすることができます。

-   **同期間隔**は、同期が発生する頻度です。

-   **競合解決ポリシー**は、グループ レベルのポリシーであり、*[ハブ側に合わせる]* と *[Member wins]\(メンバー側に合わせる\)* のどちらかにすることができます。

データ同期は、データを同期するために、ハブとスポークのトポロジを使用します。 グループ内のいずれかのデータベースを、ハブ データベースとして定義する必要があります。 他のデータベースは、メンバー データベースです。 同期は、ハブと個々のメンバー間でのみ発生します。
-   **ハブ データベース**は、Azure SQL データベースにする必要があります。
-   **メンバー データベース**は、SQL データベース、オンプレミス SQL Server データベース、または Azure 仮想マシン上の SQL Server インスタンスにすることができます。
-   **同期データベース**には、データ同期のメタデータとログが含まれています。 同期データベースは、ハブ データベースと同じリージョンにある Azure SQL データベースである必要があります。 同期データベースは、お客様が作成し、所有します。

> [!NOTE]
> オンプレミスのデータベースを使用している場合は、[ローカル エージェントを構成する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-sql-data-sync)必要があります。

![データベース間でのデータの同期](media/sql-database-sync-data/sync-data-overview.png)

## <a name="when-to-use-data-sync"></a>データ同期を使用する場合

データ同期は、いくつかの Azure SQL データベースや SQL Server データベースにわたるデータを最新の状態に保つ必要がある場合に便利です。 以下に、データ同期の主なユース ケースを示します。

-   **ハイブリッド データ同期:** データ同期を使用すると、オンプレミス データベースと Azure SQL データベースの間でデータの同期を維持し、SQL 内にデータ層を備えたハイブリッド アプリケーションを実現できます。 この機能は、クラウドへの移行を検討していて、一部のアプリケーションを Azure に配置しようとしているお客様にとって利点となる場合があります。

-   **分散アプリケーション:** 多くの場合、ワークロードが異なるのであればデータベースも分離しておくと便利です。 たとえば、大規模な運用データベースがあり、このデータに対するレポートまたは分析ワークロードを実行する必要がある場合は、この追加のワークロードのために 2 つ目のデータベースを用意すると便利です。 そうすることで、運用ワークロードのパフォーマンスへの影響を最低限にすることができます。 データ同期を使えば、この 2 つのデータベースの同期を維持することができます。

-   **グローバル分散アプリケーション:** 多くの企業は、複数のリージョン、さらには複数の国にわたって展開しています。 ネットワーク待ち時間を最小限にするには、データは最寄りのリージョンに配置するのが一番です。 データ同期では、世界中のリージョンのデータベースを簡単に同期させることができます。

以下のシナリオでは、データ同期をお勧めしません。

-   障害復旧

-   読み取りスケール

-   ETL (OLTP から OLAP へ)

-   オンプレミス SQL Server から Azure SQL Database への移行

## <a name="how-does-data-sync-work"></a>データ同期のしくみ 

-   **データ変更の追跡:** データ同期では、挿入、更新、および削除の 3 種類のトリガーを使用して変更を追跡します。 変更はユーザー データベースのサイド テーブルに記録されます。

-   **データの同期:** データ同期は、ハブとスポークのモデルで設計されています。 ハブは、各メンバーと個別に同期されます。 ハブからの変更がメンバーにダウンロードされ、次にメンバーからの変更がハブにアップロードされます。

-   **競合の解決:** データ同期には、競合を解決するために、*[ハブ側に合わせる]* と *[Member wins]\(メンバー側に合わせる\)* という 2 つのオプションが用意されています。
    -   *[ハブ側に合わせる]* を選択すると、ハブでの変更が常にメンバーでの変更を上書きします。
    -   *[Member wins]\(メンバー側に合わせる\)* を選択すると、メンバーでの変更がハブでの変更を上書きします。 複数のメンバーがある場合、最終的な値は、どのメンバーが最初に同期されるかによって異なります。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="performance-impact"></a>パフォーマンスへの影響
データ同期では、挿入、更新、および削除の 3 種類のトリガーを使用して変更を追跡します。 これにより、ユーザー データベース内にサイド テーブルが作成されます。 これらのアクティビティはデータベースのワークロードに影響を与えるため、サービス レベルを評価したうえで、必要があればアップグレードしてください。

### <a name="eventual-consistency"></a>最終的な一貫性
データ同期はトリガー ベースであるため、トランザクションの一貫性は保証されません。 Microsoft では、最終的にはすべての変更が行われることと、データ同期でデータ損失が発生しないことを保証しています。

### <a name="unsupported-data-types"></a>サポートされていないデータ型

-   FileStream

-   SQL/CLR UDT

-   XMLSchemaCollection (XML サポート)

-   Cursor、Timestamp、Hierarchyid

### <a name="requirements"></a>必要条件

-   各テーブルには主キーが必要です。

-   テーブルは、主キー以外の ID 列を設けることはできません。

-   データベース名に特殊文字を使用することはできません。

### <a name="limitations-on-service-and-database-dimensions"></a>サービスとデータベースの数量に関する制限

|                                                                 |                        |                             |
|-----------------------------------------------------------------|------------------------|-----------------------------|
| **数量**                                                      | **制限**              | **対処法**              |
| データベースが属することができる同期グループの最大数。       | 5                      |                             |
| 1 つの同期グループ内のエンドポイントの最大数              | 30                     | 複数の同期グループを作成する |
| 1 つの同期グループ内のオンプレミス エンドポイントの最大数。 | 5                      | 複数の同期グループを作成する |
| データベース、テーブル、スキーマ、および列名の文字数                       | 名前 1 件あたり 50 文字 |                             |
| 同期グループ内のテーブル数                                          | 500                    | 複数の同期グループを作成する |
| 同期グループ内のテーブルの列数                              | 1,000                   |                             |
| テーブルでのデータ行のサイズ                                        | 24 Mb                  |                             |
| 最小同期間隔                                           | 5 分              |                             |

## <a name="next-steps"></a>次のステップ

SQL Database および SQL データ同期の詳細については、以下を参照してください。

-   [SQL データ同期の概要](sql-database-get-started-sql-data-sync.md)

-   [完全な SQL データ同期テクニカル ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_full_documentation.pdf?raw=true)

-   [SQL データ同期 REST API ドキュメントのダウンロード](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

-   [SQL Database の概要](sql-database-technical-overview.md)

-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)



