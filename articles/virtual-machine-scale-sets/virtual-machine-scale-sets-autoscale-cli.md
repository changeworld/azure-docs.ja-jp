---
title: "Azure CLI を使用した仮想マシン スケール セットの自動スケール | Microsoft Docs"
description: "Azure CLI 2.0 を使用した仮想マシン スケール セットの自動スケール ルールを作成する方法"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0 を使用して仮想マシン スケール セットを自動的にスケールする
スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケール機能により、顧客のニーズに対応したり、アプリのライフ サイクルを通じて、アプリケーション パフォーマンスの変化に対応することができます。

この記事では、スケール セット内の VM インスタンスのパフォーマンスを監視する自動スケール ルールを Azure CLI 2.0 を使用して作成する方法を示します。 これらの自動スケール ルールは、これらのパフォーマンス メトリックに応じて VM インスタンスの数を増減します。 これらの手順は、[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) または [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md) を使用して完了することもできます。


## <a name="prerequisites"></a>前提条件
自動スケール ルールを作成するには、既存の仮想マシン スケール セットが必要です。 スケール セットは、[Azure Portal](virtual-machine-scale-sets-create-portal.md)、[Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)、または[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) を使用して作成できます。

自動スケール ルールを作成しやすくするため、使用するスケール セット用にいくつかの変数を定義します。 次の例は、*myResourceGroup* という名前のリソース グループ内に含まれ、かつ *eastus* リージョン内にある、 *myScaleSet* という名前のスケール セットの変数を定義します。 サブスクリプション ID は、[az account show](/cli/azure/account#az_account_show) を使用して取得します。 複数のサブスクリプションがアカウントに関連付けられている場合は、最初のサブスクリプションだけが返されます。 名前とサブスクリプション ID を次のように調整します。

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義
自動スケール ルールは、Azure CLI 2.0 を使用して、JSON (JavaScript Object Notation) としてデプロイされます。 自動スケール ルールを定義してデプロイする完全な JSON は、この記事の後半に記載されています。 

自動スケール プロファイルの先頭で、スケール セット容量の既定値、最小値、および最大値を定義します。 次の例では、既定および最小の容量として *2* つの VM インスタンスを設定し、最大の容量として *10* を定義しています。

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>自動的にスケールアウトするルールの作成
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

CPU に対する負荷の平均が 10 分間に 70% を上回った場合に、スケール セット内の VM インスタンスの数を増やすルールを作成してみましょう。 ルールがトリガーされると、VM インスタンスの数が 20% 増加します。 VM インスタンス数が少ないスケール セットでは、`type` を *ChangeCount* に設定して、`value` を *1* つまたは *2* つのインスタンスに増やことができます。 VM インスタンス数が多いスケール セットでは、VM インスタンスを 10% または 20% 増やすとより適切になる場合があります。

このルールでは、次のパラメーターが使用されます。

| パラメーター         | 説明                                                                                                         | 値           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | スケール セット アクションを監視して適用するパフォーマンス メトリック。                                                   | Percentage CPU  |
| *timeGrain*       | 分析のためにメトリックを収集する頻度。                                                                   | 1 分        |
| *timeAggregation* | 分析のために収集したメトリックの集計方法を定義します。                                                | 平均         |
| *timeWindow*      | メトリックとしきい値を比較する前に監視する時間。                                   | 10 分      |
| *operator*        | しきい値に対してメトリック データを比較するために使用する演算子。                                                     | より大きい    |
| *threshold*       | 自動スケール ルールがアクションをトリガーする値。                                                      | 70%             |
| *direction*       | ルールが適用されるときに、スケール セットをスケールアップするかスケールダウンするかを定義します。                                             | Increase (増加)        |
| *type*            | VM インスタンスの数をパーセンテージで変更することを示します。                                 | Percent Change (変化率)  |
| *値*           | ルールが適用されるときに、スケールアップまたはスケールダウンする VM インスタンスの数。                                            | 20              |
| *cooldown*        | ルールを再度適用する前に待機する時間。この値を超えると、自動スケール操作が反映されます。 | 5 分       |

次の例では、VM インスタンスの数をスケールアウトするルールを定義します。 *metricResourceUri* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>自動的にスケールインするルールの作成
夜間や週末は、アプリケーションの需要が低下する可能性があります。 この低下した負荷が一定期間持続する場合、スケール セット内の VM インスタンスの数を減らす自動スケール ルールを構成できます。 このスケールイン アクションは、現在の需要を満たすのに必要な数のインスタンスのみを実行するため、スケール セットの実行コストを削減します。

CPU に対する負荷の平均が 10 分間に 30% を下回った場合に、スケール セット内の VM インスタンスの数を減らす別のルールを作成します。 次の例では、VM インスタンスの数をスケールアウトするルールを定義します。 *metricResourceUri* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>自動スケール ルールをスケール セットに適用
最後の手順では、自動スケールのプロファイルとルールをスケール セットに適用します。 これでアプリケーションの需要に基づいて、スケールが自動的にスケールインまたはスケールアウトすることができます。 次のように [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) を使用して自動スケール プロファイルを適用します。 完全な JSON は、前のセクションで説明したプロファイルとルールを使用します。

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>スケール セット内のインスタンスの数の監視
VM インスタンスの数と状態を確認するには、[az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) を使用してスケール セット内のインスタンスの一覧を表示します。 状態は、VM インスタンスが自動的にスケールアウトするスケール セットとしてプロビジョニングされているかどうか、または自動的にスケールインするスケールとしてプロビジョニング解除されているかどうかを示します。 次の例は、*myResourceGroup* という名前のリソース グループ内の *myScaleSet* という名前のスケール セットの VM インスタンスの状態を表示します。

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>スケジュールに基づいた自動スケール
前の例では、CPU 使用率などの基本的なホスト メトリックを使用して、スケール セットを自動的にスケールインまたはスケールアウトしました。 スケジュールに基づいて自動スケール ルールを作成することもできます。 これらのスケジュール ベースのルールを使用すると、アプリケーションの需要の増加が予想されるコアタイムなどの前に、VM インスタンスの数を自動的にスケールアウトしたり、週末などの需要の低下が見込まれる時間にインスタンスの数を自動的にスケールインすることができます。

スケジュールベースの自動スケール ルールを使用するには、開始時間と終了時間が固定された時間枠で実行される VM インスタンスの数を定義する JSON プロファイルを作成します。 次の例では、各作業日 (月曜から金曜) の午前 *9* 時に、インスタンスを *10* にスケール アウトするルールを定義します。

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

夜間にスケールインするため、減らす VM インスタンス数と適切な開始時刻を指定する別のルールを作成します。

次の完全な例では、[az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) を使用して、スケールアウトしてからスケールインし、自動スケール プロファイルを適用するルールを定義します。 この例では、前の例で作成したメトリックベースの自動スケール ルールを上書きします。 *metricResourceUri* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>次のステップ
この記事では、自動スケール ルールを使用して、水平方向にスケーリングして、スケール セット内の VM インスタンスの*数*を増減する方法について説明しました。 垂直方向にスケーリングして、VM インスタンスの*サイズ*を増減することもできます。 詳細については、「[仮想マシン スケール セットの使用を開始する](virtual-machine-scale-sets-vertical-scale-reprovision.md)」を参照してください。

VM インスタンスの管理方法については、「[Manage virtual machine scale sets with Azure PowerShell](virtual-machine-scale-sets-windows-manage.md)」 (Azure PowerShell を使用した仮想マシン スケール セットの管理) を参照してください。

自動スケール ルールをトリガーするときにアラートを生成する方法について詳しくは、「[Azure Monitor で自動スケール操作を使用して電子メールと webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)」をご覧ください。 [Azure Monitor で監査ログを使用して電子メールと webhook アラート通知を送信する](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)こともできます。
