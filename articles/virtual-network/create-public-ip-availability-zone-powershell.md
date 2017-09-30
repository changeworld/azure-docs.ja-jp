---
title: "PowerShell を使用してゾーン パブリック IP アドレスを作成する |Microsoft Docs"
description: "PowerShell を使用して可用性ゾーン内にパブリック IP を作成します。"
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 701a8febb3f897fd7f1a81a00adb4635df153d3a
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>PowerShell を使用して可用性ゾーン内にパブリック IP アドレスを作成する

Azure 可用性ゾーン (プレビュー) 内にパブリック IP アドレスをデプロイできます。 可用性ゾーンとは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 以下の項目について説明します。

> * 可用性ゾーンにパブリック IP アドレスを作成する方法
> * 可用性ゾーンに作成された関連リソースを特定する方法
  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

この記事では、バージョン 4.4.0 以上の AzureRM モジュールがインストールされている必要があります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 インストールまたはアップグレードの必要がある場合は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM)から最新バージョンの AzureRM モジュールをインストールします。

> [!NOTE]
> 可用性ゾーンはプレビュー段階であり、開発とテストのシナリオで使用できます。 一部の Azure リソース、リージョン、VM サイズ ファミリについては、サポートを利用できます。 利用を開始する方法のほか、可用性ゾーンを試すことができる Azure リソース、リージョン、VM サイズ ファミリについては、[可用性ゾーンの概要](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)に関するページを参照してください。 サポートが必要な場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) を参照するか、[Azure サポート チケットを発行](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)してください。

## <a name="log-in-to-azure"></a>Azure へのログイン

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Create resource group

[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*myResourceGroup* という名前のリソース グループが *westeurope* リージョンに作成されます。 *westeurope* は、可用性グループ (プレビュー段階) をサポートする Azure リージョンの 1 つです。

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>ゾーンのパブリック IP アドレスの作成

次のコマンドを使用して、可用性ゾーン内にパブリック IP アドレスを作成します。

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> 標準 SKU のパブリック IP アドレスを仮想マシンのネットワーク インターフェイスに割り当てるときは、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)を利用し、特定のトラフィックを明示的に許可する必要があります。 ネットワーク セキュリティ グループを作成して関連付け、特定のトラフィックを明示的に許可するまでは、リソースと通信できません。

## <a name="get-zone-information-about-a-public-ip-address"></a>パブリック IP アドレスについてのゾーン情報の取得

次のコマンドを使用して、パブリック IP アドレスのゾーン情報を取得します。

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>次のステップ

- [可用性ゾーン](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)について詳しく学ぶ
- [パブリック IP アドレス](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json)について詳しく学ぶ 
