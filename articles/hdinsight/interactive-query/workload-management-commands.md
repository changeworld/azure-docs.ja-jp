---
title: Hive LLAP ワークロード管理コマンド
titleSuffix: Azure HDInsight
description: Hive LLAP ワークロード管理コマンド
ms.service: hdinsight
ms.topic: reference
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: dc05ae5ec7cad35d5cda549e654caf3d44d91a03
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483135"
---
# <a name="hive-llap-workload-management-commands"></a>Hive LLAP ワークロード管理コマンド

ワークロード管理機能は、次の Hive コマンドを使用して制御および管理できます。 これらのコマンドは、既存の ALTER、CREATE、DROP、および SHOW ステートメントに似ています。

## <a name="alter-mapping"></a>Alter Mapping 
リソース プールへのクエリのルーティングを変更します。 
#### <a name="syntax"></a>構文 
```hql
ALTER { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ]
```
#### <a name="example"></a>例 
```hql
ALTER USER MAPPING 'hive' IN demo_plan TO etl WITH ORDER 1;
```

## <a name="alter-pool"></a>Alter Pool 
クエリ プールのプロパティの変更、トリガーの追加、トリガーの削除を行います。 
#### <a name="syntax"></a>構文 
```hql
ALTER POOL plan_name.pool_path [ SET {property=value, ... } | UNSET { property, ... } ];
ALTER POOL plan_name.pool_path [ ADD | DROP ] TRIGGER name;
```
#### <a name="example"></a>例 
```hql
ALTER POOL demo_plan.default ADD TRIGGER defaultToETL;
```

## <a name="alter-resource-plan"></a>Alter Resource Plan 
プランの有効化、無効化、アクティブ化、検証、または変更を行います。 
#### <a name="syntax"></a>構文 
```hql
ALTER RESOURCE PLAN name [ VALIDATE | DISABLE | ENABLE | ACTIVATE | RENAME TO another_name | SET {property=value, ... } | UNSET {property, ... } ];
```
#### <a name="example"></a>例 
```hql
ALTER RESOURCE PLAN demo_plan SET DEFAULT POOL=etl, QUERY_PARALLELISM=3;
```

## <a name="alter-trigger"></a>Alter Trigger 
トリガーをリソース プールに追加するか、リソース プールからトリガーを削除します。 
#### <a name="syntax"></a>構文 
```hql
ALTER TRIGGER plan_name.name { ADD TO | DROP FROM } { POOL path | UNMANAGED };
```
#### <a name="example"></a>例 
```hql
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;
```

## <a name="create-mapping"></a>Create Mapping 
リソース プールにクエリをルーティングします。 
#### <a name="syntax"></a>構文 
```hql
CREATE { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name { TO pool_path | UNMANAGED } [ WITH ORDER num ];
```
#### <a name="example"></a>例 
```hql
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
```

## <a name="create-pool"></a>Create Pool 
リソース プランのクエリ プールを作成して追加します。 
#### <a name="syntax"></a>構文 
```hql
CREATE POOL plan_name.path WITH ALLOC_FRACTION = decimal, QUERY_PARALLELISM = num, [ SCHEDULING_POLICY = scheduling_value ];
```
#### <a name="example"></a>例 
```hql
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
```

## <a name="create-resource-plan"></a>Create Resource Plan 
リソース プランを作成します。 
#### <a name="syntax"></a>構文 
```hql
CREATE RESOURCE PLAN plan_name [ WITH QUERY PARALLELISM=number | LIKE name];
```
#### <a name="example"></a>例 
```hql
CREATE RESOURCE PLAN demo_plan;
```

## <a name="create-trigger"></a>Create Trigger 
トリガーを作成し、リソース プランに追加します。 
#### <a name="syntax"></a>構文 
```hql
CREATE TRIGGER plan_name.name WHEN condition DO action;
```
#### <a name="example"></a>例 
```hql
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
```

## <a name="disable-workload-management"></a>Disable Workload Management 
アクティブなリソース プランを非アクティブ化します。 
#### <a name="syntax"></a>構文 
```hql
DISABLE WORKLOAD MANAGEMENT;
```
#### <a name="example"></a>例 
```hql
DISABLE WORKLOAD MANAGEMENT
```

## <a name="drop-mapping"></a>Drop Mapping 
リソース プランからマッピングを削除します。 
#### <a name="syntax"></a>構文 
```hql
DROP { USER | GROUP | APPLICATION } MAPPING 'entity_name' IN plan_name;
```
#### <a name="example"></a>例 
```hql
DROP USER MAPPING 'hive' IN demo_plan;
```

## <a name="drop-pool"></a>Drop Pool 
リソース プランからクエリ プールを削除します。 
#### <a name="syntax"></a>構文 
```hql
DROP POOL plan_name.pool_path;
```
#### <a name="example"></a>例 
```hql
CREATE POOL demo_plan.etl;
```

## <a name="drop-resource-plan"></a>Drop Resource Plan 
リソース プランを削除します。 
#### <a name="syntax"></a>構文 
```hql
DROP RESOURCE PLAN plan_name;
```
#### <a name="example"></a>例 
```hql
DROP RESOURCE PLAN demo_plan;
```

## <a name="drop-trigger"></a>Drop Trigger 
リソース プランからトリガーを削除します。 
#### <a name="syntax"></a>構文 
```hql
DROP TRIGGER plan_name.trigger_name;
```
#### <a name="example"></a>例 
```hql
DROP TRIGGER demo_plan.defaultToETL;
```

## <a name="replace-resource-plan-with"></a>Replace Resource Plan With 
あるリソース プランの内容を別のリソース プランの内容に置き換えます。 
#### <a name="syntax"></a>構文 
```hql
REPLACE RESOURCE PLAN name1 WITH name2; 
REPLACE ACTIVE RESOURCE PLAN name1 WITH name2;
```
#### <a name="example"></a>例 
```hql
REPLACE RESOURCE PLAN rp_plan1 WITH rp_plan2;
```

## <a name="show-resource-plan"></a>Show Resource Plan 
プランの内容を一覧表示します。 
#### <a name="syntax"></a>構文 
```hql
SHOW RESOURCE PLAN plan_name;
```
#### <a name="example"></a>例 
```hql
SHOW RESOURCE PLAN demo_plan;
```

## <a name="show-resource-plans"></a>Show Resource Plans 
すべてのリソース プランを一覧表示します。 
#### <a name="syntax"></a>構文 
```hql
SHOW RESOURCE PLANS;
```
#### <a name="example"></a>例 
```hql
SHOW RESOURCE PLANS;
```
