---
title: "SQL Data Warehouse へのソリューションの移行 | Microsoft Docs"
description: "Azure SQL Data Warehouse プラットフォームにソリューションを組み込むための移行ガイダンス。"
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: 198365eb-7451-4222-b99c-d1d9ef687f1b
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: joeyong;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e5937f8472492cd1dd77c82ed518a665718623a1
ms.contentlocale: ja-jp
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-solution-to-sql-data-warehouse"></a>SQL Data Warehouse へのソリューションの移行
SQL Data Warehouse は、ニーズに合わせて柔軟に拡張できる分散データベース システムです。 パフォーマンスと拡張性の両方を維持するために、SQL Server 内のすべての機能が SQL Data Warehouse に実装されるわけではありません。 次の移行に関するトピックでは、SQL Data Warehouse にソリューションを移行する際のいくつかの重要な要素について説明します。 拡張性を考えてデータ ウェアハウスを設計すると、さまざまな設計パターンが導入されるため、従来の手法が常に最適であるとは限りません。 そのため、既存のソリューションを採用すると、SQL Data Warehouse によって提供される分散プラットフォームを最大限に活用できる場合もあります。

また、SQL Data Warehouse は Microsoft Azure に基づいたプラットフォームであることに留意することも重要です。 このため、移行の一環としてクラウドへのデータ転送が含まれる場合もよくあります。 データ転送はそれ自体が問題になるものであり、特に容量が増加する場合には慎重に検討する必要があります。 データ転送とデータの読み込みは別々のトピックです。

## <a name="migration-guidance"></a>移行ガイダンス
移行を開始する前に、次の記事を読んで製品の相違点と基本的な概念を必ず理解しておいてください。

* [スキーマの移行][Migrate your schema]
* [データの移行][Migrate your data]
* [コードの移行][Migrate your code]

## <a name="next-steps"></a>次のステップ
また、CAT (Customer Advisory Team) には、ブログを通じて発行する SQL Data Warehouse の優れたガイダンスもあります。  移行に関するその他のガイダンスについては、「[Migrating data to Azure SQL Data Warehouse in practice][Migrating data to Azure SQL Data Warehouse in practice]」(Azure SQL Data Warehouse へのデータの実践移行) の記事を参照してください。

<!--Image references-->

<!--Article references-->
[Migrate your schema]: sql-data-warehouse-migrate-schema.md
[Migrate your data]: sql-data-warehouse-migrate-data.md
[Migrate your code]: sql-data-warehouse-migrate-code.md


<!--MSDN references-->


<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/

