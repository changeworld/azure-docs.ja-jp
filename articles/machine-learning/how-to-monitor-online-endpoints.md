---
title: マネージド オンライン エンドポイント (プレビュー) を監視する
titleSuffix: Azure Machine Learning
description: マネージド オンライン エンドポイントを監視し、Application Insights を使用してアラートを作成します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: how-to, devplatv2
ms.openlocfilehash: ecde110225ef42ace6b19e54930ee9f4152acec6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558632"
---
# <a name="monitor-managed-online-endpoints-preview"></a>マネージド オンライン エンドポイント (プレビュー) を監視する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

この記事では、[Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー)](concept-endpoints.md) を監視する方法について説明します。 Application Insights を使用してメトリックを表示し、マネージド オンライン エンドポイントの最新の状態を常に把握しておくためのアラートを作成します。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * マネージド オンライン エンドポイントのメトリックを表示する
> * メトリックのダッシュボードを作成する
> * メトリック アラートの作成

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) をデプロイします。
- 少なくとも、エンドポイントに対する[閲覧者アクセス権](../role-based-access-control/role-assignments-portal.md)が必要です。

## <a name="view-metrics"></a>メトリックを表示する

マネージド エンドポイントまたはデプロイのメトリックを表示するには、次の手順に従います。
1. [Azure ポータル](https://portal.azure.com)にアクセスします。
1. マネージド オンライン エンドポイントまたはデプロイ リソースに移動します。

    マネージド オンライン エンドポイントとデプロイは、それらを所有しているリソース グループに移動することによって見つけることができる、Azure Resource Manager (ARM) リソースです。 **[Machine Learning online endpoint]\(Machine Learning オンライン エンドポイント\)** と **[Machine Learning online deployment]\(Machine Learning オンライン デプロイ\)** というリソースの種類を探します。

1. 左側の列で、 **[メトリック]** を選択します。

## <a name="available-metrics"></a>使用可能なメトリック

選択したリソースに応じて、表示されるメトリックは異なります。 メトリックのスコープは、マネージド オンライン エンドポイントとマネージド オンライン デプロイ (プレビュー) で異なります。

### <a name="metrics-at-endpoint-scope"></a>エンドポイント スコープのメトリック

- 要求の待機時間
- 要求待ち時間 P50 (50 パーセンタイルでの要求待ち時間)
- 要求待ち時間 P90 (90 パーセンタイルでの要求待ち時間)
- 要求待ち時間 P95 (95 パーセンタイルでの要求待ち時間)
- 1 分あたりの要求数

次のディメンションで分割:

- デプロイ
- 状態コード
- 状態コード クラス

### <a name="metrics-at-deployment-scope"></a>デプロイ スコープのメトリック

- CPU 使用率
- デプロイ容量 (要求されたインスタンスの種類のインスタンスの数)
- ディスク使用率
- GPU メモリ使用率 (GPU インスタンスのみ該当)
- GPU 使用率 (GPU インスタンスのみ該当)
- メモリ使用率

次のディメンションで分割:

- InstanceId

## <a name="create-a-dashboard"></a>ダッシュボードを作成する

カスタム ダッシュボードを作成して、マネージド オンライン エンドポイントのメトリックなど、複数のソースからのデータを Azure portal で視覚化できます。 詳細については、[Application Insights を使用したカスタム KPI ダッシュボードの作成](../azure-monitor/app/tutorial-app-dashboards.md#add-custom-metric-chart)に関するセクションを参照してください。
    
## <a name="create-an-alert"></a>アラートを作成する

また、カスタム アラートを作成して、マネージド オンライン エンドポイントに対する重要な状態の更新を通知することもできます。

1. メトリック ページの右上で、 **[新しいアラート ルール]** を選択します。

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-new-alert-rule.png" alt-text="オンライン エンドポイントの監視: 赤い四角形で囲まれた [新しいアラート ルール] ボタンを示すスクリーンショット":::

1. 条件名を選択して、どのようなときにアラートがトリガーされるかを指定します。

    :::image type="content" source="./media/how-to-monitor-online-endpoints/online-endpoints-configure-signal-logic.png" alt-text="オンライン エンドポイントの監視: 赤い四角形で囲まれた [シグナル ロジックの構成] ボタンを示すスクリーンショット":::

1. **[Add action groups]\(アクション グループの追加\)**  >  **[Create action groups]\(アクション グループの作成\)** の順に選択して、アラートがトリガーされたときに何が起きるようにするかを指定します。

1. **[アラート ルールの作成]** を選択して、アラートの作成を完了します。


## <a name="next-steps"></a>次の手順

* [デプロイされたエンドポイントのコストを表示する](./how-to-view-online-endpoints-costs.md)方法について学習します。
* [メトリックス エクスプローラー](../azure-monitor/essentials/metrics-charts.md)の詳細情報を読みます。