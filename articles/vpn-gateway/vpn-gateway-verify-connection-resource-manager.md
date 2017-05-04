---
title: "VPN Gateway 接続の確認 | Microsoft Docs"
description: "この記事では、仮想ネットワーク VPN Gateway の接続を確認する方法を説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: a67ea630cc8b5a3e9deab7733aa5cd2055949ec0
ms.lasthandoff: 04/27/2017


---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway 接続の確認

この記事では、Resource Manager とクラシック両方のデプロイメント モデルでの VPN ゲートウェイ接続を確認する方法について説明します。

## <a name="verify-using-the-azure-portal"></a>Azure Portal を使用した確認

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="verify-using-powershell"></a>PowerShell を使用した確認

PowerShell を使用して確認を行うには、Azure Resource Manager PowerShell コマンドレットの最新版をインストールします。 PowerShell コマンドレットのインストールについて詳しくは、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。 Resource Manager コマンドレットの使用方法の詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページをご覧ください。

### <a name="log-in-to-your-azure-account"></a>Azure アカウントにログインする
1. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。

  ```powershell
  Login-AzureRmAccount
  ```
2. アカウントのサブスクリプションを確認します。

  ```powershell
  Get-AzureRmSubscription
  ``` 
3. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

### <a name="verify-your-connection"></a>接続を確認する

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="verify-using-the-azure-cli"></a>Azure CLI を使用した確認

Azure CLI を使用して確認するには、最新バージョンの CLI コマンド (2.0 以降) をインストールします。 CLI コマンドのインストール方法については、「[Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)」(Azure CLI 2.0 のインストール) を参照してください。

### <a name="log-in-to-your-azure-account"></a>Azure アカウントへのログイン

1. [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

  ```azurecli
  az login
  ```
2. 複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションが一覧表示されます。

  ```azurecli
  Az account list --all
  ```
3. 使用するサブスクリプションを指定します。

  ```azurecli
  Az account set --subscription
  <replace_with_your_subscription_id>
  ```

### <a name="verify-your-connection"></a>接続を確認する

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="verify-using-the-azure-portal-classic"></a>Azure Portal を使用した確認 (クラシック)
[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


## <a name="verify-using-powershell-classic"></a>PowerShell を使用した確認 (クラシック)
PowerShell を使用して確認を行うには、Azure PowerShell コマンドレットの最新版をインストールします。 Resouce Manager と Service Management (SM) の両方のバージョンをダウンロードしてインストールしてください。 PowerShell コマンドレットのインストールについて詳しくは、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。 

### <a name="log-in-to-your-azure-account"></a>Azure アカウントにログインする
1. 昇格された特権で PowerShell コンソールを開き、アカウントに接続します。

  ```powershell
  Login-AzureRmAccount
  ```
2. アカウントのサブスクリプションを確認します。

  ```powershell
  Get-AzureRmSubscription 
  ```
3. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
4. ログインして、クラシック デプロイメント モデル向けの Service Management コマンドレットを使用します。

  ```powershell
  Add-AzureAccount
  ```

### <a name="verify-your-connection"></a>接続を確認する
[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>次のステップ
* 仮想ネットワークに仮想マシンを追加できます。 手順については、 [仮想マシンの作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) に関するページを参照してください。


