---
title: CLI を使用して Azure スポット VM をデプロイする (プレビュー)
description: CLI を使用して Azure スポット VM をデプロイし、コストを節約する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 0635be14937a3688792f65208dcb9d482b9e6d44
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781851"
---
# <a name="preview-deploy-spot-vms-using-the-azure-cli"></a>プレビュー:Azure CLI を使用してスポット VM をデプロイする

[Azure スポット VM](spot-vms.md) を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット VM を削除します。 したがって、スポット VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

スポット VM の価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 スポット VM の最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。 最大価格の設定の詳細については、[スポット VM の価格](spot-vms.md#pricing)に関するページを参照してください。

Azure CLI を使用してスポットで VM を作成するプロセスは、[クイックスタートの記事](/azure/virtual-machines/linux/quick-create-cli)で詳しく説明されている内容と同じです。 単純に '--priority Spot' パラメーターを追加し、最大価格または `-1` を指定してください。

> [!IMPORTANT]
> スポット インスタンスは現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンは運用環境のワークロードにはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> パブリック プレビューの初期段階では、スポット インスタンスに固定価格が設定されているため、価格ベースの削除は行われません。


## <a name="install-azure-cli"></a>Azure CLI のインストール

スポット VM を作成するには、Azure CLI バージョン 2.0.74 以降が実行されている必要があります。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>スポット VM を作成する

この例では、価格に基づいて削除されない Linux スポット VM をデプロイする方法を示しています。 

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1
```

VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大請求価格を確認できます。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**次のステップ**

[Azure PowerShell](../windows/spot-powershell.md) または[テンプレート](spot-template.md)を使って、スポット VM を作成することもできます。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。
