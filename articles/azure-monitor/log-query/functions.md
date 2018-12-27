---
title: Azure Log Analytics の関数| Microsoft Docs
description: この記事では、Log Analytics. 内で関数を使用して、あるクエリを別のクエリから呼び出す方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885329"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics 内での関数の使用

> [!NOTE]
> このレッスンを完了する前に、[Analytics ポータルの概要](get-started-portal.md)および[クエリの概要](get-started-queries.md)に関するチュートリアルを完了する必要があります。

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


別のクエリで使用するために Log Analytics クエリを関数として保存できます。 これにより、複雑なクエリを複数のパーツに分割することで単純化でき、共通のコードを複数のクエリで再利用できます。

## <a name="create-a-function"></a>関数を作成する

Azure portal で **［保存］** をクリックした後、次の表の情報を指定して関数を作成します。

| Setting | 説明 |
|:---|:---|
| Name           | **クエリ エクスプローラー**に表示されるクエリの名前。 |
| 名前を付けて保存        | 関数 |
| 関数のエイリアス | この関数を他のクエリの中で使用するための短い名前。 スペースを含めることはできず、一意である必要があります。 |
| Category       | 保存したクエリと関数を**クエリ エクスプローラー**内で整理するためのカテゴリ。 |

> [!NOTE]
> Log Analytics 内の関数に別の関数を含めることはできません。

> [!NOTE]
> Log Analytics クエリでは関数の保存が可能ですが、現時点において、Application Insights クエリでは保存できません。



## <a name="use-a-function"></a>関数を使用する
関数は、そのエイリアスを別のクエリに含めることによって使用します。 それは他のテーブルと同じように使用できます。

## <a name="example"></a>例
次のサンプル クエリでは、最終日に報告された、更新されていないセキュリティ更新プログラムが返されます。 このクエリを、_security_updates_last_day_ というエイリアスを持つ関数として保存します。 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

別のクエリを作成し、_security_updates_last_day_ 関数を参照して、SQL に関連する更新が必要なセキュリティ更新プログラムを検索します。

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>次の手順
Log Analytics クエリ言語の使用については、他のレッスンをご覧ください。

- [文字列操作](string-operations.md)
- [日付と時刻の操作](datetime-operations.md)
- [集計関数](aggregations.md)
- [高度な集計](advanced-aggregations.md)
- [JSON とデータ構造](json-data-structures.md)
- [結合](joins.md)
- [グラフ](charts.md)
