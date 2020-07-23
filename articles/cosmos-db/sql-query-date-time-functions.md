---
title: Azure Cosmos DB クエリ言語の日付と時刻関数
description: DateTime および timestamp の操作を実行する Azure Cosmos DB の日付と時刻の SQL システム関数について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246939"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>日付と時刻関数 (Azure Cosmos DB)

日付と時刻関数を使用すると、Azure Cosmos DB で DateTime と timestamp の操作を実行できます。

## <a name="functions-to-obtain-the-date-and-time"></a>日付と時刻を取得する関数

次のスカラー関数では、UTC での現在の日付と時刻を、ISO 8601 形式に準拠した文字列または数値のタイムスタンプ (値はミリ秒単位の Unix エポック) という 2 つの形式で取得できます。

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>DateTime 値を操作する関数

次の関数を使用すると、DateTime 値を簡単に操作できます。

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregates.md)
