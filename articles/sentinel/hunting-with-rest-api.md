---
title: REST API を使用して Microsoft Azure Sentinel でのハンティングとライブストリームのクエリを管理する | Microsoft Docs
description: この記事では、Microsoft Azure Sentinel のハンティング機能を利用して、Log Analytics の REST API を活用してハンティングとライブストリームのクエリを管理する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 4b479fbe2782cb7316cee565f51d7b6170c17579
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721026"
---
# <a name="manage-hunting-and-livestream-queries-in-microsoft-sentinel-using-rest-api"></a>REST API を使用して Microsoft Azure Sentinel でのハンティングとライブストリームのクエリを管理する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Azure Sentinel は、一部分が Azure Monitor Log Analytics 上に構築されており、Log Analytics の REST API を使用してハンティングとライブストリームのクエリを管理することができます。 このドキュメントでは、REST API を使用して、ハンティング クエリを作成および管理する方法について説明します。  この方法で作成されたクエリは、Microsoft Azure Sentinel UI に表示されます。

[保存された検索 API](/rest/api/loganalytics/savedsearches) の詳細については、最も信頼できる REST API リファレンスを参照してください。

## <a name="api-examples"></a>API の例

次の例では、これらのプレースホルダーを、次の表に示す置換を使用して置き換えます。

| プレースホルダー | 置換後の文字列 |
|-|-|
| **{subscriptionId}** | ハンティングまたはライブストリームのいずれかのクエリを適用するサブスクリプションの名前。 |
| **{resourceGroupName}** | ハンティングまたはライブストリームのいずれかのクエリを適用するリソース グループの名前。 |
| **{savedSearchId}** | 各ハンティング クエリの一意の ID (GUID)。 |
| **{WorkspaceName}** | クエリの対象となる Log Analytics ワークスペースの名前。 |
| **{DisplayName}** | クエリに対して選択した表示名。 |
| **{Description}** | ハンティングまたはライブストリームのクエリの説明。 |
| **{Tactics}** | クエリに適用される MITRE ATT&CK 関連する戦略。 |
| **{Query}** | クエリのクエリ式。 |
|  

### <a name="example-1"></a>例 1

この例では、特定の Microsoft Azure Sentinel ワークスペースに対してハンティング クエリを作成または更新する方法を示します。  ライブストリーム クエリの場合は、 *"Category":"Hunting Queries"* を *"Category":"Livestream Queries"* に **要求本文** で置き換えます。 

#### <a name="request-header"></a>要求ヘッダー

```http
PUT https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
"properties": {
    “Category”: “Hunting Queries”,
    "DisplayName": "HuntingRule02",
    "Query": "SecurityEvent | where EventID == \"4688\" | where CommandLine contains \"-noni -ep bypass $\"",
    “Tags”: [
        { 
        “Name”: “Description”,
        “Value”: “Test Hunting Query”
        },
        { 
        “Name”: “Tactics”,
        “Value”: “Execution, Discovery”
        }
        ]        
    }
}
```

### <a name="example-2"></a>例 2

この例では、特定の Microsoft Azure Sentinel ワークスペースに対してハンティングまたはライブストリームのクエリを削除する方法を示します。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId} _
       /resourcegroups/{resourceGroupName} _
       /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
       /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

### <a name="example-3"></a>例 3

この例では、特定のワークスペースに対してハンティングまたはライブストリームのクエリを取得する方法を示します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId} _
    /resourcegroups/{resourceGroupName} _
    /providers/Microsoft.OperationalInsights/workspaces/{workspaceName} _
    /savedSearches/{savedSearchId}?api-version=2020-03-01-preview
```

## <a name="next-steps"></a>次のステップ

この記事では、Log Analytics API を使用して Microsoft Azure Sentinel でハンティングとライブストリームのクエリを管理する方法について学習しました。 Microsoft Azure Sentinel の詳細については、次の記事を参照してください。

- [脅威を事前に検出する](hunting.md)
- [ノートブックを使用して自動化された検出キャンペーンを実行する](notebooks.md)
