---
title: 'チュートリアル: Azure Kubernetes サービスのルーティング優先順位を構成する - Azure CLI'
titlesuffix: Azure virtual network
description: このチュートリアルでは、Azure Kubernetes サービスのルーティング優先順位を構成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369196"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>チュートリアル: Azure Kubernetes サービスのルーティング優先順位を Azure CLI を使用して構成する

この記事では、Azure CLI を使用して ISP ネットワーク ( **[インターネット]** オプション) を介して Kubernetes クラスターのルーティング設定を構成する方法について説明します。 ルーティング設定は、ルーティング設定の種類 "**インターネット**" のパブリック IP アドレスを作成し、AKS クラスターの作成中にそれを使用することによって設定されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **インターネット** ルーティング優先順位を使用してパブリック IP アドレスを作成する。
> * **インターネット** ルーティング優先順位を設定したパブリック IP を使用して Azure Kubernetes クラスターを作成する。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- この記事では、Azure CLI のバージョン 2.0.49 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group#az_group_create) コマンドを使用して、リソース グループを作成します。 次の例では、Azure の **米国東部** リージョンにリソース グループを作成します。

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>インターネット ルーティング優先順位を設定してパブリック IP アドレスを作成する

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) コマンドを使用し、ルーティング優先順位の種類に **インターネット** を設定してパブリック IP アドレスを作成します。

次のコマンドを実行すると、**米国東部** Azure リージョンに **インターネット** をルーティング優先設定にして新しいパブリック IP アドレスが作成されます。

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  現在、ルーティング優先設定では IPV4 パブリック IP アドレスのみがサポートされています。

## <a name="create-kubernetes-cluster-with-public-ip"></a>パブリック IP を使用して Kubernetes クラスターを作成する

先ほど作成したパブリック IP の ID を変数に格納し、後で使用できるようにします。 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) を使用して、パブリック IP ID を取得します。

以下のコマンドを実行してパブリック IP ID を取得し、その次のコマンドで使用する変数に格納します。

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

[az aks create](/cli/azure/aks#az_aks_create) を使用して、Kubernetes クラスターを作成します。

以下のコマンドを実行すると、Kubernetes クラスターが作成され、前の手順で作成したパブリック IP の変数が使用されます。

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>AKS クラスターをデプロイするには数分かかります。

検証するには、Azure portal で、前の手順で作成したパブリック IP を検索します。 パブリック IP アドレスは、ロード バランサーに関連付けられています。 ロード バランサーは、次のように Kubernetes クラスターに関連付けられています。

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="AKS クラスターのパブリック IP アドレスのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az_group_delete) コマンドを使用して、リソース グループ、パブリック IP、AKS クラスター、およびすべての関連リソースを削除します。

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>次のステップ

次の記事に進み、ルーティング優先順位を混合設定して仮想マシンを作成する方法について学習してください。
> [!div class="nextstepaction"]
> [仮想マシンの両方のルーティング優先設定オプションを構成する](routing-preference-mixed-network-adapter-portal.md)