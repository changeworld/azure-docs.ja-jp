---
title: マネージド オンライン エンドポイントを自動スケーリングする
titleSuffix: Azure Machine Learning
description: マネージド エンドポイントをスケールアップする方法について説明します。 CPU、メモリ、ディスク領域を増加し、追加の機能を取得します。
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: seramasu
author: rsethur
ms.reviewer: laobri
ms.custom: devplatv2
ms.date: 11/03/2021
ms.openlocfilehash: b98730e29dc5cc6e8b2e75781b366b5923adf2ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565769"
---
# <a name="autoscale-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイントを自動スケーリングする (プレビュー)

自動スケールでは、アプリケーションの負荷を処理するために適切な量のリソースが自動的に実行されます。 [マネージド エンドポイント](concept-endpoints.md)は、Azure Monitor 自動スケーリング機能との統合によって、自動スケールをサポートします。

Azure Monitor 自動スケーリング機能では、豊富なルール セットがサポートされています。 メトリックベースのスケーリング (たとえば、CPU 使用率 >70%)、スケジュールに基づくスケーリング (たとえば、営業時間のピーク時のルールのスケーリング)、またはその組み合わせを構成できます。 詳細については、「[Microsoft Azure の自動スケールの概要](/azure-monitor/autoscale/autoscale-overview.md)」を参照してください。

:::image type="content" source="media/how-to-autoscale-endpoints/concept-autoscale.png" alt-text="必要に応じてインスタンスを追加または削除する自動スケーリングの図":::

現在、自動スケールは、Azure CLI、REST、ARM、またはブラウザー ベースの Azure portal を使用して管理できます。 Python SDK などの他の Azure ML SDK によって、今後サポートが追加される予定です。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件 

* デプロイされたエンドポイント。 [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングします (プレビュー)](how-to-deploy-managed-online-endpoints.md)。 

## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義

エンドポイントに対して自動スケールを有効にするには、最初に自動スケール プロファイルを定義します。 このプロファイルでは、スケール セット容量の既定値、最小値、および最大値が定義されます。 次の例では、既定容量と最小容量を 2 つの VM インスタンスとして設定し、最大容量を 5 に設定します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

次のスニペットでは、エンドポイントとデプロイの名前を設定します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="set_endpoint_deployment_name" :::

次に、デプロイとエンドポイントの Azure Resource Manager ID を取得します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="set_other_env_variables" :::

次のスニペットでは、自動スケーリング プロファイルを作成します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="create_autoscale_profile" :::

> [!NOTE]
> 詳細については、[自動スケーリングのリファレンス ページ](/cli/azure/monitor/autoscale?view=azure-cli-latest&preserve-view=true)を参照してください

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure Machine Learning スタジオ](https://ml.azure.com)で、ワークスペースを選択し、ページの左側から __[エンドポイント]__ を選択します。 エンドポイントが一覧表示されたら、構成するエンドポイントを選択します。

:::image type="content" source="media/how-to-autoscale-endpoints/select-endpoint.png" alt-text="ポータルのエンドポイント デプロイ エントリのスクリーンショット。":::

エンドポイントの __[詳細]__ タブから、 __[自動スケーリングの構成]__ を選択します。

:::image type="content" source="media/how-to-autoscale-endpoints/configure-auto-scaling.png" alt-text="エンドポイントの詳細の [自動スケーリングの構成] リンクのスクリーンショット。":::

__[リソースをスケーリングする方法を選択します]__ の下で、 __[カスタム自動スケーリング]__ を選択し、構成を開始します。 既定のスケール条件については、次の値を使用します。

* __[スケール モード]__ を __[メトリックに基づいてスケーリングする]__ に設定します。
* __[最小値]__ を __2__ に設定します。
* __[最大値]__ を __5__ に設定します。
* __[既定値]__ を __2__ に設定します。

:::image type="content" source="media/how-to-autoscale-endpoints/choose-custom-autoscale.png" alt-text="カスタム自動スケーリングの選択を示すスクリーンショット。":::

---

## <a name="create-a-rule-to-scale-out-using-metrics"></a>メトリックを使用してスケールアウトするルールを作成する

一般的なスケールアウト ルールは、平均 CPU 負荷が高い場合に VM インスタンスの数を増やすというルールです。 次の例では、5 分間の CPU 平均負荷が 70% を超える場合に、さらに 2 つのノード (最大) を割り当てます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="scale_out_on_cpu_util" :::

このルールは `my-scale-settings` プロファイルに含まれます (`autoscale-name` はプロファイルの `name` と一致)。 `condition` 引数の値は、"VM インスタンス間の平均 CPU 消費量が 5 分間で 70% を超えた場合にルールがトリガーされる必要がある" ことを示しています。 その条件が満たされた場合、さらに 2 つの VM インスタンスが割り当てられます。 

> [!NOTE]
> CLI 構文の詳細については、[`az monitor autoscale`](/cli/azure/monitor/autoscale) を参照してください。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

__[ルール]__ セクションで、 __[ルールの追加]__ を選択します。 __[スケール ルール]__ ページが表示されます。 次の情報を使用して、このページのフィールドにデータを入力します。

* __[メトリック名]__ を __[CPU Utilization Percentage]__ (CPU 使用率) に設定します。
* __[演算子]__ を __[より大きい]__ に設定し、 __[メトリックのしきい値]__ を __70__ に設定します。
* __[期間 (分)]__ を 5 に設定します。 __[時間グレインの統計]__ は __[Average]__ のままにしておきます。
* __[操作]__ を __[カウントを増やす量]__ に設定し、 __[インスタンス数]__ を __2__ に設定します。

最後に、 __[追加]__ ボタンを選択してルールを作成します。

:::image type="content" source="media/how-to-autoscale-endpoints/scale-out-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-out-rule.png" alt-text="「5 分間で CPU 使用率が 70% を超える」という条件のスケールアウト ルールを示すスクリーンショット。":::

---

## <a name="create-a-rule-to-scale-in-using-metrics"></a>メトリックを使用してスケールインするルールを作成する

負荷が軽い場合、スケールインのルールを使用することで、VM インスタンスの数を減らすことができます。 次の例では、CPU 負荷が 5 分間 30% 未満の場合、下限 2 まで、1 つのノードを解放します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="scale_in_on_cpu_util" :::

# <a name="portal"></a>[ポータル](#tab/azure-portal)

__[ルール]__ セクションで、 __[ルールの追加]__ を選択します。 __[スケール ルール]__ ページが表示されます。 次の情報を使用して、このページのフィールドにデータを入力します。

* __[メトリック名]__ を __[CPU Utilization Percentage]__ (CPU 使用率) に設定します。
* __[演算子]__ を __[より小さい]__ に設定し、 __[メトリックのしきい値]__ を __30__ に設定します。
* __[期間 (分)]__ を __5__ に設定します。
* __[操作]__ を __[カウントを減らす量]__ に設定し、 __[インスタンス数]__ を __1__ に設定します。

最後に、 __[追加]__ ボタンを選択してルールを作成します。

:::image type="content" source="media/how-to-autoscale-endpoints/scale-in-rule.png" lightbox="media/how-to-autoscale-endpoints/scale-in-rule.png" alt-text="スケールイン ルールを示すスクリーンショット":::

スケールアウト ルールとスケールイン ルールの両方がある場合、ルールは次のスクリーンショットのようになります。 平均 CPU 負荷が 5 分間で 70% を超える場合は、上限 5 まで、さらに 2 つのノードを割り当てる必要があると指定しました。 CPU 負荷が 5 分間 30% 未満の場合は、下限 2 まで、1 つのノードを解放する必要があります。 

:::image type="content" source="media/how-to-autoscale-endpoints/autoscale-rules-final.png" lightbox="media/how-to-autoscale-endpoints/autoscale-rules-final.png" alt-text="ルールを含む自動スケール設定を示すスクリーンショット。":::

---

## <a name="create-a-scaling-rule-based-on-endpoint-metrics"></a>エンドポイント メトリックに基づいてスケーリング ルールを作成する

前のルールがデプロイに適用されました。 次に、エンドポイントに適用されるルールを追加します。 この例では、要求の待機時間が 5 分間、平均 70 ミリ秒を超える場合、別のノードを割り当てる必要があります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="scale_up_on_request_latency" :::

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ページの下部にある __+ Add a scale condition(+ スケール条件の追加)__ を選択します。

__[メトリックに基づいてスケーリングする]__ を選択してから、 __[ルールの追加]__ を選択します。 __[スケール ルール]__ ページが表示されます。 次の情報を使用して、このページのフィールドにデータを入力します。

* __[メトリック ソース]__ を __[その他のリソース]__ に設定します。
* __[リソースの種類]__ を __[機械学習オンライン エンドポイント]__ に設定します。
* __[リソース]__ をエンドポイントに設定します。
* __[メトリック名]__ を __[要求の待機時間]__ に設定します。
* __[演算子]__ を __[より大きい]__ に設定し、 __[メトリックのしきい値]__ を __70__ に設定します。
* __[期間 (分)]__ を __5__ に設定します。
* __[操作]__ を __[カウントを増やす量]__ に設定し、 __[インスタンス数]__ を 1 に設定します。

:::image type="content" source="media/how-to-autoscale-endpoints/endpoint-rule.png" lightbox="media/how-to-autoscale-endpoints/endpoint-rule.png" alt-text="エンドポイント メトリック ルールを示すスクリーンショット。":::

---

## <a name="create-scaling-rules-based-on-a-schedule"></a>スケジュールに基づいてスケーリング ルールを作成する

特定の日または特定の時間にのみ適用されるルールを作成することもできます。 この例では、週末のノード数が 2 に設定されています。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-moe-autoscale.sh" ID="weekend_profile" :::

# <a name="portal"></a>[ポータル](#tab/azure-portal)

ページの下部にある __+ Add a scale condition(+ スケール条件の追加)__ を選択します。 新しいスケール条件で、次の情報を使用してフィールドに値を入力します。
 
* __[特定のインスタンス数にスケーリングする]__ を選択します。
* __[インスタンス数]__ を __2__ に設定します。
* __[スケジュール]__ を __[特定の曜日に繰り返す]__ に設定します。
* [スケジュール] を __[繰り返し間隔]__ __[土曜日]__ および __[日曜日]__ に設定します。

:::image type="content" source="media/how-to-autoscale-endpoints/schedule-rules.png" lightbox="media/how-to-autoscale-endpoints/schedule-rules.png" alt-text="スケジュールに基づいたルールを示すスクリーンショット":::

---

## <a name="delete-resources"></a>リソースを削除する

デプロイを使用しない場合は、次のように削除します。

:::code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>次の手順

Azure Monitor を使用した自動スケーリングの詳細については、次の記事を参照してください。

- [自動スケール設定について](/autoscale/autoscale-understand-settings)
- [自動スケーリングの一般的なパターンの概要](/autoscale/autoscale-common-scale-patterns)
- [自動スケーリングのベスト プラクティス](/autoscale/autoscale-best-practices)
- [Azure 自動スケーリングのトラブルシューティング](/autoscale/autoscale-troubleshoot)
