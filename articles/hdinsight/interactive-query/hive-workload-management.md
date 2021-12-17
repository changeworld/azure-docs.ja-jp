---
title: Hive LLAP ワークロード管理機能
titleSuffix: Azure HDInsight
description: Hive LLAP ワークロード管理機能
ms.service: hdinsight
ms.topic: how-to
author: guptanikhil007
ms.author: guptan
ms.reviewer: jasonh
ms.date: 05/25/2021
ms.openlocfilehash: 8dde65d0547552af90caddd214dd1ceda5a754ee
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482414"
---
# <a name="hive-llap-workload-management-wlm-feature"></a>Hive LLAP ワークロード管理 (WLM) 機能
Interactive Query クラスター、特にマルチテナント環境では、リソース管理が不可欠です。 Hive LLAP (低待機時間の分析処理) の場合、ワークロード管理を使用して、ユーザーが特定のワークロードのニーズに一致し、それらのリソースの不一致を回避できます。 <br> ワークロード管理には、リソース プール (クエリ プールとも呼ばれる) が実装されています。これにより、Hive や LLAP で使用できるリソースを、特定のワークロードに使用されるプールに分割できます。
また、個々のリソース プールごとに、リソースの割合とクエリの並列処理を構成することもできます。

!['LLAP のアーキテクチャ。'](./media/hive-workload-management/llap-architecture.png)

## <a name="enable-hive-llap-workload-management-feature-for-hdinsight-clusters"></a>HDInsight クラスターの Hive LLAP ワークロード管理機能を有効にする

次の手順に従って、HDInsight Interactive Query クラスターでワークロード管理機能を有効にします。
1. 新しい yarn キューを作成します。これを使用して、ワークロード管理の Tez AM を表示できます。
2. Ambari を使用してクラスター構成を変更し、Hive の機能を有効にします。
3. リソース プランを作成してアクティブ化します。

### <a name="create-a-new-yarn-queue-suitable-for-workload-management-feature"></a>ワークロード管理機能に適した新しい yarn キューを作成する
次の[ガイド](../hdinsight-troubleshoot-yarn.md)を使用して、`wm` という名前の新しい yarn キューを作成します。
次の構成に基づいて、クラスターで `wm` キューを構成します。

| QueueName   | 容量 | 最大容量 | 優先度 | 最大 AM リソース |
|------------|---------|--------------|----------|---------------------|
| `default`   | 5%       | 5%           | 0        | 33%                 |
| `llap`      | 85%      | 100%         | 10       | 33%                 |
| `wm`        | 10%      | 15%          | 9        | 100%                |

`wm` キューの構成が次の表示のようになっていることを確認します。
:::image type="content" source="./media/hive-workload-management/wm-yarn-queue.png" alt-text="wm キューの構成。":::

### <a name="enable-workload-management-feature-in-hive-configs"></a>Hive 構成でワークロード管理機能を有効にする
次のプロパティをカスタム hiveserver2 対話型サイトに追加し、その値を、新しく作成された yarn queue (`wm`) の名前に設定します。 構成の変更を反映させるため、対話型 HiveServer を再起動します。
```
hive.server2.tez.interactive.queue=wm
```

### <a name="create-resource-plan"></a>リソース プランを作成する
基本的なリソース プランを作成する方法の例を次に示します。
!['基本的なリソース プラン。'](./media/hive-workload-management/wlm-resourceplan.jpg)

beeline を使用して次のコマンドを実行し、上記のリソース プランを作成します。

#### <a name="commands-to-create-view-and-drop-the-resource-plan"></a>リソース プランを作成、表示、削除するコマンド
```hql
# CREATE RESOURCE PLAN
CREATE RESOURCE PLAN demo_plan;

# CREATE POOLS
ALTER POOL demo_plan.default SET ALLOC_FRACTION = 0.65, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.etl WITH ALLOC_FRACTION = 0.20, QUERY_PARALLELISM = 2;
CREATE POOL demo_plan.sys_accounts WITH ALLOC_FRACTION = 0.15, QUERY_PARALLELISM = 1;

# CREATE MAPPING
CREATE USER MAPPING 'hive' IN demo_plan TO sys_accounts WITH ORDER 1;
 
# CREATE TRIGGERS
CREATE TRIGGER demo_plan.defaultToETL WHEN  ELAPSED_TIME > 20000 DO MOVE TO etl;
ALTER TRIGGER demo_plan.defaultToETL ADD TO POOL default;
CREATE TRIGGER demo_plan.ETLKill  WHEN ELAPSED_TIME > 40000 DO KILL;
ALTER TRIGGER demo_plan.ETLKill ADD TO POOL etl;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# VALIDATE PLAN
ALTER RESOURCE PLAN demo_plan VALIDATE;

# ENABLE PLAN
ALTER RESOURCE PLAN demo_plan ENABLE;

#  ACTIVATE PLAN
ALTER RESOURCE PLAN demo_plan ACTIVATE;

# SHOW RESOURCE PLAN
SHOW RESOURCE PLANS;
SHOW RESOURCE PLAN demo_plan;

# DISABLE PLAN
-- In case plan is in active state first run:
-- DISABLE WORKLOAD MANAGEMENT;
ALTER RESOURCE PLAN demo_plan DISABLE;

# DROP RESOURCE PLAN
DROP RESOURCE PLAN demo_plan;
```

## <a name="understanding-resource-plan"></a>リソース プランについて
最適なリソース プランを作成するには、ワークロードの要件を十分に理解している必要があります。

### <a name="number-of-pools"></a>プールの数
プールの数は、クエリの並列処理の合計によって制限されます (プールあたり最低 1 つのクエリ)。
ほとんどのワークロードで、3 つを超えるプールが必要になることはありません。 
- 既定値 (対話型クエリの場合) 
- etl/batch (実行時間の長いクエリの場合)
- sys (システム管理者の場合)

### <a name="total-query_parallelism"></a>QUERY_PARALLELISM の合計
QUERY_PARALLELISM の合計、または同時実行クエリの合計数は、以下の式を使用して求められます。

```
Number of total concurrent queries(Tez AMs) = Math.floor( (total cluster memory capacity / size of Tez AM container) x percentage of wm queue capacity)
```

次に例を示します。 <br/>
Tez AM コンテナーのサイズが 4 GB、yarn クラスターの総メモリ容量が 400 GB で、そのうち 10% が wm キューに割り当てられているとします。その場合、次のようになります。 <br/>
同時実行クエリの合計数 = floor ((400/4) x 0.10) = 10

> [!Tip]
> Tez AM が承認済み状態で停止されないようにするために、wm キューの容量を必要以上に確保、つまり、`wm` キューの容量を 10.01% にして、`default` キューの容量を 4.99% に減らすことができます。

### <a name="mappings"></a>マッピング
マッピングにより、特定のプールにクエリを転送するメカニズムが用意されます。 マッピングの数が増えると、指定された 1 つのクエリに複数のルールが適用される場合があります。 どのルールを優先すべきかを設定するには: 省略可能な `WITH ORDER` 句で順序を指定されている場合、順序の低いルールが優先されます。 それ以外の場合、`user` ルールは `application` ルールよりも優先され、`application` ルールは `group` ルールよりも優先されます。 <br/>
優先順位が同じグループ ルールの順序は未定義です。


> [!Note]
> * WLM プランがアクティブな場合、`llap` キュー内の Tez AM は使用されません。 `llap` キュー内のこれらの Tez AM は、WLM リソース プランが無効になっている場合、すぐに使用できます。
> * WLM リソース プランを有効にすると、指定されたリソース プランに対して構成された `QUERY_PARALLELISM` の合計と同じ数の Tez AM が起動されます。 これらの Tez AM が承認済み状態で停止されないように、`wm` キューのサイズを調整する必要があります。
> * リソース プランでの使用には、次の 2 つのカウンターの使用のみがサポートされています。
>    * EXECUTION_TIME
>    * ELAPSED_TIME

## <a name="related-articles"></a>関連記事
* [Hive LLAP Workload Management のコマンドの概要](workload-management-commands.md)
* [Hive LLAP ワークロード管理に関する問題のトラブルシューティング](troubleshoot-workload-management-issues.md)


