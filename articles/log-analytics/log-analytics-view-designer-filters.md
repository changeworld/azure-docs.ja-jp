---
title: Azure Log Analytics ビューのフィルター | Microsoft Docs
description: Log Analytics ビューのフィルターでは、ビュー自体を変更せずに、特定のプロパティの値によってビュー内のデータをフィルター処理することができます。  この記事では、フィルターの使用方法およびカスタム ビューへの追加方法について説明します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "23655414"
---
# <a name="filters-in-log-analytics-views"></a>Log Analytics ビューのフィルター
[Log Analytics ビュー](log-analytics-view-designer.md)の**フィルター**では、ビュー自体を変更せずに、特定のプロパティの値によってビュー内のデータをフィルター処理することができます。  たとえば、ビューのユーザーが、特定のコンピューターまたは複数のコンピューターからのデータだけをフィルター表示したビューを利用できます。  1 つのビューに複数のフィルターを作成して、ユーザーが複数のプロパティでフィルター処理することが可能です。  この記事では、フィルターの使用方法およびカスタム ビューへの追加方法について説明します。

## <a name="using-a-filter"></a>フィルターを使用する
**[フィルター]** をクリックしてビューのフィルター ウィンドウを開きます。  ここでは、時間の範囲と、ビューで利用可能なすべてのフィルターの値を選択できます。  フィルターを選択すると、使用可能な値の一覧が表示されます。  1 つまたは複数の値を選択するか入力します。 ビューが自動的に更新され、指定された値でフィルター処理されます。 

フィルターの値が選択されていない場合、そのフィルターはビューに適用されません。  フィルターのすべての値を削除すると、そのフィルターは適用されなくなります。


![フィルターの例](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>フィルターを作成する

[ビューの編集](log-analytics-view-designer.md)時に、**[フィルター]** タブからフィルターを作成します。  フィルターはビューに対してグローバルで、ビュー内のすべての部分に適用されます。  

![フィルター設定](media/log-analytics-view-designer/filters-settings.png)

フィルターの設定を次の表に示します。

| Setting | [説明] |
|:---|:---|
| フィールド名 | フィルター処理に使用されるフィールドの名前です。  これは**値のクエリ**の集計フィールドと一致している必要があります。 |
| 値のクエリ | ユーザー用のフィルターのドロップダウン リストを設定するために実行するクエリ。  特定のフィールドに対して一意の値を指定するには、ここで [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) または [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) のいずれかを使用する必要があります。また、**フィールド名**に一致する必要があります。  [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) を使用すると、ユーザーに表示される値を並び替えることができます。 |
| タグ | フィルターをサポートするクエリで使用されるフィールドの名前です。ユーザーへの表示にも使われます。 |

### <a name="examples"></a>例

次の表に、一般的なフィルターの例をいくつか挙げます。  

| フィールド名 | 値のクエリ | タグ |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | [Computers (コンピューター)] |
| EventLevelName | Event &#124; distinct EventLevelName | 重大度 |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | 重大度 |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>ビューのクエリを変更する

フィルターが適用されるようにするには、ビューでクエリを変更し、選択された値でフィルター処理を行う必要があります。  ビューでクエリを変更しないと、ユーザーが選択する値は適用されません。

クエリでフィルター値を使用する構文は次のようになります。 

    where ${filter name}  

たとえば、イベントを返し、Computers というフィルターを使用するクエリがビューにある場合、以下を使用することができます。

    Event | where ${Computers} | summarize count() by EventLevelName

Severity という別のフィルターを追加した場合は、両方のフィルターを適用する次のクエリを使用できます。

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>次の手順
* カスタム ビューに追加できる[視覚エフェクト パーツ](log-analytics-view-designer-parts.md)の詳細についてはこちらをご覧ください。