---
title: クイック スタート - Azure Application Gateway による Web トラフィックのルーティング - Azure CLI | Microsoft Docs
description: Azure CLI を使用して、Web トラフィックをバックエンド プール内の仮想マシンにルーティングする Azure Application Gateway を作成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0ba18b1ef0ba6c0a73759577c83ab80550baa6f8
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55754746"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-cli"></a>クイック スタート:Azure Application Gateway による Web トラフィックの転送 - Azure CLI

このクイック スタートでは、Azure CLI を使用して、そのバックエンド プール内の 2 つの仮想マシンで、アプリケーション ゲートウェイをすばやく作成する方法を示します。 さらに、それをテストし、正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.4 以降を実行してください。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 [az group create](/cli/azure/group#az-group-create) を使用してリソース グループを作成します。 

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する 

仮想ネットワークを作成すると、アプリケーション ゲートウェイが他のリソースと通信できるようになります。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。 この例では、2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つは仮想マシン用です。 アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。

仮想ネットワークとサブネットを作成するには、[az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用します。 パブリック IP アドレスを作成するには、[az network public-ip create](/cli/azure/network/public-ip) を実行します。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet   \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、Azure がアプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 

### <a name="create-two-virtual-machines"></a>2 つの仮想マシンの作成

アプリケーション ゲートウェイが正常に作成されたことを確認するには、仮想マシンに [NGINX Web サーバー](https://docs.nginx.com/nginx/)をインストールします。 cloud-init 構成ファイルを使って、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行することができます。 cloud-init の詳細については、「[Azure での仮想マシンに対する cloud-init のサポート](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)」を参照してください。

Azure Cloud Shell で、次の構成をコピーして、*cloud-init.txt* という名前のファイルに貼り付けます。 「*editor cloud-init.txt*」と入力してファイルを作成します。

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

[az network nic create](/cli/azure/network/nic#az-network-nic-create) で、ネットワーク インターフェイスを作成します。 仮想マシンを作成するには、[az vm create](/cli/azure/vm#az-vm-create) を使用します。

```azurecli-interactive
for i in `seq 1 2`; do
  az network nic create \
    --resource-group myResourceGroupAG \
    --name myNic$i \
    --vnet-name myVNet \
    --subnet myBackendSubnet
  az vm create \
    --resource-group myResourceGroupAG \
    --name myVM$i \
    --nics myNic$i \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
done
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、アプリケーション ゲートウェイを作成します。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 すると、Azure により、ネットワーク インターフェイスのプライベート IP アドレスが、アプリケーション ゲートウェイのバックエンド プールにサーバーとして追加されます。

```azurecli-interactive
address1=$(az network nic show --name myNic1 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
address2=$(az network nic show --name myNic2 --resource-group myResourceGroupAG | grep "\"privateIpAddress\":" | grep -oE '[^ ]+$' | tr -d '",')
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Enabled \
  --public-ip-address myAGPublicIPAddress \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --servers "$address1" "$address2"
```

Azure によってアプリケーション ゲートウェイが作成されるのに最大 30 分かかる場合があります。 作成後は、**[アプリケーション ゲートウェイ]** ページの **[設定]** セクションで次の設定を確認できます。

- **appGatewayBackendPool**:**[バックエンド プール]** ページにあります。 これは、必要なバックエンド プールを指定します。
- **appGatewayBackendHttpSettings**:**[HTTP 設定]** ページにあります。 これは、アプリケーション ゲートウェイがポート 80 を使用すること、および通信に HTTP プロトコルを使用することを指定します。
- **appGatewayHttpListener**:**[リスナー] ページ**にあります。 これは、**appGatewayBackendPool** に関連付けられている既定のリスナーを指定します。
- **appGatewayFrontendIP**:**[フロントエンド IP 構成]** ページにあります。 これは、*myAGPublicIPAddress* を **appGatewayHttpListener** に割り当てます。
- **rule1**:**[ルール]** ページにあります。 **appGatewayHttpListener** に関連付けられている既定のルーティング規則を指定します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

Azure はアプリケーション ゲートウェイを作成するために NGINX Web サーバーを必要としませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するために、NGINX をインストールしました。 新しいアプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
``` 

そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。
    
![アプリケーション ゲートウェイのテスト](./media/quick-create-cli/application-gateway-nginxtest.png)

ブラウザーを更新すると、2 番目の VM の名前が表示されるはずです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用してリソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。

```azurecli-interactive 
az group delete --name myResourceGroupAG
```
 
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure CLI を使用してアプリケーション ゲートウェイで Web トラフィックを管理する](./tutorial-manage-web-traffic-cli.md)

