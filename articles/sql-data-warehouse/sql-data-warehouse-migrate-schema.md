---
title: "SQL Data Warehouse へのスキーマの移行 | Microsoft Docs"
description: "ソリューション開発のための Azure SQL Data Warehouse へのスキーマの移行に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 538b60c9-a07f-49bf-9ea3-1082ed6699fb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 0630f43642a0be98c470032d32b74ca14ee144e5
ms.lasthandoff: 04/03/2017


---
# <a name="migrate-your-schema-to-sql-data-warehouse"></a>SQL Data Warehouse へのスキーマの移行
次の概要を確認して、SQL Server と SQL Data Warehouse の相違点を理解し、データベースの移行に役立ててください。

## <a name="table-migration"></a>テーブルの移行
テーブルを移行する場合、SQL Data Warehouse テーブルのテーブル機能を理解する必要があります。  [テーブルの概要][table overview]に関する記事は、その開始点として最適です。  この記事では、テーブルの統計、ディストリビューション、パーティション分割、およびインデックス作成など、テーブルを作成する場合の最も重要な考慮事項を紹介します。  また、[サポートされていないテーブルの機能][unsupported table features]と回避策についても取り上げます。

SQL Data Warehouse では、次の一般的なビジネス データ型がサポートされています。  サポートされているデータ型と[サポートされていないデータ型][unsupported data types]の一覧については、[データ型][data types]に関する記事を参照してください。  [データ型][data types]に関する記事には、[サポートされていないデータ型][unsupported data types]を識別するためのクエリも含まれています。  データ型を変換する場合は、必ず「[データ型のベスト プラクティス][data type best practices]」を参照してください。

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse にデータベース スキーマを正常に移行したら、次の記事のいずれかに進みます。

* [データの移行][Migrate your data]
* [コードの移行][Migrate your code]

SQL Data Warehouse のベスト プラクティスの詳細については、「[Azure SQL Data Warehouse のベスト プラクティス][best practices]」を参照してください。

<!--Image references-->

<!--Article references-->
[Migrate your code]: ./sql-data-warehouse-migrate-code.md
[Migrate your data]: ./sql-data-warehouse-migrate-data.md
[best practices]: ./sql-data-warehouse-best-practices.md
[table overview]: ./sql-data-warehouse-tables-overview.md
[unsupported table features]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[data types]: ./sql-data-warehouse-tables-data-types.md
[unsupported data types]: ./sql-data-warehouse-tables-data-types.md#unsupported-data-types
[data type best practices]: ./sql-data-warehouse-tables-data-types.md#data-type-best-practices

<!--MSDN references-->


<!--Other Web references-->

