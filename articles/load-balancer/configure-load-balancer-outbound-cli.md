---
title: Azure CLI を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する
titlesuffix: Azure Load Balancer
description: この記事では、Azure CLI を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: 97a7e09994ab93eb75881a2eae2b49821ca8289f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085010"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Azure CLI を使用して Standard Load Balancer の負荷分散規則とアウトバウンド規則を構成する

このクイック スタートでは、Azure CLI を使用して Standard Load Balancer のアウトバウンド規則を構成する方法について説明します。  

作業が完了すると、Load Balancer リソースに 2 つのフロントエンドとそれらに関連付けられた規則 (1 つは受信用、もう 1 つは送信用) が含まれます。  各フロントエンドはパブリック IP アドレスへの参照を持ちます。このシナリオでは、受信と送信に異なるパブリック IP アドレスを使用します。   負荷分散規則では受信負荷分散のみを提供し、アウトバウンド規則では VM に提供される送信 NAT を制御します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.28 以降のバージョンを実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。

## <a name="create-resource-group"></a>リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group#create) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myresourcegroupoutbound* という名前のリソース グループを *eastus2* に作成します。

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Create virtual network
[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) を使用して、*myresourcegroupoutbound* に *mysubnetoutbound* という名前のサブネットがある *myvnetoutbound* という名前の仮想ネットワークを作成します。

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>受信パブリック IP アドレスの作成 

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) を使用して、*myresourcegroupoutbound* に *mypublicipinbound* という名前の Standard パブリック IP アドレスを作成します。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>送信パブリック IP アドレスの作成 

[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) を使用して、Load Balancer のフロントエンド送信構成の Standard IP アドレスを作成します。

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```


## <a name="create-azure-load-balancer"></a>Azure Load Balancer の作成

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。
  - ロード バランサーの受信ネットワーク トラフィックを受け取るフロントエンド IP。
  - フロントエンド IP から負荷分散されたネットワーク トラフィックが送信されるバックエンド プール。
  - バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  - VM へのトラフィックの分散方法を定義する、ロード バランサーの受信規則。
  - VM からのトラフィックの分散方法を定義する、ロード バランサーのアウトバウンド規則。

### <a name="create-load-balancer"></a>Load Balancer の作成

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) を使用して、受信 IP アドレスを持つ *lb* という名前の Load Balancer を作成します。これには、受信フロントエンド IP 構成と、前の手順で作成したパブリック IP アドレス *mypublicipinbound* に関連付けられているバックエンド プールを含めます。

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepool \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-frontend-ip"></a>送信フロントエンド IP の作成
[az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create) を使用して、Load Balancer の送信フロントエンド IP 構成を作成します。これには、パブリック IP アドレス *mypublicipoutbound* に関連付けられている *myfrontendoutbound* という名前の送信フロントエンド IP 構成を含めます。

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>正常性プローブの作成

正常性プローブは、すべての仮想マシン インスタンスを確認し、ネットワーク トラフィックを送信できるかどうかを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>負荷分散規則の作成

ロード バランサー規則では、必要な発信元ポートおよび宛先ポートと共に、受信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド プールを定義します。 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create) を使用してロード バランサー規則 *myinboundlbrule* を作成します。この規則では、フロントエンド プール *myfrontendinbound* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *bepool* に送信します。 

>[!NOTE]
>この負荷分散規則では、--disable-outbound-snat パラメーターを指定するため、自動送信 (S)NAT が無効になります。 送信 NAT はアウトバウンド規則によってのみ提供されます。

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepool \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>アウトバウンド規則の作成

アウトバウンド規則では、フロントエンド *myfrontendoutbound* で表されるフロントエンド パブリック IP を定義します。これは、この規則が適用されるすべての送信 NAT トラフィックとバックエンド プールに使用されます。  バックエンド プール *bepool* 内のすべての仮想マシン (NIC IP 構成) の送信ネットワーク変換用のアウトバウンド規則 *myoutboundrule* を作成します。  また、次のコマンドでは、送信アイドル タイムアウトを 4 分から 15 分に変更し、1024 個ではなく 10000 個の SNAT ポートを割り当てます。  詳細については、[アウトバウンド規則](https://aka.ms/lboutboundrules)に関する記事をご覧ください。

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepool
```

この時点で、各 NIC リソースの IP 構成を更新して、バックエンド プール *bepool* に VM を追加できるようになります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除できます。

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>次の手順
この記事では、Standard Load Balancer の作成、ロード バランサーの受信トラフィック規則の構成、バックエンド プール内の VM の正常性プローブの構成を行いました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

