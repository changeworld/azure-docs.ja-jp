---
title: "Azure Log Analytics ワークスペース間でクエリを実行する | Microsoft Docs"
description: "この記事では、サブスクリプション内の複数のワークスペースや特定の App Insights アプリ間でクエリを実行する方法について説明します。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>複数の Log Analytics ワークスペース間でクエリを実行する方法

以前の Azure Log Analytics では、現在のワークスペース内からしかデータを分析することができず、サブスクリプション内で定義された複数のワークスペース間でクエリを実行する機能が制限されていました。  

現在は、複数の Log Analytics ワークスペース間だけでなく、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定の Application Insights アプリからのデータのクエリを実行できるようになりました。 これにより、システム全体のデータを確認できます。  この種のクエリは、Azure Portal ではなく、[高度なポータル](log-analytics-log-search-portals.md#advanced-analytics-portal)でのみ実行できます。  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Log Analytics ワークスペース間と Application Insights からのクエリ
クエリ内の別のワークスペースを参照するには、[*ワークスペース*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace())識別子を使用します。Application Insights のアプリの場合は、[*アプリ*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app())識別子を使用します。  

たとえば、最初のクエリは現在のワークスペースおよび *contosoretail-it* という名前の他のワークスペースの双方の Update テーブルから、分類に必要な更新の集計されたカウントを返します。  2 番目のクエリの例では、Application Insights の *fabrikamapp* という名前のアプリに対して行われた要求の集計数を返します。 

### <a name="identifying-workspace-resources"></a>ワークスペースのリソースの識別
ワークスペースの識別は、次の方法のいずれかで実行できます。

* リソース名 - 人間が判読できるワークスペースの名前。"*コンポーネント名*" と呼ばれることもあります。 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >その名前でワークスペースを識別すると、アクセス可能なすべてのサブスクリプション全体で一意であるとみなされます。 指定した名前を持つ複数のアプリケーションが存在する場合は、あいまいさのためにクエリが失敗します。 この場合、その他の識別子のいずれかを使用する必要があります。

* 修飾名 - ワークスペースの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ワークスペース ID - ワークスペース ID は、グローバル一意識別子 (GUID) として表される、各ワークスペースに割り当てられた一意で不変の識別子です。

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* Azure リソース ID - Azure で定義されている、ワークスペースの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。  ワークスペースごとに、*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName* という形式になります。  

    例: 
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>アプリケーションの識別

Application Insights でのアプリケーションの識別は、*app(Identifier)* 式で実行できます。  *Identifier* 引数は、次のいずれかを使用してアプリを指定します。

* リソース名 - 人間が判読できるアプリの名前。"*コンポーネント名*" と呼ばれることもあります。  

    `app("fabrikamapp")`

* 修飾名 - アプリの "完全な名前"。*subscriptionName/resourceGroup/componentName* の形式で、サブスクリプション名、リソース グループ、およびコンポーネント名から構成されます。 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Azure サブスクリプション名が一意ではない場合、この識別子はあいまいである可能性があります。 
    >

* ID - アプリケーションのアプリ GUID。

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* Azure リソース ID - Azure で定義されている、アプリの一意な ID。 リソース名があいまいな場合は、リソース ID を使用します。 形式は */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName* です。  

    例: 
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>次の手順

Log Analytics で使用できるすべてのクエリ構文オプションは、[Log Analytics のログ検索のリファレンス](https://docs.loganalytics.io/docs/Language-Reference)でご覧いただけます。    