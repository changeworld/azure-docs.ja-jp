---
title: "Azure で Linux 仮想マシンの負荷分散を行う方法 | Microsoft Docs"
description: "Azure Load Balancer を使用して、可用性が高く堅牢なアプリケーションを 3 つの Linux VM にまたがって作成する方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 105ff3614a05926d2bc2f236837bb4d5d95fcf32
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法
このチュートリアルでは、トラフィックを分散し高可用性を提供する、Azure Load Balancer のさまざまなコンポーネントについて説明します。 ロード バランサーが動作していることを確認するには、3 つの Linux 仮想マシン (VM) 上で動作する Node.js アプリケーションを作成します。

このチュートリアルの手順は、最新バージョンの [Azure CLI 2.0](/cli/azure/install-azure-cli) を使用して行うことができます。


## <a name="azure-load-balancer-overview"></a>Azure Load Balancer の概要
Azure Load Balancer は、着信トラフィックを正常な VM に分散することで高可用性を実現する第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーの正常性プローブは、各 VM の特定のポートを監視し、稼働している VM にのみトラフィックを分散します。

1 つまたは複数のパブリック IP アドレスが含まれるフロントエンド IP 構成を定義します。 このフロントエンド IP 構成によって、ロード バランサーとアプリケーションにインターネット経由でアクセスできるようになります。 

仮想マシンは、仮想ネットワーク インターフェイス カード (NIC) を使用してロード バランサーに接続されます。 トラフィックを VM に分散するには、バックエンド アドレス プールに、ロード バランサーに接続される仮想 NIC の IP アドレスを含めます。

トラフィックのフローを制御するには、VM にマップする特定のポートとプロトコルについて、ロード バランサー規則を定義します。

前のチュートリアルに従って[仮想マシン スケール セットを作成](tutorial-create-vmss.md)した場合は、ロード バランサーが自動的に作成されています。 これらすべてのコンポーネントが、スケール セットの一部として構成されています。


## <a name="create-azure-load-balancer"></a>Azure Load Balancer を作成する
このセクションでは、ロード バランサーの各コンポーネントを作成および設定する方法について説明します。 ロード バランサーを作成する前に、[az group create](/cli/azure/group#create) を使用してリソース グループを作成します。 次の例では、`myRGLoadBalancer` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myRGLoadBalancer --location westus
```

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネット上のアプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 パブリック IP アドレスは、[az network public-ip create](/cli/azure/public-ip#create) で作成します。 次の例では、`myRGLoadBalancer` リソース グループに `myPublicIP` という名前のパブリック IP アドレスを作成します。

```azurecli
az network public-ip create --resource-group myRGLoadBalancer --name myPublicIP
```

### <a name="create-a-load-balancer"></a>ロード バランサーの作成
ロード バランサーは、[az network lb create](/cli/azure/network/lb#create) で作成します。 次の例では、`myLoadBalancer` という名前のロード バランサーを作成し、`myPublicIP` アドレスをフロントエンド IP 構成に割り当てます。

```azurecli
az network lb create \
    --resource-group myRGLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>正常性プローブの作成
ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 既定では、15 秒間隔のチェックが 2 回連続でエラーになった VM はロード バランサーのローテーションから外されます。 正常性プローブは、プロトコルに基づいて作成するか、またはアプリの特定の正常性チェック ページに基づいて作成します。 

次の例では、TCP プローブを作成します。 よりきめ細やかな正常性チェックを行う場合は、カスタムの HTTP プローブを作成することもできます。 カスタム HTTP プローブを使用する場合は、`healthcheck.js` などの正常性チェック ページを作成する必要があります。 対象となるホストをロード バランサーのローテーションに維持するには、プローブが **HTTP 200 OK** 応答を返す必要があります。

TCP 正常性プローブを作成するには、[az network lb probe create](/cli/azure/network/lb/probe#create) を使用します。 次の例では、`myHealthProbe` という名前の正常性プローブを作成します。

```azurecli
az network lb probe create \
    --resource-group myRGLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成
ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 正常な VM のみでトラフィックを受信するには、使用する正常性プローブの定義も行います。

ロード バランサー規則は、[az network lb rule create](/cli/azure/network/lb/rule#create) で作成します。 次の例では、`myLoadBalancerRule` という名前の規則を作成しています。正常性プローブ `myHealthProbe` を使用し、ポート `80` でトラフィックを負荷分散します。

```azurecli
az network lb rule create \
    --resource-group myRGLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>仮想ネットワークを構成する
一部の VM をデプロイしてバランサーをテストする前に、関連する仮想ネットワーク リソースを作成します。 仮想ネットワークの詳細については、[Azure 仮想ネットワークの管理](tutorial-virtual-network.md)に関するチュートリアルを参照してください。

### <a name="create-network-resources"></a>ネットワーク リソースを作成する
[az network vnet create](/cli/azure/vnet#create) を使用して仮想ネットワークを作成します。 次の例では、`myVnet` という名前の仮想ネットワークと `mySubnet` という名前のサブネットを作成しています。

```azurecli
az network vnet create --resource-group myRGLoadBalancer --name myVnet --subnet-name mySubnet
```

ネットワーク セキュリティ グループを追加するには、[az network nsg create](/cli/azure/network/nsg#create) を使用します。 次の例では、`myNetworkSecurityGroup` という名前のネットワーク セキュリティ グループを作成します。

```azurecli
az network nsg create --resource-group myRGLoadBalancer --name myNetworkSecurityGroup
```

[az network nsg rule create](/cli/azure/network/nsg/rule#create) を使用してネットワーク セキュリティ グループの規則を作成します。 次の例では、`myNetworkSecurityGroupRule` という名前のネットワーク セキュリティ グループの規則を作成しています。

```azurecli
az network nsg rule create \
    --resource-group myRGLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

仮想 NIC は、[az network nic create](/cli/azure/network/nic#create) を使用して作成します。 以下の例では、3 つの仮想 NIC を作成します  (以降の手順では、アプリ用に作成する VM ごとに仮想 NIC を 1 つ)。 いつでも追加の仮想 NIC と VM を作成してロード バランサーに追加することができます。

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myRGLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

### <a name="create-cloud-init-config"></a>cloud-init 構成を作成する
[Linux 仮想マシンを初回起動時にカスタマイズする方法](tutorial-automate-vm-deployment.md)に関する先行のチュートリアルで、cloud-init を使用して VM のカスタマイズを自動化する方法を学習しました。 同じ cloud-init 構成ファイルを使用して、NGINX をインストールし、単純な "Hello World" Node.js アプリを実行することができます。 `cloud-init.txt` というファイルを作成し、次の構成を貼り付けます。

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

### <a name="create-virtual-machines"></a>仮想マシンを作成する
アプリの高可用性を高めるには、可用性セットに VM を配置します。 可用性セットの詳細については、前の[高可用性仮想マシンを作成する方法](tutorial-availability-sets.md)に関するチュートリアルを参照してください。

可用性セットを作成するには、[az vm availability-set create](/cli/azure/vm/availability-set#create) を使用します。 次の例では、`myAvailabilitySet` という名前の可用性セットを作成します。

```azurecli
az vm availability-set create \
    --resource-group myRGLoadBalancer \
    --name myAvailabilitySet \
    --platform-fault-domain-count 3 \
    --platform-update-domain-count 2
```

これで、[az vm create](/cli/azure/vm#create) を使用して VM を作成できるようになりました。 次の例では、3 台の VM を作成し、SSH キーを生成します (まだ存在していない場合)。

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myRGLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image Canonical:UbuntuServer:14.04.4-LTS:latest \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

3 台の VM をすべて作成して構成するには、数分かかります。 それぞれの VM でアプリがいつ実行されるかは、ロード バランサーの正常性プローブによって自動的に検出されます。 アプリが実行状態になると、ロード バランサー規則によってトラフィックの負荷分散が開始されます。


## <a name="test-load-balancer"></a>ロード バランサーをテストする
ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#show) を使用します。 次の例では、先ほど作成した `myPublicIP` の IP アドレスを取得しています。

```azurecli
az network public-ip show \
    --resource-group myRGLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力できます。 次の例のように、アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/tutorial-load-balancer/running-nodejs-app.png)

アプリを実行している 3 つの VM すべての間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。


## <a name="add-and-remove-vms"></a>仮想マシンの追加と削除
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、ロード バランサーから VM を除外したりロード バランサーに対して VM を追加したりする方法について説明します。

### <a name="remove-a-vm-from-the-load-balancer"></a>ロード バランサーから VM を除外する
バックエンド アドレス プールから VM を削除するには、[az network nic ip-config address-pool remove](/cli/azure/network/nic/ip-config/address-pool#remove) を使用できます。 次の例では、**myVM2** の仮想 NIC を `myLoadBalancer` から削除しています。

```azurecli
az network nic ip-config address-pool remove \
    --resource-group myRGLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

アプリを実行している残りの 2 つの VM の間で、ロード バランサーがトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。 これで VM に対して、OS 更新プログラムのインストールや VM の再起動などのメンテナンスを行うことができます。

### <a name="add-a-vm-to-the-load-balancer"></a>ロード バランサーに VM を追加する
VM のメンテナンスを実施した後や、処理能力の引き上げが必要となった場合は、[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#add) でバックエンド アドレス プールに VM を追加できます。 次の例では、**myVM2** の仮想 NIC を `myLoadBalancer` に追加しています。

```azurecli
az network nic ip-config address-pool add \
    --resource-group myRGLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシンのロード バランサーの作成について説明しました。 次のチュートリアルに進み、Azure 仮想ネットワークのコンポーネントの詳細について学習してください。

[VM ネットワークの管理](tutorial-virtual-network.md)

