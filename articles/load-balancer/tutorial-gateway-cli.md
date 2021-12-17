---
title: 'チュートリアル: ゲートウェイ ロード バランサーを作成する - Azure CLI'
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure CLI を使用して、ゲートウェイ ロード バランサーを作成する方法を学習します。
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 11/02/2021
ms.custom: template-tutorial, ignite-fall-2021
ms.openlocfilehash: 13435307985f3471800a6bfc3697c7bc0fc58ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092316"
---
# <a name="tutorial-create-a-gateway-load-balancer-using-the-azure-cli"></a>チュートリアル: Azure CLI を使用してゲートウェイ ロード バランサーを作成する

Azure Load Balancer は、Standard、Basic、および Gateway SKU で構成されます。 Azure Gateway Load Balancer は、ネットワーク仮想アプライアンス (NVA) の透過的な挿入に使用されます。 ハイ パフォーマンスと NVA の高いスケーラビリティを必要とするシナリオには、Azure Gateway Load Balancer を使用します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * プレビュー機能を登録します。
> * 仮想ネットワークを作成します。
> * ネットワーク セキュリティ グループを作成します。
> * ゲートウェイ ロード バランサーを作成します。
> * ロード バランサー フロントエンドをゲートウェイ ロード バランサーにチェーンします。

> [!IMPORTANT]
> Azure Gateway Load Balancer は、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

- アクティブなサブスクリプションを持つ Azure アカウント - [アカウントを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 既存のパブリック Standard SKU Azure Load Balancer。 ロード バランサーの作成の詳細については、 **[Azure CLI を使用したパブリック ロード バランサーの作成](quickstart-load-balancer-standard-public-cli.md)** に関する記事を参照してください。
    - このチュートリアルの目的のため、例の既存のロード バランサーの名前は **myLoadBalancer** です。

## <a name="register-preview-feature"></a>プレビュー機能を登録する

ゲートウェイ ロード バランサーのパブリック プレビューの一環として、プロバイダーを Azure サブスクリプションに登録する必要があります。

次のように [az feature register](/cli/azure/feature#az_feature_register) を使用して、**AllowGatewayLoadBalancer** プロバイダー機能を登録します。

```azurecli-interactive
  az feature register \
    --name AllowGatewayLoadBalancer \
    --namespace Microsoft.Network
```

次のように [az provider register](/cli/azure/provider#az_provider_register) を使用して、**Microsoft.Network** リソース プロバイダーを登録します。

```azurecli-interactive
  az provider register \
    --namespace Microsoft.Network
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、次のようにリソース グループを作成します。

```azurecli-interactive
  az group create \
    --name TutorGwLB-rg \
    --location eastus

```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

ゲートウェイ ロード バランサーのバックエンド プールにあるリソースには、仮想ネットワークが必要です。  

### <a name="create-virtual-network"></a>Create virtual network

[az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) を使用して、仮想ネットワークを作成します。

```azurecli-interactive
  az network vnet create \
    --resource-group TutorGwLB-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-bastion-public-ip-address"></a>bastion パブリック IP アドレスを作成する

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、Azure Bastion ホストのパブリック IP アドレスを作成します

```azurecli-interactive
az network public-ip create \
    --resource-group TutorGwLB-rg \
    --name myBastionIP \
    --sku Standard \
    --zone 1 2 3
```

### <a name="create-bastion-subnet"></a>bastion サブネットを作成する

[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) を使用して、bastion サブネットを作成します。

```azurecli-interactive
az network vnet subnet create \
    --resource-group TutorGwLB-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/27
```

### <a name="create-bastion-host"></a>bastion ホストを作成する

[az network bastion create](/cli/azure/network/bastion#az_network_bastion_create) を使用して、仮想ネットワーク内のリソースを安全に管理するための bastion ホストをデプロイします。

```azurecli-interactive
az network bastion create \
    --resource-group TutorGwLB-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Azure Bastion ホストがデプロイされるまでに数分かかる場合があります。

## <a name="configure-nsg"></a>NSG を構成する

次の例を使用して、ネットワーク セキュリティ グループを作成します。 前に作成した仮想ネットワーク内のネットワーク トラフィックに、必要な NSG ルールを構成します。

### <a name="create-nsg"></a>Create NSG

[az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) を使用して、NSG を作成します。

```azurecli-interactive
  az network nsg create \
    --resource-group TutorGwLB-rg \
    --name myNSG
```

### <a name="create-nsg-rules"></a>NSG ルールを作成する

[az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) を使用して、その NSG に規則を追加します。

```azurecli-interactive
  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100

  az network nsg rule create \
    --resource-group TutorGwLB-rg \
    --nsg-name myNSG \
    --name myNSGRule-AllowAll-TCP-Out \
    --protocol 'TCP' \
    --direction outbound \
    --source-address-prefix '0.0.0.0/0' \
    --source-port-range '*' \
    --destination-address-prefix '0.0.0.0/0' \
    --destination-port-range '*' \
    --access allow \
    --priority 100
```

## <a name="configure-gateway-load-balancer"></a>Azure Gateway Load Balancer を構成する

このセクションでは、構成を作成し、ゲートウェイ ロード バランサーをデプロイします。  

### <a name="create-gateway-load-balancer"></a>Azure Gateway Load Balancer を作成する

[az network lb create](/cli/azure/network/lb#az_network_lb_create) を使用して、ロード バランサーを作成します。

```azurecli-interactive
  az network lb create \
    --resource-group TutorGwLB-rg \
    --name myLoadBalancer-gw \
    --sku Gateway \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --backend-pool-name myBackendPool \
    --frontend-ip-name myFrontEnd
```

### <a name="create-tunnel-interface"></a>トンネル インターフェイスを作成する

内部インターフェイスは、Azure CLI を使用して、**900** の **`--identifier`** と **10800** の **`--port`** で自動的に作成されます。

[az network lb address-pool tunnel-interface add](/cli/azure/network/lb/address-pool/tunnel-interface#az_network_lb_address_pool_tunnel_interface_add) を使用して、ロード バランサーの外部トンネル インターフェイスを作成します。 

```azurecli-interactive
  az network lb address-pool tunnel-interface add \
    --address-pool myBackEndPool \
    --identifier '901' \
    --lb-name myLoadBalancer-gw \
    --protocol VXLAN \
    --resource-group TutorGwLB-rg \
    --type External \
    --port '10801'
```

### <a name="create-health-probe"></a>正常性プローブの作成
ロード バランサー内のバックエンド インスタンスの正常性を監視するには、正常性プローブが必要です。 [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) を使用して、正常性プローブを作成します。

```azurecli-interactive
  az network lb probe create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myHealthProbe \
    --protocol http \
    --port 80 \
    --path '/' \
    --interval '5' \
    --threshold '2'
    
```

### <a name="create-load-balancing-rule"></a>負荷分散規則を作成する

バックエンド インスタンス宛てのトラフィックは、負荷分散規則を使用してルーティングされます。 [az network lb rule create](/cli/azure/network/lb/probe#az_network_lb_rule_create) を使用して、ロード バランサー規則を追加します。

```azurecli-interactive
  az network lb rule create \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myLBRule \
    --protocol All \
    --frontend-port 0 \
    --backend-port 0 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```

## <a name="add-network-virtual-appliances-to-the-gateway-load-balancer-backend-pool"></a>ネットワーク仮想アプライアンスをバックエンド プールの Azure Gateway Load Balancer に追加する
Azure Marketplace を介して NVA をデプロイします。 デプロイ後は、[az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az_network_nic_ip_config_address_pool_add) を使用して、次のように仮想マシンをバックエンド プールに追加します。

## <a name="chain-load-balancer-frontend-to-gateway-load-balancer"></a>ロード バランサー フロントエンドを Azure Gateway Load Balancer にチェーンします。

この例では、Standard ロード バランサーのフロントエンドをゲートウェイ ロード バランサーにチェーンします。 

サブスクリプション内の既存のロード バランサーのフロントエンド IP に、フロントエンドを追加します。

[az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_az_network_lb_frontend_ip_show) を使用して、ゲートウェイ ロード バランサー フロントエンドのリソース ID を変数に配置します。

[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) を使用して、ゲートウェイ ロード バランサー フロントエンドを既存のロード バランサーにチェーンします。

```azurecli-interactive
  feid=$(az network lb frontend-ip show \
    --resource-group TutorGwLB-rg \
    --lb-name myLoadBalancer-gw \
    --name myFrontend \
    --query id \
    --output tsv)

  az network lb frontend-ip update \
    --resource-group CreatePubLBQS-rg \
    --name myFrontendIP \
    --lb-name myLoadBalancer \
    --public-ip-address myPublicIP \
    --gateway-lb $feid

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、ロード バランサー、および残りの関連リソースを削除できます。

```azurecli-interactive
  az group delete \
    --name TutorGwLB-rg
```

## <a name="next-steps"></a>次のステップ

Azure でネットワーク仮想アプライアンスを作成します。 

NVA を作成する場合は、このチュートリアルで作成したリソースを選択します。

* 仮想ネットワーク

* Subnet

* ネットワーク セキュリティ グループ

* Azure Gateway Load Balancer

次の記事に進んで、リージョン間 Azure Load Balancer の作成方法を学習してください。
> [!div class="nextstepaction"]
> [リージョン間のロード バランサー](tutorial-cross-region-powershell.md)
