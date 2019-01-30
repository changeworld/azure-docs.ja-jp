---
title: Azure SQL Data Warehouse リリース ノート 2018 年 12 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/12/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 074276e4550b9b2e347e5cd30c597a1d09f6cb2f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440108"
---
# <a name="whats-new-in-azure-sql-data-warehouse-version---100101060"></a>Azure SQL Data Warehouse バージョン 10.0.10106.0 の新機能
Azure SQL Data Warehouse (SQL DW) は継続的に機能強化されています。 この記事では、SQL DW バージョン 10.0.10106.0 に導入された新しい機能と変更点について説明します。

## <a name="query-restartability---ctas-and-insertselect"></a>クエリの再開可能性 - CTAS と挿入/選択
まれに発生する状況 (つまり、断続的なネットワーク接続の問題やノード エラー) で、Azure SQL DW で実行されているクエリが失敗することがあります。 [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) や INSERT-SELECT 操作などの長時間実行されるステートメントでは、このような潜在的な問題が発生する可能性が高くなります。 Azure SQL DW のこのリリースでは、(既にお知らせしている SELECT ステートメントに加えて) CTAS と INSERT-SELECT ステートメントに対する再試行ロジックが実装され、システムでこれらの一時的な問題を透過的に処理して、クエリが失敗することを防ぐことができます。 再試行の回数と処理される一時的なエラーの一覧は、システムによって構成されます。

## <a name="return-order-by-optimization"></a>並べ替え後の戻りの最適化
このリリースでは、SELECT…ORDER BY クエリのパフォーマンスが大幅に向上しています。  これまでは、クエリ実行エンジンによって各コンピューティング ノード上で結果が並べ替えられた後、それらがコントロール ノードにストリーミングされ、そこで結果がマージされていました。 今回の機能強化では、すべてのコンピューティング ノードの結果が単一のコンピューティング ノードに送信され、そこで結果がマージされ、並べ替えられた結果がそのコンピューティング ノード経由でユーザーに返されます。  これにより、クエリの結果セットに多数の行が含まれている場合、パフォーマンスが大幅に向上します。

## <a name="data-movement-enhancements-for-partitionmove-and-broadcastmove"></a>PartitionMove と BroadcastMove でのデータ移動の機能強化
Azure SQL Data Warehouse Gen2 では、種類が ShuffleMove であるデータ移動手順で、[こちらのパフォーマンスの強化に関するブログ](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)で紹介されている瞬時データ移動手法が利用されています。  このリリースでは、種類が PartitionMove と BroadcastMove であるデータ移動でも、同じ瞬時データ移動手法が利用されるようになりました。  これらの種類のデータ移動手順を使用するユーザーのクエリでは、パフォーマンスが大幅に向上します。  これらのパフォーマンスの向上を利用するためにコードを変更する必要はありません。

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
