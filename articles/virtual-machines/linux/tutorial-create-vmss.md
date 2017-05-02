---
title: "仮想マシン スケール セットを使用して Azure で高可用性アプリを作成する | Microsoft Docs"
description: "仮想マシン スケール セットと Azure CLI を使用して Linux VM に高可用性アプリを作成およびデプロイします。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/05/2017
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 4c76fb202f501f671504646395b800aeb90d8e69
ms.lasthandoff: 04/07/2017

---

# <a name="create-a-highly-available-application-on-linux-with-virtual-machine-scale-sets"></a>仮想マシン スケール セットを使用して Linux に高可用性アプリを作成する
このチュートリアルでは、仮想マシン スケール セットに高可用性アプリを作成する方法について説明します。 また、スケール セット内の仮想マシンの構成を自動化する方法についても説明します。 


## <a name="step-1---create-a-resource-group"></a>手順 1: リソース グループを作成する
このチュートリアルに取り組む前に、最新の [Azure CLI 2.0](/cli/azure/install-azure-cli) がインストールされていることを確認してください。 まだ Azure サブスクリプションにログインしていない場合は、[az login](/cli/azure/#login) でログインし、画面の指示に従ってください。

[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、`myResourceGroupVMSS` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroupVMSS --location westus
```


## <a name="step-2---define-your-app"></a>手順 2 - アプリを定義する
チュートリアルで高可用性および負荷が分散されたアプリを作成した同じ **cloud-init** 構成を使用します。 **cloud-init** の使用の詳細については、「[Use cloud-init to customize a Linux VM during creation (cloud-init を利用し、作成時に Linux VM をカスタマイズする)](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

`cloud-init.txt` というファイルを作成し、次の構成を貼り付けます。

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
  - nginx -s reload
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="step-3---create-scale-set"></a>手順 3 - スケール セットを作成する
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セットは[Azure で高可用性アプリをビルドする](tutorial-load-balance-nodejs.md)チュートリアルで学習した同じコンポーネントを使用します。 これらのコンポーネントは、可用性セット、フォールト ドメインと更新ドメイン、およびロード バランサーとして含まれます。

スケール セットを使用すると、ユーザーに代わってこれらのリソースが作成および管理されます。 スケール セット内の VM の数は定義したルールに基づいて自動的に増減されることがあります。 仮想マシンのソースとして[カスタム イメージを使用](capture-image.md)するか、このチュートリアルで示すように **cloud-init** を使用してデプロイ中に VM を構成できます。

[az vmss create](/cli/azure/vmss#create) を使用して仮想マシン スケール セットを作成します。 次の例は `myScaleSet` という名前のスケール セットを作成します。

```azurecli
az vmss create \
  --resource-group myResourceGroupVMSS \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="step-4---configure-firewall"></a>手順 4 - ファイアウォールを構成する
仮想マシン スケール セットの一部として、ロード バランサーが自動的に作成されました。 ロード バランサーはロード バランサー ルールを使用して定義した VM のセット全体にトラフィックを分散します。 トラフィックが Web アプリに到達することを許可するには、[az network lb probe create](/cli/azure/network/lb/probe#create) を使用してルールを作成します。 次の例では、`myLoadBalancerRuleWeb` という名前の規則を作成します。

```azurecli
az network lb rule create \
  --resource-group myResourceGroupVMSS \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="step-5---test-your-app"></a>手順 5 - アプリをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#show) を使用します。 次の例では、スケール セットの一部として作成された `myScaleSetLBPublicIP` の IP アドレスを取得しています。

```azurecli
az network public-ip show \
    --resource-group myResourceGroupVMSS \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/tutorial-load-balance-nodejs/running-nodejs-app.png)

アプリが動作しているスケール セット内の VM すべてに対してロード バランサーからトラフィックが負荷分散されていることを確認するために、Web ブラウザーを強制的に最新の情報に更新します。


## <a name="step-6---management-tasks"></a>手順 6 - 管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure CLI 2.0 には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
[az vmss scale](/cli/azure/vmss#scale) を使用してスケール セット内の仮想マシンの数を手動で増減させることができます。 次の例は、スケール セット内の VM の数を `5` に増やしています。

```azurecli
az vmss scale --resource-group myResourceGroupVMSS --name myScaleSet --new-capacity 5
```

自動スケール ルールを使用すると、ネットワーク トラフィックや CPU の使用率に合わせてスケール セット内の VM の数をスケールアップまたはスケールダウンする方法を定義できます。 現時点では、これらのルールは Azure CLI 2.0 では設定できません。 自動スケールを構成するには [Azure Portal](https://portal.azure.com) を使用します。

### <a name="get-connection-info"></a>接続情報を取得する
スケール セット内の VM に関する接続情報を取得するには、[az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) を使用します。 このコマンドは、パブリック IP アドレスと SSH を使用して接続を許可する各 VM のポートが出力されます。

```azurecli
az vmss list-instance-connection-info --resource-group myResourceGroupVMSS --name myScaleSet
```

### <a name="delete-resource-group"></a>Delete resource group
リソース グループを削除すると、そこに含まれているリソースもすべて削除されます。

```azurecli
az group delete --name myResourceGroupVMSS
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、**cloud-init** を使用して Web アプリを定義し、デプロイ中に各 VM を構成しました。 VM をキャプチャしてスケール セットでソース イメージを使用する方法について詳しくは、「[Linux 仮想マシンを一般化してキャプチャする方法](capture-image.md)」をご覧ください。

このチュートリアルで紹介した仮想マシン スケール セットのいくつかの機能について詳しくは、次の情報をご覧ください。

- [Azure 仮想マシン スケール セットの概要](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)
- [Azure Load Balancer の概要](../../load-balancer/load-balancer-overview.md)
- [ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御](../../virtual-network/virtual-networks-nsg.md)
