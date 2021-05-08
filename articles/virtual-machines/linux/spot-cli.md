---
title: CLI を使用して Azure Spot Virtual Machines をデプロイする
description: CLI を使用して Azure Spot Virtual Machines をデプロイすることでコストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789611"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Azure CLI を使用して Azure Spot Virtual Machines をデプロイする

[Azure Spot Virtual Machines](../spot-vms.md) を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャによって Azure スポット仮想マシンが削除されます。 したがって、Azure スポット仮想マシンは、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

Azure スポット仮想マシンの価格は、リージョンと SKU に基づいて変動します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 Azure スポット仮想マシンの最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在の Azure Spot Virtual Machine の価格または標準 VM の価格のいずれか低い方になります。 最大価格の設定の詳細については、[Azure Spot Virtual Machines の価格](../spot-vms.md#pricing)に関するページを参照してください。

Azure CLI を使用して Azure Spot Virtual Machine を作成するプロセスは、[クイックスタートの記事](./quick-create-cli.md)で詳しく説明されている内容と同じです。 '--priority Spot' パラメーターを追加し、`--eviction-policy` を Deallocate (既定値) または `Delete` に設定して、最大価格または `-1` を指定するだけです。 


## <a name="install-azure-cli"></a>Azure CLI のインストール

Azure Spot Virtual Machines を作成するには、Azure CLI バージョン 2.0.74 以降が実行されている必要があります。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Azure Spot Virtual Machine を作成する

この例では、価格に基づいて削除されない Linux Azure Spot Virtual Machine をデプロイする方法を示しています。 この VM の割り当てを解除するように削除ポリシーが設定されているため、後で再起動することができます。 VM が削除されるときに VM と基になるディスクを削除する場合は、`--eviction-policy` を `Delete` に設定します。

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大請求価格を確認できます。

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>削除をシミュレートする

REST、PowerShell、または CLI を使用して、Azure スポット仮想マシンの削除をシミュレートし、突然の削除に対してアプリケーションがどの程度適切に応答するかをテストすることができます。

ほとんどの場合、アプリケーションの自動テストに役立てることができるように、REST API [仮想マシン - 削除のシミュレーション](/rest/api/compute/virtualmachines/simulateeviction)を使用します。 REST では、`Response Code: 204` はシミュレートされた削除が成功したことを意味します。 シミュレートされた削除と[スケジュールされたイベント サービス](scheduled-events.md)を組み合わせることで、VM が削除されたときにアプリケーションがどのように応答するかを自動化できます。

スケジュールされた実行中のイベントを確認するには、[Azure Friday - Azure Scheduled Events を使用した VM のメンテナンスの準備](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)に関するページをご覧ください。


### <a name="quick-test"></a>クイック テスト

シミュレートされた削除がどのように動作するかを簡単に確認するには、スケジュールされたイベント サービスに対してクエリを実行し、Azure CLI を使用して、削除をシミュレートするときに、どのように表示されるかを確認してみましょう。

スケジュールされたイベント サービスは、初めてイベントを要求したときに、サービスに対して有効になります。 

VM にリモートでログインし、コマンド プロンプトを開きます。 

VM のコマンド プロンプトで、次のように入力します。

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

この最初の応答には最大 2 分かかることがあります。 これ以降は、ほぼ瞬時に出力が表示されます。

(ローカル コンピューターと同様) Azure CLI がインストールされているコンピューターから、[az vm simulate-eviction](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction) を使用して削除をシミュレートします。 リソース グループ名と VM 名を実際の名前に置き換えます。 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

要求が正常に行われた場合、応答の出力は `Status: Succeeded` になります。

スポット仮想マシンへのリモート接続にすぐに戻り、スケジュールされたイベントのエンドポイントに対して、もう一度クエリを実行します。 詳細情報が含まれる出力が得られるまで、次のコマンドを繰り返します。

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

スケジュールされたイベント サービスが削除通知を受け取ると、次のような応答が返されます。

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

`"EventType":"Preempt"`、およびリソースが VM リソース `"Resources":["myspotvm"]` であることを確認できます。 

`"NotBefore"` を確認することで、VM がいつ削除されるかを確認することもできます。VM は指定した時刻より前に削除されないため、これはアプリケーションで正常に終了するための時間になります。


## <a name="next-steps"></a>次のステップ

[Azure PowerShell](../windows/spot-powershell.md)、[ポータル](../spot-portal.md)、または[テンプレート](spot-template.md)を使用して Azure Spot Virtual Machine を作成することもできます。

Azure Spot Virtual Machine については、[Azure retail prices API](/rest/api/cost-management/retail-prices/azure-retail-prices) を使用して現在の価格情報を照会してください。 `meterName` と `skuName` の両方に `Spot` が含まれています。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md)を参照してください。
