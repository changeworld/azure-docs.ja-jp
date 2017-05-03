---
title: "Azure で Linux 用の仮想マシン スケール セットを作成する | Microsoft Docs"
description: "仮想マシン スケール セットを使って、Linux VM に高可用性アプリを作成およびデプロイします"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/18/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: 73167924f95c8cea0ac3cb4651cb3571fb24cc01
ms.lasthandoff: 04/21/2017

---

# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>仮想マシン スケール セットを作成して Linux に高可用性アプリをデプロイする
このチュートリアルでは、Azure で仮想マシン スケール セットを使うことにより、アプリを実行する仮想マシン (VM) の数をすばやく拡張する方法について説明します。 仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU の使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 実際に動いている仮想マシン スケール セットを確認するため、複数の Linux VM で動作する Node.js アプリを作成します。

このチュートリアルの手順は、最新バージョンの [Azure CLI 2.0](/cli/azure/install-azure-cli) を使用して行うことができます。


## <a name="scale-set-overview"></a>スケール セットの概要
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セットは、前の[高可用性 VM の作成](tutorial-availability-sets.md)チュートリアルで学習したものと同じコンポーネントを使います。 スケール セット内の VM は、可用性セット内に作成されて、論理障害ドメインおよび更新ドメインに配布されます。

スケール セットには必要に応じて VM が作成されます。 自動スケール ルールを定義して、スケール セットの VM を追加または削除する方法とタイミングを制御できます。 これらのルールは、CPU の負荷、メモリの使用量、ネットワーク トラフィックなどのメトリックに基づいて発動できます。

Azure プラットフォーム イメージを使う場合、スケール セットは最大 1,000 個の VM をサポートします。 運用環境のワークロードでは、[カスタム VM イメージの作成](tutorial-custom-images.md)が必要な場合があります。 カスタム イメージを使うと、最大 100 個の VM をスケール セットに作成できます。


## <a name="create-an-app-to-scale"></a>スケーリングするアプリを作成する
運用環境で使う場合は、インストールと構成が済んだアプリケーションを含む[カスタム VM イメージを作成](tutorial-custom-images.md)できます。 このチュートリアルでは、スケール セットの動作をすぐに確認できるように初回起動時に VM をカスタマイズします。

前のチュートリアルでは、cloud-init を使って [Linux 仮想マシンを初回起動時にカスタマイズする方法](tutorial-automate-vm-deployment.md)を説明しました。 同じ cloud-init 構成ファイルを使って、NGINX をインストールし、単純な "Hello World" Node.js アプリを実行することができます。 `cloud-init.txt` というファイルを作成し、次の構成を貼り付けます。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>スケール セットを作成する
スケール セットを作成する前に、[az group create](/cli/azure/group#create) を使ってリソース グループを作成します。 次の例では、`myResourceGroupScaleSet` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroupScaleSet --location westus
```

ここでは、[az vmss create](/cli/azure/vmss#create) を使って仮想マシン スケール セットを作成します。 以下の例では、`myScaleSet` という名前のスケール セットを作成し、cloud-int ファイルを使って VM をカスタマイズして、存在しない場合は SSH キーを生成します。

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="allow-web-traffic"></a>Web トラフィックを許可する
仮想マシン スケール セットの一部として、ロード バランサーが自動的に作成されました。 ロード バランサーはロード バランサー ルールを使用して定義した VM のセット全体にトラフィックを分散します。 ロード バランサーの概念と構成につい詳しくは、次のチュートリアル「[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)」をご覧ください。

トラフィックが Web アプリに到達することを許可するには、[az network lb rule creat](/cli/azure/network/lb/rule#create) を使ってルールを作成します。 次の例では、`myLoadBalancerRuleWeb` という名前の規則を作成します。

```azurecli
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>アプリケーションをテストする
Web 上の Node.js アプリを確認するには、[az network public-ip show](/cli/azure/network/public-ip#show) でロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された `myScaleSetLBPublicIP` の IP アドレスを取得しています。

```azurecli
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/tutorial-create-vmss/running-nodejs-app.png)

動作しているスケール セットを確認するには、Web ブラウザーを強制的に最新の情報に更新して、アプリが動作しているすべての VM に対してロード バランサーからトラフィックが負荷分散されているのを見ることができます。


## <a name="management-tasks"></a>管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure CLI 2.0 には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セットで実行されている VM の一覧を表示するには、[az vmss list-instances](/cli/azure/vmss#list-instances) を次のように使います。

```azurecli
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

出力は次の例のようになります。

```azurecli
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  westus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  westus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
現在スケール セット内にあるインスタンスの数を見るには、[az vmss show](/cli/azure/vmss#show) と `sku.capacity` に対するクエリを使います。

```azurecli
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

[az vmss scale](/cli/azure/vmss#scale) を使って、スケール セット内の仮想マシンの数を手動で増減させることができます。 次の例では、スケール セット内の VM の数を `5` に設定します。

```azurecli
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```

自動スケール ルールを使用すると、ネットワーク トラフィックや CPU の使用率に合わせてスケール セット内の VM の数をスケールアップまたはスケールダウンする方法を定義できます。 現時点では、これらのルールは Azure CLI 2.0 では設定できません。 自動スケールを構成するには [Azure Portal](https://portal.azure.com) を使用します。

### <a name="get-connection-info"></a>接続情報を取得する
スケール セット内の VM に関する接続情報を取得するには、[az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) を使用します。 このコマンドでは、SSH での接続を許可する各 VM のパブリック IP アドレスとポートが出力されます。

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupScaleSet --name myScaleSet
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシン スケール セットの作成方法を説明しました。 次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

[仮想マシンを負荷分散する](tutorial-load-balancer.md)

