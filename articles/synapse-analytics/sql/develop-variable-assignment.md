---
title: 変数に Synapse SQL を代入する
description: この記事では、T-SQL 変数に Synapse SQL を代入する際のヒントについて説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 91e251c8274ad85fcb0b3a65a3bdbcc660ceba00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85101003"
---
# <a name="assigning-variables-with-synapse-sql"></a>変数への Synapse SQL の代入

この記事では、T-SQL 変数に Synapse SQL を代入する際のヒントについて説明します。

## <a name="setting-variables-with-declare"></a>DECLARE を使用した変数の設定

Synapse SQL の変数は、`DECLARE` ステートメントまたは `SET` ステートメントを使用して設定されます。 DECLARE を使用した変数の初期化は、Synapse SQL で変数値を設定する最も柔軟な方法の 1 つです。

```sql
DECLARE @v  int = 0
;
```

DECLARE を使用して、一度に複数の変数を設定することもできます。 SELECT または UPDATE を使用して次を行うことはできません。

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

同じ DECLARE ステートメントで変数を初期化して使用することはできません。 たとえば、次の例は、 *\@p1* が同じ DECLARE ステートメントで初期化して使用されているため許可されません。 次の例は、エラーが発生します。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>SET を使用した値の設定

SET は、1 つの変数を設定する際によく使用されるメソッドです。

次のステートメントはすべて、SET を使用して変数を設定する有効な方法です。

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET を使用して設定できる変数は、一度に 1 つに限られます。 ただし、複合演算子は許容されます。

## <a name="limitations"></a>制限事項

変数代入で UPDATE は使用できません。

## <a name="next-steps"></a>次のステップ

開発に関するその他のヒントについては、[Synapse SQL の開発の概要](develop-overview.md)に関する記事をご覧ください。
