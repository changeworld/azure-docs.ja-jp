---
title: 'チュートリアル: Azure CLI 2.0 を使用したスケール セットの自動スケール | Microsoft Docs'
description: Azure CLI 2.0 を使用して CPU 需要の増減に合わせて仮想マシンのスケール セットを自動的にスケーリングする方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>チュートリアル: Azure CLI 2.0 を使用して仮想マシン スケール セットを自動的にスケーリングする
スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケールにより、顧客のニーズに対応したり、アプリのライフサイクル全体でアプリケーション パフォーマンスの変化に対応したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.29 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 


## <a name="create-a-scale-set"></a>スケール セットを作成する
自動スケール ルールの作成に役立てるために、サブスクリプション ID、リソース グループとスケール セット名、および場所に関するいくつかのパラメーターを定義します。

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

次のように [az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

ここでは、[az vmss create](/cli/azure/vmss#create) を使って仮想マシン スケール セットを作成します。 次の例では、インスタンス数が *2* のスケール セットを作成し、SSH キーが存在しない場合は SSH キーを生成します。

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義
自動スケール ルールは、Azure CLI 2.0 を使用して、JSON (JavaScript Object Notation) としてデプロイされます。 ここで、この自動スケール プロファイルの各部分を確認した後、完全な例を作成しましょう。

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


## <a name="create-a-rule-to-autoscale-out"></a>自動スケールアウト ルールの作成
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

CPU に対する負荷の平均が 5 分間に 70% を上回った場合にスケール セット内の VM インスタンスの数を増やすルールを作成してみましょう。 ルールがトリガーされると、VM インスタンスの数が 3 つ増えます。

このルールでは、次のパラメーターが使用されます。

| パラメーター         | 説明                                                                                                         | 値           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | スケール セット アクションを監視して適用するパフォーマンス メトリック。                                                   | Percentage CPU  |
| *timeGrain*       | 分析のためにメトリックを収集する頻度。                                                                   | 1 分        |
| *timeAggregation* | 分析のために収集したメトリックの集計方法を定義します。                                                | 平均         |
| *timeWindow*      | メトリックとしきい値を比較する前に監視する時間。                                   | 5 分       |
| *operator*        | しきい値に対してメトリック データを比較するために使用する演算子。                                                     | より大きい    |
| *threshold*       | 自動スケール ルールがアクションをトリガーする値。                                                      | 70%             |
| *direction*       | ルールが適用されるときにスケール セットをスケールインするかスケールアウトするかを定義します。                                              | Increase (増加)        |
| *type*            | VM インスタンスの数を特定の値で変更することを示します。                                    | 変更数    |
| *値*           | ルールが適用されるときにスケールインまたはスケールアウトする VM インスタンスの数。                                             | 3               |
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
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>自動スケールイン ルールの作成
夜間や週末は、アプリケーションの需要が低下する可能性があります。 この低下した負荷が一定期間持続する場合、スケール セット内の VM インスタンスの数を減らす自動スケール ルールを構成できます。 このスケールイン アクションによって、現在の需要を満たすのに必要な数のインスタンスのみが実行されるようになるため、スケール セットの実行コストが削減されます。

CPU に対する負荷の平均が 5 分間に 30% を下回った場合にスケール セット内の VM インスタンスの数を減らす別のルールを作成します。 次の例では、VM インスタンスの数を 1 つスケールインするルールを定義します。 *metricResourceUri* は、サブスクリプション ID、リソース グループ名、およびスケール セット名に以前に定義した変数を使用します。

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>自動スケール ルールをスケール セットに適用
最後の手順では、自動スケールのプロファイルとルールをスケール セットに適用します。 これでアプリケーションの需要に基づいて、スケール セットを自動的にスケールインまたはスケールアウトすることができます。 次のように [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) を使用して自動スケール プロファイルを適用します。 次の完全な JSON は、前のセクションで説明したプロファイルとルールを使用します。

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
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
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
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
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
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


## <a name="generate-cpu-load-on-scale-set"></a>スケール セットに対する CPU 負荷の生成
自動スケール ルールをテストするには、スケール セットの VM インスタンスに対する CPU 負荷を生成します。 このシミュレートされた CPU 負荷を適用すると、自動スケールによってスケールアウトされ、VM インスタンスの数が増えます。 次に、シミュレートされた CPU 負荷を小さくすると、自動スケール ルールによってスケールインされ、VM インスタンスの数が減ります。

最初に、[az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) を使用して、スケール セット内の VM インスタンスに接続するためのアドレスとポートを一覧表示します。

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

次の出力例には、インスタンス名、ロード バランサーのパブリック IP アドレス、およびネットワーク アドレス変換 (NAT) 規則によってトラフィックが転送されるポート番号が示されています。

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

最初の VM インスタンスに SSH 接続します。 `-p` パラメーターを使用して、独自のパブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

ログインしたら、**stress** ユーティリティをインストールします。 最初は *10* 個の **stress** worker を使用して CPU 負荷を生成します。 これらの worker は、*420* 秒間実行されます。これは、自動スケール ルールで目的のアクションを実行するのに十分な値です。

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

**stress** から *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* のような出力が表示されたら、*Enter* キーを押してプロンプトに戻ります。

**stress** によって CPU 負荷が生成されていることを確認するために、**top** ユーティリティを使用してアクティブなシステム負荷を調べます。

```azuecli-interactive
top
```

**top** を終了し、VM インスタンスへの接続を閉じます。 **stress** は、VM インスタンスで実行され続けます。

```azurecli-interactive
Ctrl-c
exit
```

前の [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) によって一覧表示されたポート番号を使用して、2 番目の VM インスタンスに接続します。

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

**stress** をインストールして実行し、この 2 番目の VM インスタンスで 10 個の worker を起動します。

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

再び **stress** から *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* のような出力が表示されたら、*Enter* キーを押してプロンプトに戻ります。

2 番目の VM インスタンスへの接続を閉じます。 **stress** は、VM インスタンスで実行され続けます。

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>アクティブな自動スケール ルールの監視
スケール セット内の VM インスタンスの数を監視するには、**watch** を使用します。 各 VM インスタンスで **stress** によって生成された CPU 負荷に対するスケールアウト プロセスが自動スケール ルールによって開始されるまでに 5 分かかります。

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

CPU しきい値に達すると、自動スケール ルールによってスケール セット内の VM インスタンスの数が増えます。 次の出力には、スケール セットの自動スケールアウトによって作成された 3 つの VM が示されています。

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

**stress** が最初の VM インスタンスで停止すると、平均 CPU 負荷は正常に戻ります。 その 5 分後、自動スケール ルールによって VM インスタンスの数がスケールインされます。 スケールイン アクションでは、ID が最も大きな VM インスタンスが最初に削除されます。 次の出力例には、スケール セットの自動スケールインによって削除された 1 つの VM インスタンスが示されています。

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

`Ctrl-c` キーを押して *watch* を終了します。 スケール セットは、引き続き 5 分ごとにスケールインされ、その都度 VM インスタンスが 1 つ削除されます。この操作は、VM インスタンスの数が最小数の 2 になるまで繰り返されます。


## <a name="clean-up-resources"></a>リソースのクリーンアップ
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group#az_group_delete) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure CLI 2.0 を使用してスケール セットを自動的にスケールインまたはスケールアウトする方法について学習しました。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする

実際に動作している仮想マシン スケール セットの例については、次のサンプルの Azure CLI 2.0 サンプル スクリプトを参照してください。

> [!div class="nextstepaction"]
> [Azure CLI 2.0 用のスケール セット スクリプトのサンプル](cli-samples.md)