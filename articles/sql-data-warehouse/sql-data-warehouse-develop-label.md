---
title: SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化 | Microsoft Docs
description: ソリューション開発のための Azure SQL Data Warehouse でのラベルを使用してクエリのインストルメント化に関するヒント。
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524246"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse で、ラベルを使用してクエリをインストルメント化
ソリューション開発のための Azure SQL Data Warehouse でのラベルを使用してクエリのインストルメント化に関するヒント。


## <a name="what-are-labels"></a>ラベルとは?
SQL Data Warehouse は、クエリ ラベルと呼ばれる概念をサポートします。 難解な領域に入る前に、1 つの例を説明しましょう。

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 このタグは、ラベルが DMV を介してクエリできるので特に便利です。 ラベルのクエリを実行できることで、問題のあるクエリを検索したり、ELT 実行の進行状況を識別したりするためのメカニズムが提供されます。

良好な命名規則はたいへん役立ちます。 たとえば、ラベルを PROJECT、PROCEDURE、STATEMENT、または COMMENT で始めると、ソース管理内のすべてのコード間でクエリを一意に識別する上で役立ちます。

次のクエリでは動的管理ビューを使用して、ラベルごとに検索が行われます。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> クエリをする際に、語句のラベルを角かっこまたは二重引用符で囲むことが必要です。 ラベルは予約語であるため、区切られていない場合はエラーが発生します。 
> 
> 

## <a name="next-steps"></a>次の手順
開発に関するその他のヒントについては、「[開発の概要](sql-data-warehouse-overview-develop.md)」をご覧ください。


