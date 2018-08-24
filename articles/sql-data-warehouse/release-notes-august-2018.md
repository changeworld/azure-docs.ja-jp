---
title: Azure SQL Data Warehouse リリース ノート 2018 年 8 月 | Microsoft Docs
description: Azure SQL Data Warehouse のリリース ノート。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/06/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 1f3b17f3163c29f9b9e1e47e14ccdbc1e37e1010
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39633659"
---
# <a name="whats-new-in-azure-sql-data-warehouse-august-2018"></a>Azure SQL Data Warehouse の新機能 2018 年 8 月
Azure SQL Data Warehouse では、継続的に機能強化を図っています。 この記事では、2018 年 8 月に導入された新しい機能と変更点について説明します。

## <a name="automatic-intelligent-insights"></a>自動 Intelligent Insights
Microsoft は、データ ウェアハウスの自動化のクラウド環境を提供する[自動 Intelligent Insights](https://azure.microsoft.com/blog/automatic-intelligent-insights-to-optimize-performance-with-sql-data-warehouse/) を導入しました。 データ スキューや最適化されていないテーブルの統計情報を把握するためにデータ ウェアハウスを監視する必要がなくなります。 SQL Data Warehouse では、追加費用なしですべての Gen2 インスタンスに対して Intelligent Insights を利用できます。 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) と統合することで、アクティブなワークロードのパフォーマンスを向上させるためのベスト プラクティスの推奨事項を自動的に受信できます。 SQL Data Warehouse はワークロードを分析し、使用状況に基づいて推奨事項を適用します。 この分析は毎日行われ、使用状況レポートとワークロードの改善に関する推奨事項を監視できます。

Azure Advisor ポータルで推奨事項を表示できます。![Azure Advisor ポータルの Azure SQL Data Warehouse の推奨事項](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/4e205b6d-df04-48db-8eec-d591f2592cf4.png)

各カテゴリの詳細を表示して、特定のアラートの推奨事項を確認することができます。![Azure Advisor ポータルの Azure SQL Data Warehouse の推奨事項の詳細](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/3c42426e-6969-46e3-9025-c34c0755a302.png)

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