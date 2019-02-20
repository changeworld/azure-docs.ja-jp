---
title: Azure SQL Data Warehouse リリース ノート | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.openlocfilehash: 51932ebf7d5bdc6830098ce7136a3eee7255ffe1
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56245509"
---
# <a name="azure-sql-data-warehouse-release-notes"></a>Azure SQL Data Warehouse リリース ノート
この記事は、[Azure 仮想マシン上の SQL Server](sql-data-warehouse-overview-what-is.md) の最新リリースで導入された新機能と機能強化をまとめたものです。 この記事では、今回のリリースとは直接関連しないものの、同じタイム フレームで公開された注目すべきコンテンツの更新についても一覧表示しています。 他の Azure サービスの機能強化については、「[Azure の更新情報](https://azure.microsoft.com/updates)」をご覧ください。

## <a name="sql-data-warehouse-version-100101060-january"></a>SQL Data Warehouse バージョン 10.0.10106.0 (1 月)

### <a name="service-improvements"></a>サービスの機能強化

| サービスの機能強化 | 詳細 |
| --- | --- |
| **クエリの再開可能性 - CTAS と挿入/選択** | まれに発生する状況 (つまり、断続的なネットワーク接続の問題やノード エラー) で、Azure SQL DW で実行されているクエリが失敗することがあります。 [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-ctas) や INSERT-SELECT 操作などの長時間実行されるステートメントでは、このような潜在的な問題が発生する可能性が高くなります。 Azure SQL DW のこのリリースでは、(既にお知らせしている SELECT ステートメントに加えて) CTAS と INSERT-SELECT ステートメントに対する再試行ロジックが実装されています。 この変更により、システムでこれらの一時的な問題を透過的に処理して、クエリが失敗することを防ぐことができます。 再試行の回数と処理される一時的なエラーの一覧は、システムによって構成されます。|
|**並べ替え後の戻りの最適化**|このリリースでは、SELECT…ORDER BY クエリのパフォーマンスが大幅に向上しています。   現在、すべてのコンピューティング ノードの結果が単一のコンピューティング ノードに送信され、そこで結果がマージされ、並べ替えられた結果がそのコンピューティング ノード経由でユーザーに返されます。  単一のコンピューティング ノードを経由してマージすることにより、クエリの結果セットに多数の行が含まれている場合、パフォーマンスが大幅に向上します。 これまでは、クエリ実行エンジンによって各コンピューティング ノード上で結果が並べ替えられた後、それらがコントロール ノードにストリーミングされ、そこで結果がマージされていました。|
|**PartitionMove と BroadcastMove でのデータ移動の機能強化**|Azure SQL Data Warehouse Gen2 では、種類が ShuffleMove であるデータ移動手順で、[パフォーマンスの強化に関するブログ](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)で紹介されている瞬時データ移動手法が使用されています。 このリリースでは、種類が PartitionMove と BroadcastMove であるデータ移動でも、同じ瞬時データ移動手法が利用されるようになりました。 これらの種類のデータ移動手順を使用するユーザー クエリの実行では、パフォーマンスが大幅に向上します。 これらのパフォーマンスの向上を利用するためにコードを変更する必要はありません。|

### <a name="documentation-improvements"></a>ドキュメントの改善

| ドキュメントの改善 | 詳細 |
| --- | --- |
|なし | |
| | |

## <a name="next-steps"></a>次の手順
[SQL Data Warehouse の作成](./create-data-warehouse-portal.md)

## <a name="more-information"></a>詳細情報
- [ブログ - Azure SQL Data Warehouse](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Customer Advisory Team のブログ](https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/)
- [顧客の成功事例](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Stack Overflow フォーラム](http://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
- [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Azure 用語集](../azure-glossary-cloud-terminology.md)