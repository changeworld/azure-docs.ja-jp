---
title: Azure SQL Data Warehouse リリース ノート 2018 年 10 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: e67edf382a49839d890d2c1dec50c44bbb19705a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52966825"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 10 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 10 月に導入された新しい機能と変更点について説明します。

## <a name="devops-for-data-warehousing"></a>データウェアハウス用の DevOps
SQL Data Warehouse (SQL DW) に要求の多かった機能が Visual Studio での SQL Server Data Tool (SSDT) のサポートでプレビューになりました。 開発者のチームは、1 つのバージョン管理されたコードベースで共同作業し、世界中の任意のインスタンスに変更をすばやくデプロイできます。 参加に関心がおありですか。 この機能は、現在プレビューで使用できます。 [SQL Data Warehouse Visual Studio SQL Server Data Tools (SSDT) - プレビュー登録フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u)にアクセスして登録することができます。 需要が多いため、お客様にとって最適なエクスペリエンスを実現するためにプレビューへの受け入れを管理しています。 サインアップ後、7 営業日以内に状態を確認することを目指しています。

## <a name="row-level-security-generally-available"></a>行レベルセキュリティの一般公開
Azure SQL Data Warehouse (SQL DW) では、機密データをセキュリティで保護する強力な機能を追加する行レベル セキュリティ (RLS) をサポートするようになりました。 RLS を導入することにより、テーブル内でアクセスできる行とアクセスできる人など、行に対するアクセスを制御するためのセキュリティ ポリシーを実装できます。 RLS を使用すると、ご使用のデータ ウェアハウスを再設計しないでも、このようなきめ細やかなアクセス制御が可能になります。 アクセス制限のロジックは、別のアプリケーションのデータから離れた場所ではなく、データベース層そのものに配置されているため、全般的なセキュリティ モデルが RLS によって簡素化されます。 また、RLS により、アクセス制御を管理するために行をフィルター処理するビューを導入する必要がなくなります。 すべてのお客様について、このエンタープライズ グレードのセキュリティ機能の追加コストはありません。

## <a name="advanced-advisors"></a>高度なアドバイザー
データ ウェアハウスの推奨事項とメトリックの追加により、Azure SQL Data Warehouse (SQL DW) の高度なチューニングが簡単になりました。 任意に活用できる Azure Advisor からの以下の高度なパフォーマンス レコメンデーションが追加されています。
1.  アダプティブ キャッシュ – キャッシュ使用率の最適化のために拡大縮小するタイミングに関するアドバイスを得られます。
2.  テーブルの分散 – データ移動を減らしてワークロードのパフォーマンスを向上させるために、テーブルをレプリケートするタイミングを判別します。 
3.  Tempdb – Tempdb 競合を減らすために、リソース クラスを拡大縮小したり構成したりするタイミングを把握します。

概要ブレードでのほぼリアルタイムのメトリックに関する拡張されたカスタマイズ可能な監視グラフなど、[Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) によるデータ ウェアハウス メトリックのより深い統合が提供されています。 使用状況の監視や、データ ウェアハウス レコメンデーションの検証や適用の際、Azure Monitor メトリックにアクセスするためにデータ ウェアハウスの概要ブレードを移動しなくてもよくなりました。 さらに、パフォーマンス レコメンデーションを補完する、新しいメトリック (Tempdb やアダプティブ キャッシュ使用率など) を使用できるようになりました。

## <a name="advanced-tuning-with-integrated-advisors"></a>統合されたアドバイザーによる高度なチューニング
データ ウェアハウスの推奨事項とメトリックの追加、および Azure Advisor と Azure Monitor で統合されたエクスペリエンスを提供するポータル概要ブレードのデザイン変更により、Azure SQL Data Warehouse (SQL DW) の高度なチューニングが簡単になりました。

## <a name="accelerated-database-recovery-adr"></a>高速データベース復旧 (ADR)
Azure SQL Data Warehouse の高速データベース復旧 (ADR) がパブリック プレビューになりました。 ADR は、現在の復旧プロセスを一から再設計することで、特に実行時間の長いトランザクションがある場合などにデータベースの可用性を大幅に向上させる、新しい SQL Server エンジンです。 ADR の主な利点は、高速かつ一貫性のあるデータベースの復旧と、瞬時のトランザクション ロールバックです。

## <a name="azure-monitor-diagnostics-logs"></a>Azure Monitor 診断ログ
Azure Monitor 診断ログと直接統合されることにより、SQL Data Warehouse (SQL DW) では、分析ワークロードに対する拡張された分析情報を利用できるようになりました。 この新しい機能により、開発者は長期間にわたってワークロード ビヘイビアーを分析し、クエリの最適化や容量の管理に関して十分に情報を得たうえで決定を下すことができます。 データ ウェアハウスのワークロードに対する追加の分析情報を提供する [Azure Monitor 診断ログ](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs)により、外部ログ プロセスを導入しました。 ボタンを 1 回クリックすることで、[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries) を使用して履歴クエリのパフォーマンスをトラブルシューティングするための診断ログを構成できます。 Azure Monitor 診断ログでは、監査目的でログをストレージ アカウントに保存することによって、カスタマイズ可能な保有期間をサポートしています。また、ほぼリアルタイムでのテレメトリの分析情報を入手するためにログをイベント ハブにストリームしたり、[ログ クエリ]()で Log Analytics を使用することによってログを分析したりすることが可能です。 診断ログは、データ ウェアハウスのテレメトリ ビューで構成されています。これは、よく使用される SQL Data Warehouse でのパフォーマンス トラブルシューティングの DMV に相当します。 この最初のリリースでは、以下のシステム動的管理ビューが有効になっています。

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>列ストアのメモリ管理
圧縮された列ストア行グループの数が増えると、その行グループ内の列セグメント メタデータを管理するために必要なメモリも増えます。  その結果、一部の列ストア動的管理ビュー (DMV) に対して実行されるクエリのパフォーマンスとクエリが低下する可能性があります。  このリリースでは、このようなケースの内部メタデータのサイズを最適化する改善が加えられ、こうしたクエリのエクスペリエンスとパフォーマンスが向上しました。 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Azure Data Lake Storage Gen2 の統合 (GA)
Azure SQL Data Warehouse (SQL DW) は、Azure Data Lake Storage Gen2 とネイティブに統合するようになりました。 お客様は、外部テーブルを使用して ABFS から SQL DW にデータを読み込むことができます。 この機能を使用することにより、Data Lake Storage Gen2 内のデータ レイクと統合できます。 

## <a name="bug-fixes"></a>バグの修正

| タイトル | 説明 |
|:---|:---|
| **DW2000 以降のデータ ウェアハウスでの小さいリソース クラスにおける Parquet に対する CETAS のエラー** | この修正により、Create External Table As 内での Parquet コード パスに対する null 参照が正しく識別されます。 |
|**一部の CTAS 操作で ID 列の値が失われることがある** | 別のテーブルへの CTAS を行うと、ID 列の値が維持されない場合があります。 ブログでの報告: [https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/)。 |
| **クエリがまだ実行している間にセッションが終了されたときに発生する場合がある内部エラー** | この修正では、クエリがまだ実行している間にセッションが終了されると、InvalidOperationException がトリガーされます。 |
| **(2018 年 11 月に配置) 顧客が Polybase を使用して複数の小さなファイルを ADLS (Gen1) からの読み込もうとしたときに、パフォーマンスは十分に最適ではありませんでした。** | AAD セキュリティ トークンの検証中のシステムのパフォーマンスがボトルネックでした。 セキュリティ トークンのキャッシュを有効にすると、パフォーマンスの問題は軽減されました。 |


## <a name="next-steps"></a>次の手順
SQL Data Warehouse の概要について学習したので、次は[SQL Data Warehouse を簡単に作成する][create a SQL Data Warehouse]方法について学習してください。 Azure に慣れていない場合に新しい用語を調べるには、[Azure 用語集][Azure glossary]が役立ちます。 または、次の SQL Data Warehouse リソースも確認できます。  

* [顧客の成功事例]
* [ブログ]
* [機能に関する要求]
* [ビデオ]
* [Customer Advisory Team のブログ]
* [Stack Overflow フォーラム]
* [Twitter]


[ブログ]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team のブログ]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[顧客の成功事例]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[機能に関する要求]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow フォーラム]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[ビデオ]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
