---
title: パブリック IP アドレスをアップグレードする - Azure CLI
description: この記事では、Azure CLI を使用して Basic SKU のパブリック IP アドレスをアップグレードする方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: ad9310dfe05f9639f1448d60d545d2f29c89ca98
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233982"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-cli"></a>Azure CLI を使用してパブリック IP アドレスをアップグレードする

Azure パブリック IP アドレスは Basic または Standard のいずれかの SKU で作成されます。 SKU によって、割り当て方法、機能サポート、関連付けることができるリソースなどの機能が決まります。 

この記事では、Azure CLI を使用して、静的な Basic SKU のパブリック IP アドレスを Standard SKU にアップグレードする方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* サブスクリプション内の **静的な** Basic SKU のパブリック IP アドレス。 詳細については、[パブリック IP アドレスの作成 - Azure portal](./create-public-ip-portal.md#create-a-basic-sku-public-ip-address) に関するページを参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- このチュートリアルには、Azure CLI のバージョン 2.0.28 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="upgrade-public-ip-address"></a>パブリック IP アドレスのアップグレード

このセクションでは、Azure CLI を使用し、静的な Basic SKU のパブリック IP を Standard SKU にアップグレードします。

```azurecli-interactive
az network public-ip update \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard

```
> [!NOTE]
> アップグレードする Basic パブリック IP には、静的割り当ての種類が必要です。 動的に割り当てられた IP アドレスをアップグレードしようとすると、IP をアップグレードできないという警告が表示されます。

> [!WARNING]
> Basic パブリック IP を Standard SKU にアップグレードすると、元に戻すことはできません。 Basic から Standard SKU にアップグレードされたパブリック IP には引き続き、[可用性ゾーン](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)の保証はありません。

## <a name="verify-upgrade"></a>アップグレードの確認

このセクションでは、パブリック IP アドレスが Standard SKU になっていることを確認します。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myBasicPublicIP \
  --query sku \
  --output tsv

```
コマンドに **Standard** と表示されるはずです。

## <a name="next-steps"></a>次のステップ

この記事では、Basic SKU のパブリック IP アドレスを Standard SKU にアップグレードしました。

Azure のパブリック IP アドレスに関する詳細については、次を参照してください。

- [Azure でのパブリック IP アドレス](public-ip-addresses.md)
- [パブリック IP の作成 - Azure portal](./create-public-ip-portal.md)