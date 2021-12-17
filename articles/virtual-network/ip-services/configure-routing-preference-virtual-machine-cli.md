---
title: 'チュートリアル: VM 用にルーティング優先設定を構成する - Azure CLI'
description: このチュートリアルでは、Azure CLI を使用して、ルーティング優先設定を選択してパブリック IP アドレスを持つ VM を作成する方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369412"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>チュートリアル: Azure CLI を使用して VM 用にルーティング優先設定を構成する
このチュートリアルでは、仮想マシン用にルーティング優先設定を構成する方法について説明します。 ルーティング優先設定オプションとして **[インターネット]** を選択すると、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * **インターネット** ルーティング優先設定用に構成されたパブリック IP アドレスを作成します。
> * 仮想マシンを作成します。
> * パブリック IP アドレスが **インターネット** ルーティング優先設定に設定されていることを確認します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

[az group create](/cli/azure/group#az_group_create) を使用して、**TutorVMRoutePref-rg** という名前のリソース グループを場所 **westus2** に作成します。

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

[az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) を使用して、**TutorVMRoutePref-rg** に **myPublicIP** という Standard ゾーン冗長パブリック IPv4 アドレスを作成します。 CLI コマンドで、**インターネット** の **タグ** は、パラメーターとしてパブリック IP アドレスに適用され、**インターネット** ルーティング優先設定が有効になります。

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm#az_vm_create) を使用して仮想マシンを作成します。 前のセクションで作成したパブリック IP アドレスは CLI コマンドの一部として追加され、作成時に VM にアタッチされます。

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>インターネット ルーティング優先設定の確認

[az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) を使用して、パブリック IP アドレスに **インターネット** ルーティング優先設定が構成されていることを確認します。

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想マシンとパブリック IP プレフィックスを使い終えたら、[az group delete](/cli/azure/group#az_group_delete) を使用してリソース グループとそれに含まれるすべてのリソースを削除します。

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>次のステップ

次の記事に進み、ルーティング優先設定を組み合わせて仮想マシンを作成する方法について説明します。
> [!div class="nextstepaction"]
> [仮想マシンの両方のルーティング優先設定オプションを構成する](routing-preference-mixed-network-adapter-portal.md)

