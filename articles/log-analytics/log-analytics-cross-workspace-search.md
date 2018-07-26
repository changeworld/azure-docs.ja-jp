---
title: Azure Log Analytics でのリソース間検索 | Microsoft Docs
description: この記事では、サブスクリプション内の複数のワークスペースや App Insights アプリのリソースに対してクエリを実行する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: e7ca3bcb3c3322c0eba12d7f9eb2ee2bc7b7600c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049849"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Log Analytics でクロス リソースのログ検索を実行する  

以前の Azure Log Analytics では、現在のワークスペース内からしかデータを分析することができず、サブスクリプション内で定義された複数のワークスペース間でクエリを実行する機能が制限されていました。  また、Application Insights を使用した Web ベースのアプリケーションから収集されたテレメトリ項目を、Application Insights または Visual Studio から直接検索することしかできませんでした。  またこれにより、運用データとアプリケーション データを一緒にネイティブ分析することが難しくなっていました。   

現在は、複数の Log Analytics ワークスペース間だけでなく、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからのデータのクエリを実行できるようになりました。 これにより、システム全体のデータを確認できます。  これらのタイプのクエリは、Azure Portal ではなく、[高度なポータル](log-analytics-log-search-portals.md#advanced-analytics-portal)でのみ実行できます。 1 回のクエリに含めることができるリソース (Log Analytics ワークスペースおよび Application Insights アプリ) の数は 100 個に制限されています。 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Log Analytics ワークスペース間と Application Insights からのクエリ
クエリ内の別のワークスペースを参照するには、[*ワークスペース*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace())識別子を使用します。Application Insights のアプリの場合は、[*アプリ*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())識別子を使用します。  

### <a name="identifying-workspace-resources"></a>ワークスペースのリソースの識別
次の例は、Log Analytics ワークスペース間でのクエリを実行し、*contosoretail-it* という名前のワークスペースの Update テーブルから、ログの集計されたカウントを返します。 

ワークスペースの識別は、次の方法のいずれかで実行できます。

* リソース名 - 人間が判読できるワークスペースの名前。"*コンポーネント名*" と呼ばれることもあります。 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >ワークスペースを名前で識別する場合、その名前は、アクセス可能なすべてのサブスクリプション間で一意であることが前提とされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。

* 修飾名 - ワークスペースの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ワークスペース ID - ワークスペース ID は、グローバル一意識別子 (GUID) として表される、各ワークスペースに割り当てられた一意で不変の識別子です。

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure リソース ID - Azure で定義されている、ワークスペースの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。  ワークスペースごとに、*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName* という形式になります。  

    例: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Update | count
    ```

### <a name="identifying-an-application"></a>アプリケーションの識別
次の例では、Application Insights の *fabrikamapp* という名前のアプリに対して行われた要求の集計数を返します。 

Application Insights でのアプリケーションの識別は、*app(Identifier)* 式で実行できます。  *Identifier* 引数は、次のいずれかを使用してアプリを指定します。

* リソース名 - 人間が判読できるアプリの名前。"*コンポーネント名*" と呼ばれることもあります。  

    `app("fabrikamapp")`

* 修飾名 - アプリの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ID - アプリケーションのアプリ GUID。

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure リソース ID - Azure で定義されている、アプリの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。 形式は */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName* です。  

    例: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>複数のリソース間でのクエリの実行
任意のリソース インスタンスの複数のリソースにクエリを実行できます。ワークスペースとアプリを組み合わせることもできます。
    
2 つのワークスペースにわたるクエリの例:    
    ```
    union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
    | where TimeGenerated >= ago(1h)
    | where UpdateState == "Needed"
    | summarize dcount(Computer) by Classification
    ```

## <a name="next-steps"></a>次の手順

Log Analytics で使用できるすべてのクエリ構文オプションは、[Log Analytics のログ検索のリファレンス](https://docs.loganalytics.io/docs/Language-Reference)でご覧いただけます。    
