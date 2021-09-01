---
title: ゲートウェイ接続を確認する
titleSuffix: Azure VPN Gateway
description: 仮想ネットワーク VPN Gateway の接続を確認する方法を説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: eaeb77e1dafe7b2791537190d0b4d220363280a8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729472"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 接続の確認

この記事では、クラシック デプロイ モデルと [Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)の両方での VPN ゲートウェイ接続を確認する方法について説明します。

## <a name="azure-portal"></a>Azure portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

PowerShell を使用して Resource Manager デプロイ モデルでの VPN ゲートウェイ接続を確認するには、[Azure Resource Manager PowerShell コマンドレット](/powershell/azure/)の最新バージョンをインストールします。

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Azure CLI を使用して Resource Manager デプロイ モデルでの VPN ゲートウェイ接続を確認するには、[CLI コマンドレット](/cli/azure/install-azure-cli)の最新バージョン (2.0 以降) をインストールします。

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Azure Portal (クラシック)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (クラシック)

PowerShell を使用してクラシック デプロイ モデルでの VPN ゲートウェイ接続を確認するには、Azure PowerShell コマンドレットの最新バージョンをインストールします。 [Service Management](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) モジュールをダウンロードし、インストールしてください。 'Add-AzureAccount' を使用して、クラシック デプロイ モデルにログインします。

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>次のステップ

* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/windows/quick-create-portal.md) に関するページを参照してください。