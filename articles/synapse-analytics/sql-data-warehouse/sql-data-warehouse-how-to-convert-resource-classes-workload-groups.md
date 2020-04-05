---
title: リソース クラスからワークロード グループへの変換
description: Azure SQL Data Warehouse のリソース クラスに似たワークロード グループを作成する方法について説明します。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d20a811270b89772ab75bc298544a549caa01041
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350448"
---
# <a name="convert-resource-classes-to-workload-groups"></a>リソース クラスからワークロード グループへの変換
ワークロード グループは、システム リソースを分離および格納するためのメカニズムを提供します。  さらに、ワークロード グループでは、その中で実行されている要求の実行規則を設定できます。  クエリタイム アウト実行規則では、ユーザーの介入なしにランナウェイ クエリを取り消すことができます。  この記事では、既存のリソース クラスを取得し、同様の構成でワークロード グループを作成する方法について説明します。  また、オプションのクエリ タイムアウト規則が追加されます。

## <a name="understanding-the-existing-resource-class-configuration"></a>既存のリソース クラスの構成について
ワークロード グループには、要求ごとに割り当てられるシステム リソース全体の割合を指定する `REQUEST_MIN_RESOURCE_GRANT_PERCENT` という名前のパラメーターが必要です。  リソース割り当ては、コンカレンシー スロットを割り当てることによって[リソース クラス](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes)に対して行われます。  `REQUEST_MIN_RESOURCE_GRANT_PERCENT` に指定する値を決定するには、sys.dm_workload_management_workload_groups_stats <link tbd> DMV を使用します。  たとえば、次のクエリは、staticrc40 のようなワークロード グループを作成するために `REQUEST_MIN_RESOURCE_GRANT_PERCENT` パラメーターに使用できる値を返します。   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> ワークロード グループは、システム リソース全体の割合に基づいて動作します。  

ワークロード グループは、システム リソース全体の割合に基づいて動作するため、スケール アップおよびスケール ダウンすると、システム リソース全体を基準とした静的リソース クラスに割り当てられるリソースの割合が変化します。  たとえば、DW1000c の staticrc40 は、システム リソース全体の 9.6% を割り当てます。  DW2000c では、19.2% が割り当てられます。  このモデルは、コンカレンシーのためにスケールアップする場合と、要求ごとに割り当てるリソースを増やす場合の比較に似ています。   

## <a name="create-workload-group"></a>ワークロード グループを作成する
`REQUEST_MIN_RESOURCE_GRANT_PERCENT` が既知であれば、CREATE WORKLOAD GROUP <link> 構文を使用して、ワークロード グループを作成できます。  必要に応じて、0 より大きい `MIN_PERCENTAGE_RESOURCE` を指定して、ワークロード グループのリソースを分離することもできます。  また必要に応じて、ワークロード グループが使用できるリソースの量を制限するために、`CAP_PERCENTAGE_RESOURCE` を 100 未満に指定することもできます。  

次の例では、`MIN_PERCENTAGE_RESOURCE` を設定して、システム リソースの 9.6% を `wgDataLoads` の専用にし、1 つのクエリが常に実行できるようにすることを保証します。  さらに、`CAP_PERCENTAGE_RESOURCE` は 38.4% に設定され、このワークロード グループは 4 個の同時要求に制限されます。  `QUERY_EXECUTION_TIMEOUT_SEC` パラメーターを 3600 に設定すると、1 時間以上実行されるすべてのクエリが自動的に取り消されます。

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>分類子を作成する
以前は、リソース クラスへのクエリのマッピングは、[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class) を使用して行われていました。  同じ機能を実現し、ワークロード グループに要求をマップするには、[CREATE WORKLOAD CLASSIFIER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) 構文を使用します。  sp_addrolemember を使用した場合、ログインに基づいてリソースを要求にマッピングすることのみが可能でした。  分類子はログインの他に、
    - ラベル●らべる○
    - session
    - time のような追加オプションを提供します。次の例では、`AdfLogin` に設定された[オプション ラベル](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label)も持つ `factloads` ログインからのクエリを、上記で作成されたワークロード グループ `wgDataLoads` に割り当てます。

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>サンプル クエリでのテスト
次に示すのは、ワークロード グループと分類子が正しく構成されていることを確認するためのサンプル クエリと DMV クエリです。

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>次のステップ

ワークロードの分離 - リンク未定: ワークロード グループを作成する方法 -リンク未定