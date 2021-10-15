---
title: 専用 SQL プール内の誤って分類されたワークロードのトラブルシューティング
description: Azure Synapse Analytics の専用 SQL プールで、ワークロードが意図しないワークロード グループに誤って分類されているシナリオを特定し、トラブルシューティングを行います。
author: SudhirRaparla
ms.author: nvraparl
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 10/01/2021
ms.custom: template-how-to
ms.reviewer: wiassaf
ms.openlocfilehash: 6cf1564ccc62d6429da56bc8116b124b44b58653
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728401"
---
# <a name="troubleshooting-a-misclassified-workload-in-azure-synapse-analytics"></a>Azure Synapse Analytics での誤って分類されたワークロードのトラブルシューティング

この記事では、専用 SQL プール内のワークロードについて、誤って分類されたワークロードのトラブルシューティングの方法と、その分類の背後にある理由を特定する方法について説明します。

Azure Synapse Analytics には、[ワークロードを適切なワークロード グループに分類](sql-data-warehouse-workload-classification.md)したり、[重要度を割り当て](sql-data-warehouse-workload-importance.md)たり、SLA を満たすために[リソースを分離](sql-data-warehouse-workload-isolation.md)したりといった、ワークロード管理機能があります。 

ただし、シナリオによっては、これらの機能を組み合わせると、ユーザーの意図を反映していないワークロードの分類につながる可能性があります。 この記事では、一般的なシナリオとそのトラブルシューティングの方法を示します。 まず、誤って分類されたワークロードのシナリオをトラブルシューティングするための基本情報を照会する必要があります。

> [!NOTE]
> この記事は、Azure Synapse Analytics のサーバーレス SQL プールには当てはまりません。

## <a name="basic-troubleshooting-information"></a>基本的なトラブルシューティング情報

誤って分類されたワークロードのシナリオのトラブルシューティングを行うには、次の情報が必要です。

- [ワークロード グループ](#workload-groups)のリスト
- すべての[ワークロード分類子](#workload-classifiers)および関連するワークロード グループのリスト
- [ユーザーとマップされたワークロード グループ](#users-and-mapped-resource-classes)のリスト (システムおよびユーザー定義) 
- 要求の[ワークロード グループと分類子の詳細](#workload-group-and-classifier-details-of-a-request)のリスト


### <a name="workload-groups"></a>ワークロード グループ

#### <a name="azure-portal"></a>Azure portal

Azure portal ですべてのワークロード グループ (システム ワークロード グループを含む) と関連する詳細のリストを取得するには、次のようにします。

1. 目的の専用 SQL プールが作成されている Azure Synapse ワークスペースに移動します。
1. 左側のウィンドウに、ワークスペースの下に作成されたすべてのプールの種類が表示されます。 **[分析プール]** セクションで **[SQL プール]** を選択します。
1. 目的の専用 SQL プールを選択します。
1. 左側のペインの **[設定]** で、 **[ワークロードの管理]** を選択します。
1. **[ワークロード グループ]** セクションに、すべてのワークロードのリストが表示されます。 既定では、 **[ユーザー定義のワークロード グループ]** のみのリストが表示されます。 ユーザー定義とシステム定義の両方のワークロード グループのリストを表示するには、フィルターを編集して **[すべて選択]** します。

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png" alt-text="ワークロード グループのリストのフィルター" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/filter-all-workload-groups.png":::

#### <a name="t-sql"></a>T-SQL

T-SQL を使用してワークロード グループを表示するには、[SQL Server Management Studio (SSMS) を使用して専用 SQL プールに接続](../sql/get-started-ssms.md)し、次のクエリを発行します。
 
```sql
SELECT * FROM sys.workload_management_workload_groups;
```

詳細については、「[sys.workload_management_workload_groups](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-groups-transact-sql)」を参照してください。


### <a name="workload-classifiers"></a>ワークロード分類子

#### <a name="azure-portal"></a>Azure portal

Azure portal でワークロード グループ別にすべてのワークロード分類子 (システム定義の分類子を含む) を表示するには、ワークロード グループの表の **[分類子]** 列に表示されている番号を確認します (前のセクションを参照)。

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png" alt-text="ワークロード グループ分類子のリスト" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/view-workload-classifiers.png":::

#### <a name="t-sql"></a>T-SQL

T SQL を使用してすべてのワークロード分類子 (システム定義の分類子を含む) のリストを取得するには、次のクエリを使用します。

```sql
SELECT * FROM sys.workload_management_workload_classifiers;
```

詳細については、「[sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql)」を参照してください。

### <a name="users-and-mapped-resource-classes"></a>ユーザーとマップされたリソース クラス

T SQL でシステム定義のリソース クラスとマップされたユーザーのリストを取得するには、次のクエリを使用します。

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS [Member Name]
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');
```

### <a name="workload-group-and-classifier-details-of-a-request"></a>要求のワークロード グループと分類子の詳細

誤って分類されたワークロードの問題をトラブルシューティングする際の最初の手順は、クエリのワークロード グループとワークロード分類子を識別することです。 送信されたクエリとその分類を表示するには、動的管理ビュー [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を使用します。

```sql
SELECT * FROM sys.dm_pdw_exec_requests;
```

詳細については、「[sys.dm_pdw_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)」を参照してください。

## <a name="common-scenarios-of-misclassified-workloads"></a>誤って分類されたワークロードの一般的なシナリオ

ワークロードが意図せず誤って分類される可能性がある一般的なシナリオを次に示します。

### <a name="mixed-usage-of-resource-classes-and-user-defined-workload-management"></a>リソース クラスとユーザー定義のワークロード管理の同時使用

リソース クラスとワークロード グループが一緒に使用されるシナリオでは、ユーザー ロールからリソース クラスへのマッピングがワークロード分類子のルールと競合し、意図しないクエリの分類につながる可能性があります。 

次のシナリオで考えてみましょう。

1. データベース ユーザー DBAUser は、`sp_addrolemember` プロシージャを使用して largerc リソース クラス ロールに割り当てられています。
1. DBAUser は、ワークロード管理を使用して、新しいワークロード グループと分類子を作成しました。
1. 新しく作成されたワークロード分類子は、データベース ロール DBARole を、重要度の高い mediumrc リソース クラスにマップします。 
1. DBAUser は、DBARole データベース ロールのメンバーになりました。
1. DBAUser がクエリを実行すると、クエリはワークロード分類子に基づいて mediumrc 上で実行されることが期待されます。 代わりに、largerc に割り当てられます。"**ユーザー**" のマッピングは、分類子への "**ロール メンバーシップ**" のマッピングよりも優先されるためです。

ワークロード管理を行うには、リソース クラスとワークロード管理グループを同時に使用しないようにすることをお勧めします。 リソース クラスをワークロードに変換する手順の詳細については、「[リソース クラスからワークロード グループへの変換](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)」を参照してください。 

ただし、リソース クラスとワークロード管理の両方を一緒に使用する必要がある場合もあります。 このようなシナリオでは、誤分類のトラブルシューティングを簡単にするため、ワークロード分類子を作成するときは、リソース クラス ロールのマッピングを削除することをお勧めします。 次のコードは、既存のリソース クラス ロールのメンバーシップを返します。 対応するリソース クラスから返されたメンバー名ごとに、[sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) を実行します。

```sql
SELECT  r.name AS [Resource Class]
, m.name AS membername
FROM    sys.database_role_members AS rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
EXEC sp_droprolemember '[Resource Class]', membername;
```

### <a name="some-administrative-users-are-always-mapped-to-smallrc-workload-group"></a>一部の管理ユーザーが常に smallrc ワークロード グループにマップされる

Azure Synapse Workspace SQL の管理者ログイン、Azure Synapse Azure Active Directory 管理者 (ユーザーまたはグループ メンバー)、またはデータベース所有者のシナリオについて考えてみましょう。 これらのユーザーは、まだワークロード分類子を持っているか、smallrc 以外のリソース クラスのロールに追加されている可能性があります。 これらのユーザーによって実行されるすべてのクエリは、ユーザーが別のリソース クラスまたはワークロード グループにマップされている場合でも、引き続き smallrc リソース クラスで実行されます。 

**推奨**: これらの管理ユーザーは、既定のワークロード グループを変更できません。 詳細については、「[リソース クラスを使用したワークロード管理](resource-classes-for-workload-management.md#default-resource-class)」を参照してください。 重要な、またはパフォーマンスに影響されやすいワークロードは、専用 SQL プールでこれらの管理ユーザーの 1 人として実行しないことをお勧めします。

Azure Synapse Workspace SQL の管理者ログインと Azure Synapse Azure Active Directory 管理者 (ユーザーまたはグループ メンバー) は、Azure portal で指定されます。

:::image type="content" source="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png" alt-text="Workspace SQL の [管理者ログイン] フィールドを参照してサービス管理者を特定する" lightbox="./media/sql-data-warehouse-how-to-troubleshoot-missed-classification/identify-sql-admin.png":::

同様に、データベース所有者 (dbo) および **db_owner** データベースのロールでは、既定のリソース クラスを変更することはできません。 ユーザーがデータベースの所有者であるか、**db_owner** データベース ロールの下に追加された場合、ユーザーによって実行されるすべてのクエリは、既定で smallrc に移動します。 これらのロールは、smallrc 以外のリソース クラスに追加することはできません。 ただし、このロールに含まれているユーザーがクエリを別のワークロード グループに分類する場合は、[ワークロード分類子の定義](sql-data-warehouse-workload-classification.md)で MEMBERNAME オプションを使用できます。


### <a name="use-workload-group-precedence-for-better-classification"></a>分類を改善するためにワークロード グループの優先順位を使用する

ワークロード分類子が複数のワークロード グループにマップされていたり、ユーザーが複数のリソース クラスにマップされていたりするシナリオでは、優先順位によって、選択されたワークロード グループとリソース クラスが決まります。 優先順位のルールのリスト: 

> [!NOTE]
> 「[リソース クラスとユーザー定義のワークロード管理の同時使用](#mixed-usage-of-resource-classes-and-user-defined-workload-management)」のセクションで説明したように、リソース クラスとユーザー定義のワークロード グループやクラスを組み合わせて使用することはお勧めできません。

#### <a name="if-resource-classes-are-being-used"></a>リソース クラスが使用されている場合:

リソース クラスが使用されているシナリオでは、実行するワークロードの種類ごとに専用のユーザーを作成することをお勧めします。 ただし、ユーザーが複数のリソース クラスのメンバーである場合、「[リソース クラスの優先順位](resource-classes-for-workload-management.md#resource-class-precedence)」に記載されている優先順位のルールが有効になります。

1. 動的リソース クラスは静的リソース クラスに優先します。 たとえば、ユーザーが mediumrc (動的) と staticrc80 (静的) のメンバーの場合、ユーザー クエリは mediumrc で実行されます。
1. より大きなリソース クラスは、より小さいリソース クラスよりも優先されます。 たとえば、ユーザーが staticrc20 と staticrc80 のメンバーの場合、ユーザー クエリは staticrc80 を使用して実行されます。

#### <a name="if-workload-management-capabilities-are-used"></a>ワークロード管理機能が使用されている場合

WLM は、同じユーザーまたはワークロード グループ用に複数のワークロード分類子を作成する機能を提供します。 分類子定義ステートメントには、ワークロードに割り当てられている受信要求に基づいて複数のパラメーターがあります。 これらのパラメーターの "**重み**" スコアは次のようになり、このスコアによって優先順位が決まります。 

|**分類子パラメーター** |**Weight**   |
|---------------------|---------|
|MEMBERNAME:USER      |64       |
|MEMBERNAME:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

例を使用して、これらの優先順位ルールの動作を確認しましょう。 ユーザーが次のように 2 つのワークロード分類子を作成する場合:

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,START_TIME     = '18:00' 
 ,END_TIME       = '07:30'
 ,IMPORTANCE     = 'Low')
```

User-1 によって送信されるクエリは、両方の分類子を介して送信できます。 午後 6 時から午前 7 時 (UTC) の間に 'dimension_loads' ラベルを持つ User-1 によって実行されるクエリは `wgDashboards` に割り当てられます。WLM_LABEL の重みスコアが START_TIME/END_TIME より高いためです。 `CLASSIFIER-1` の重み付けは 80 (ユーザーの 64 + WLM_LABEL の 16) です。 `CLASSIFIER-2` の重み付けは 68 (ユーザーの 64 + START_TIME/END_TIME の 4) です。 

ワークロードの分類の詳細については、「[分類の重み付け](sql-data-warehouse-workload-classification.md#classification-weighting)」および[クエリ ラベル](../sql/develop-label.md)に関するページを参照してください。

### <a name="what-happens-if-precedence-in-workload-classification-leads-to-a-tie"></a>ワークロード分類の優先順位が同順位になった場合

ワークロード分類子の優先順位が適用された後でも、クエリを複数のワークロード グループに分類できます。 たとえば、次の分類子を考えてみましょう。

```sql
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'Low');
```

ユーザーが `OPTION (LABEL = 'dimension_loads')` でクエリを実行する場合は、優先順位のルールに基づいて `wgDataLoad` または `wgUserqueries` のいずれかに分類できます。 しかし、どのワークロード グループが選択されるのでしょうか?

#### <a name="workload-group-and-classifier-tie-breakers"></a>ワークロード グループと分類子の優先順位の判断基準

ワークロード グループまたは分類子に同順位だった場合は、次の優先順位が有効になります。

1. リソース割り当てが最も高いワークロード グループが選択されます。 この動作は、ログインが複数のリソース クラスのメンバーであるシナリオでパフォーマンスを最適化します。 この動作により、下位互換性も確保されます。  
    
次の 2 つのワークロード グループとワークロード分類子について考えてみましょう。
    
```sql
-- Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 26 
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2              
,CAP_PERCENTAGE_RESOURCE = 100 )

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 15
,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5               
,CAP_PERCENTAGE_RESOURCE = 50 );

--Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
,MEMBERNAME     = 'USER-1'  
,WLM_LABEL      = 'dimension_loads' 
,IMPORTANCE     = 'High');
```
                  
ユーザーが `OPTION (LABEL = 'dimension_loads')` でクエリを実行する場合、クエリは両方の分類子の条件を満たします。 ただし、要求あたりの最小リソース割り当てが高くなるので、ユーザー要求は `wgUserQueries` ワークロード グループにルーティングされます。

2. 次に、判断基準として、各ワークロード グループのコンカレンシー設定と使用可能なコンカレンシーが考慮されます。 要求が到着した時点で使用可能なコンカレンシーが最も高いワークロード グループは、クエリが実行される可能性が最も高くなります。 

次の 2 つのワークロード グループとワークロード分類子について考えてみましょう。

```sql
--Workload Groups
CREATE WORKLOAD GROUP wgDataLoad
WITH
( MIN_PERCENTAGE_RESOURCE = 30              
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

CREATE WORKLOAD GROUP wgUserqueries
WITH
( MIN_PERCENTAGE_RESOURCE = 30
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2 (concurrency of 15)
 ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 2
 ,CAP_PERCENTAGE_RESOURCE = 100 );

-- Workload Classifiers
CREATE WORKLOAD CLASSIFIER CLASSIFIER-1 WITH  
( WORKLOAD_GROUP = 'wgDataLoad'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');

CREATE WORKLOAD CLASSIFIER CLASSIFIER-2 WITH  
( WORKLOAD_GROUP = 'wgUserqueries'
 ,MEMBERNAME     = 'USER-1'  
 ,WLM_LABEL      = 'dimension_loads' 
 ,IMPORTANCE     = 'High');
```
              
ユーザーが `OPTION (LABEL = 'dimension_loads')` でクエリを実行する場合、クエリは両方の分類子の条件を満たすため、両方が同順位です。 ユーザーがクエリを送信するときに、5 つの同時実行クエリが `wgUserqueries` グループ内で実行され、10 個のクエリが `wgDataLoad` グループ内で実行されるシナリオを考えてみましょう。 ユーザー要求は、`CLASSIFIER-2` を使用して `wgUserqueries` グループにルーティングされます。`wgUserqueries` ワークロード グループは、ユーザーがクエリを送信した時点で使用可能なコンカレンシーが高くなります。

3. 次に、判断基準として、要求の重要度の設定が考慮されます。 優先順位のルールを使用したワークロード分類に同順位があった場合、要求は最も重要度の高いワークロード グループにルーティングされます。 詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください。

4. 最後に、判断基準として、ワークロード グループの作成時間が考慮されます。 ユーザー要求は、最近作成されたワークロード グループにルーティングされます。 


## <a name="next-steps"></a>次の手順
- ワークロードの分類の詳細については、[ワークロード分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。
- ワークロードの重要度の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください

> [!div class="nextstepaction"]
> [ワークロードの重要度の構成に移動](sql-data-warehouse-how-to-configure-workload-importance.md)
 
