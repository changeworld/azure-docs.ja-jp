---
title: "Azure Machine Learning Workbench で精度が最適で最短時間の実行を見つける方法 | Microsoft Docs"
description: "Azure Machine Learning Workbench を使用して CLI で最適な精度を見つけるためのエンドツーエンドのユース ケース"
services: machine-learning
author: totekp
ms.author: kefzhou
manager: akannava
ms.reviewer: akannava, haining, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 9e5c2cc0b9ec17154c5280850d971308abfc2eb3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-runs-with-the-best-accuracy-and-lowest-duration"></a>精度が最適で最短時間の実行を見つける方法
実行が複数回の場合、ユース ケース例として、最適な精度の実行を見つけることがあります。 アプローチの 1 つは、[JMESPath](http://jmespath.org/) クエリでコマンド ライン インターフェイス (CLI) を使用することです。 Azure CLI で JMESPath を使用する方法については、[こちらの記事](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)を参照してください。 次の例では、0、0.98、1、および 1 の精度で 4 個の実行が作成されます。 範囲 `[MaxAccuracy-Threshold, MaxAccuracy]` (`Threshold = .03`) 内に含まれる場合、実行はフィルターされます。

## <a name="sample-data"></a>サンプル データ
`Accuracy` 値の既存の実行がない場合、以下の手順でクエリ用の実行が生成されます。

まず、Workbench で Python ファイルを作成し、`log_accuracy.py` という名前を付け、次のコードを貼り付けます。
```python
from azureml.logging import get_azureml_logger

logger = get_azureml_logger()

accuracy_value = 0.5

if len(sys.argv) > 1:
     accuracy_value = float(sys.argv[1])

logger.log("Accuracy", accuracy_value)
```

次に `run.py` というファイルを作成し、次のコードを貼り付けます。
```python
import os

accuracy_values = [0, 0.98, 1.0, 1.0]
for value in accuracy_values:
    os.system('az ml experiment submit -c local ./log_accuracy.py {}'.format(value))
```

最後に、Workbench で CLI を開き、コマンド `python run.py` を実行して 4 個の実験を送信します。 スクリプトが完了すると、[`Run History`] タブにさらに 4 個の実行が表示されます。

## <a name="querying-the-run-history"></a>実行履歴のクエリ
最初のコマンドで、最大精度の値を見つけます。
```powershell
az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
```

2 つ目のコマンドでは、この `1` の最大精度値と `0.03` のしきい値を使用し、`Accuracy` を使用してフィルターを実行し、`duration` 順で並べ替えを実行します。
```powershell
az ml history list --query '@[?Accuracy >= sum(`[1, -0.03]`)] | sort_by(@, &duration)'
```
> [!NOTE]
> 厳格な上限チェックが必要な場合、クエリの形式は ``@[?Accuracy >= sum(`[$max_accuracy_value, -$threshold]`) && Accuracy <= `$max_accuracy_value`]`` になります。

Powershell を使用する場合、次のコードではしきい値と最大精度を格納するためにローカル変数を使用します。
```powershell
$threshold = 0.03
$max_accuracy_value = az ml history list --query '@[?Accuracy != null] | max_by(@, &Accuracy).Accuracy'
$find_runs_query = '@[?Accuracy >= sum(`[{0}, -{1}]`)] | sort_by(@, &duration)' -f $max_accuracy_value, $threshold
az ml history list --query $find_runs_query
```

## <a name="next-steps"></a>次のステップ
- ログ記録の詳細については、「[Azure Machine Learning Workbench で実行履歴とモデル メトリックを使用する方法](how-to-use-run-history-model-metrics.md)」を参照してください。    
