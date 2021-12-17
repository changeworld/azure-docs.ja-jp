---
title: Azure DNS 用の PowerShell のインクルード ファイル
description: Azure DNS 用の PowerShell のインクルード ファイル
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 04/28//2021
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS, devx-track-azurepowershell
ms.openlocfilehash: 2454ab0a95e8f69fb72e85b8fad083793e00735e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720490"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Azure DNS 用に Azure PowerShell をセットアップする

### <a name="before-you-begin"></a>始める前に

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

PowerShell コンソールを開き、アカウントに接続します。 詳細については、「[Azure PowerShell を使用してサインインする](/powershell/azure/azurerm/authenticate-azureps)」を参照してください。

```azurepowershell-interactive
Connect-AzAccount
```

### <a name="select-the-subscription"></a>サブスクリプションの選択
 
アカウントのサブスクリプションを確認します。

```azurepowershell-interactive
Get-AzSubscription
```

使用する Azure サブスクリプションを選択します。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Resource Manager では、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```azurepowershell-interactive
New-AzResourceGroup -Name MyDNSResourceGroup -location "West US"
```
