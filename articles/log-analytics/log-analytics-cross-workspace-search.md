---
title: "Azure Log Analytics ワークスペース間でクエリを実行する | Microsoft Docs"
description: "この記事では、サブスクリプション内の複数のワークスペース間でクエリを実行する方法について、例を挙げて説明します。"
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
ms.date: 10/04/2017
ms.author: magoedte
ms.openlocfilehash: 6b6dc6f472a87178c006301f4f692aeff15e257e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="perform-queries-across-multiple-log-analytics-workspaces"></a>複数の Log Analytics ワークスペース間でクエリを実行する

以前の Azure Log Analytics では、現在のワークスペース内からしかデータを分析することができず、サブスクリプション内で定義された複数のワークスペース間でクエリを実行する機能が制限されていました。  

現在は、複数のワークスペース間でクエリを実行できるため、システム全体のデータを確認できるようになりました。  この種のクエリは、Azure Portal ではなく、[高度なポータル](log-analytics-log-search-portals.md#advanced-analytics-portal)でのみ実行できます。  

## <a name="querying-across-log-analytics-workspaces"></a>Log Analytics ワークスペース間でのクエリ実行
クエリで他のワークスペースを参照するには、*workspace* 識別子を使用します。  たとえば、次のクエリは現在のワークスペースおよび *contosoretail-it* という名前の他のワークスペースの双方の Update テーブルから、分類に必要な更新の集計されたカウントを返します。  


## <a name="identifying-resources"></a>リソースの識別
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

* Azure リソース ID - Azure で定義されている、ワークスペースの一意な ID。 リソース名があいまいな場合は、これを使用します。  ワークスペースごとに、*/subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName* という形式になります。  

    For example:
    ``` 
    `workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

## <a name="next-steps"></a>次のステップ

Log Analytics で使用できるすべてのクエリ構文オプションは、[Log Analytics のログ検索のリファレンス](https://docs.loganalytics.io/docs/Language-Reference)でご覧いただけます。    