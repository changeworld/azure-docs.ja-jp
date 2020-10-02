---
title: インスツルメント化されたクエリにラベルを使用する
description: ソリューション開発のために Synapse SQL プールでラベルを使用してクエリをインストルメント化するためのヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 91b6dac5fba4bb8dfd8cf4a3bb4e5952f8388bb5
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459149"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Synapse SQL プールでのラベルを使用したクエリのインストルメント化

この記事に含まれているのは、SQL プールでラベルを使用してクエリをインストルメント化するソリューションを開発するためのヒントです。

ソリューション開発のための Azure Synapse Analytics でのラベルを使用してクエリのインストルメント化に関するヒント。

## <a name="what-are-labels"></a>ラベルとは?

SQL プールでは、クエリ ラベルと呼ばれる概念がサポートされています。 難解な領域に入る前に、1 つの例を説明しましょう。

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 このタグが便利なのは、DMV を介してラベルをクエリできるためです。

ラベルのクエリを実行できることで、問題のあるクエリを検索したり、ELT 実行の進行状況を識別したりするためのメカニズムが提供されます。

良好な命名規則はたいへん役立ちます。 たとえば、ラベルを PROJECT、PROCEDURE、STATEMENT、または COMMENT で始めると、ソース管理内のすべてのコード間でクエリを一意に識別する上で役立ちます。

次のクエリでは、動的管理ビューを使用して、ラベルごとに検索が行われます。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> クエリをする際に、語句のラベルを角かっこまたは二重引用符で囲むことが必要です。 ラベルは予約語であるため、区切られていない場合はエラーが発生します。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
