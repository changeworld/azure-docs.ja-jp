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
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 6a4ac2f26c01555ef54a4ee2248db7cd2818661e
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189423"
---
# <a name="filters-in-log-analytics-views"></a>Log Analytics ビューのフィルター
[Log Analytics ビュー](view-designer.md)の**フィルター**では、ビュー自体を変更せずに、特定のプロパティの値によってビュー内のデータをフィルター処理することができます。  たとえば、ビューのユーザーが、特定のコンピューターまたは複数のコンピューターからのデータだけをフィルター表示したビューを利用できます。  1 つのビューに複数のフィルターを作成して、ユーザーが複数のプロパティでフィルター処理することが可能です。  この記事では、フィルターの使用方法およびカスタム ビューへの追加方法について説明します。

## <a name="using-a-filter"></a>フィルターを使用する
ビューの上部にあるデータ時間範囲をクリックしてドロップダウンを開き、ビューのデータ時間範囲を変更します。

![フィルターの例](media/view-designer-filters/filters-example-time.png)

**[+]** をクリックして、ビューに定義されているカスタム フィルターを使用してフィルターを追加します。 ドロップダウンからフィルターの値を選択するか、値を入力します。 **[+]** をクリックしてさらにフィルターを追加します。 


![フィルターの例](media/view-designer-filters/filters-example-custom.png)

フィルターのすべての値を削除すると、そのフィルターは適用されなくなります。


## <a name="creating-a-filter"></a>フィルターを作成する

[ビューの編集](view-designer.md)時に、**[フィルター]** タブからフィルターを作成します。  フィルターはビューに対してグローバルで、ビュー内のすべての部分に適用されます。  

![フィルター設定](media/view-designer-filters/filters-settings.png)

フィルターの設定を次の表に示します。

| Setting | 説明 |
|:---|:---|
| フィールド名 | フィルター処理に使用されるフィールドの名前です。  これは**値のクエリ**の集計フィールドと一致している必要があります。 |
| 値のクエリ | ユーザー用のフィルターのドロップダウン リストを設定するために実行するクエリ。  特定のフィールドに対して一意の値を指定するには、ここで [summarize](/azure/kusto/query/summarizeoperator) または [distinct](/azure/kusto/query/distinctoperator) のいずれかを使用する必要があります。また、**フィールド名**に一致する必要があります。  [sort](/azure/kusto/query/sortoperator) を使用すると、ユーザーに表示される値を並び替えることができます。 |
| タグ | フィルターをサポートするクエリで使用されるフィールドの名前です。ユーザーへの表示にも使われます。 |

### <a name="examples"></a>例

次の表に、一般的なフィルターの例をいくつか挙げます。  

| フィールド名 | 値のクエリ | タグ |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | [Computers (コンピューター)] |
| EventLevelName | Event &#124; distinct EventLevelName | severity |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | severity |
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
* カスタム ビューに追加できる[視覚エフェクト パーツ](view-designer-parts.md)の詳細についてはこちらをご覧ください。
