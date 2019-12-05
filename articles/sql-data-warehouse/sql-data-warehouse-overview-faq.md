---
title: Azure Synapse Analytics (旧称 SQL DW) のよく寄せられる質問
description: この記事では、Azure Synapse Analytics (旧称 SQL DW) に関して顧客と開発者からよく寄せられる質問を示します
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9e64d184ef0fd120d1bc64ce274ee882b7938df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708649"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (旧称 SQL DW) のよく寄せられる質問

## <a name="general"></a>全般

Q. Azure Synapse とは

A. Azure Synapse は、データ ウェアハウスとビッグ データ分析がまとめられた無制限の分析サービスです。 サーバーレスのオンデマンド リソースまたはプロビジョニング済みのリソースを使用しながら、大規模に、各自の条件で自由にデータを照会することができます。 Azure Synapse では、これら 2 つの環境を 1 つにした統合エクスペリエンスを使用して、データの取り込み、準備、管理、提供を行い、BI と機械学習の差し迫ったニーズに対応できます。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. Azure SQL Data Warehouse はどうなりましたか

A. Azure Synapse は、Azure SQL Data Warehouse (SQL DW) の進化版です。 業界をリードする同じデータ ウェアハウスに、まったく新しいレベルのパフォーマンスと機能を搭載しました。 Azure Synapse を使用すると、現在の運用環境で既存のデータ ウェアハウスのワークロードを引き続き実行でき、プレビュー段階にある新機能の利点を自動的に利用できるようになります。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. SQL Analytics とは

A. SQL Analytics は、Azure Synapse で一般提供されているエンタープライズ データ ウェアハウス機能を表します。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. Azure Synapse の使用を開始する方法

A. [Azure の無料アカウント](https://azure.microsoft.com/free/sql-data-warehouse/)で使用を開始するか、[詳細について営業担当者にお問い合わせください](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。 

Q. データ セキュリティに対して Azure Synapse にはどのような効果がありますか

A. Azure Synapse は、TDE や監査など、データを保護するための複数のソリューションを提供します。 詳細については、 [セキュリティ]に関するページを参照してください。

Q. Azure Synapse が準拠している法律またはビジネスに関する標準は、どこで確認できますか。

A. SOC や ISO などの製品によって提供されるさまざまなコンプライアンスについては、[Microsoft のコンプライアンスに関するページ]をご覧ください。 まずコンプライアンスのタイトルを選び、ページ右側の [Microsoft in-scope cloud services]\(Microsoft 対象クラウド サービス\) セクションで Azure を展開して、Azure Synapse が準拠しているサービスを確認します。

Q. Power BI は接続できますか

A. はい。 Power BI は Azure Synapse での直接クエリをサポートしますが、多数のユーザーまたはリアルタイム データを意図したものではありません。 Power BI のパフォーマンスをさらに最適化したい場合は、Azure Analysis Services または Analysis Services IaaS 上で Power BI を使用することを検討してください。

Q. SQL Analytics の容量制限はどれくらいですか

A. [容量制限に関するページ]をご覧ください。 

Q. スケール/一時停止/再開にそれほど時間がかかるのはなぜでしょうか。

A. さまざまな要因がコンピューティングの管理操作に必要な時間に影響します。 長時間を要する操作に共通するのは、トランザクションのロールバックです。 スケール操作または一時停止操作が開始されると、すべての着信セッションがブロックされ、クエリが廃棄されます。 システムを安定した状態に維持するには、操作を開始する前にトランザクションをロールバックする必要があります。 トランザクションのログ数が多く、サイズが大きいほど、システムを安定した状態に復元するために操作を停止する時間が長くなります。

## <a name="user-support"></a>ユーザー サポート

Q. 機能の要求があります。どこにお願いすればいいですか。

A. 機能の要求がある場合は、[UserVoice] ページでお送りください。

Q. 何かを行う方法を知るにはどうすればよいですか。

A. Azure Synapse での開発については、[Stack Overflow] に関するページで質問できます。 

Q. サポート チケットはどのように送信すればよいですか。

A. [サポート チケット]は Azure Portal で提出できます。

## <a name="sql-languagefeature-support"></a>SQL 言語/機能のサポート 

Q. サポートされているデータ型は何ですか

A. [データの種類]に関する記事を参照してください。

Q. どのようなテーブル機能がサポートされていますか。

A. 多くの機能がサポートされていますが、サポートされない機能もいくつかあります。詳しくは、「[サポートされていないテーブルの機能]」をご覧ください。

## <a name="tooling-and-administration"></a>ツールと管理

Q. REST API は SQL Analytics でサポートされていますか

A. はい。 SQL Database で使うことができるほとんどの REST 機能は、SQL Analytics でも使用できます。 API の情報については、REST のドキュメントまたは [MSDN] をご覧ください。


## <a name="loading"></a>読み込み

Q. どのようなクライアント ドライバーをサポートしていますか。

A. DW 用のドライバー サポートについては、[接続文字列に関するページ]をご覧ください。

Q:PolyBase ではどのようなファイル形式がサポートされていますか

A:Orc、RC、Parquet、およびフラット区切りテキストです。

Q:PolyBase を使用して接続できるデータ ソースは何ですか 

A:[Azure Data Lake Store] と [Azure Storage Blobs] です。

Q:Azure Storage Blob または ADLS に接続するとき、計算プッシュダウンは可能ですか 

A:いいえ、PolyBase は記憶域コンポーネントのみを操作します。 

Q:HDI に接続できますか。

A:HDI は、HDFS レイヤーとして ADLS または WASB を使うことができます。 HDFS レイヤーとしていずれかがある場合は、SQL DW にそのデータを読み込むことができます。 ただし、HDI インスタンスに対するプッシュダウン計算を生成することはできません。 

## <a name="next-steps"></a>次の手順
Azure Synapse の詳細については、「[概要]」ページをご覧ください。


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[接続文字列に関するページ]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[サポート チケット]: ./sql-data-warehouse-get-started-create-support-ticket.md
[セキュリティ]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft のコンプライアンスに関するページ]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[容量制限に関するページ]: ./sql-data-warehouse-service-capacity-limits.md
[データの種類]: ./sql-data-warehouse-tables-data-types.md
[サポートされていないテーブルの機能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects feature request]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[概要]: ./sql-data-warehouse-overview-faq.md
