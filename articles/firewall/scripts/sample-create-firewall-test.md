---
title: Azure PowerShell サンプル スクリプト - Azure Firewall のテスト環境を作成する
description: Azure PowerShell サンプル スクリプト - Azure Firewall のテスト環境を作成します。
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: daf847ea9a6b6310afbc8eca7f662065fe75ade6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397184"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure Firewall のテスト環境を作成する

[!INCLUDE [firewall-preview-notice](../../../includes/firewall-preview-notice.md)]

Azure Firewall の記事の例では、Azure Firewall のパブリック プレビューを既に有効にしていることを前提としています。 詳細については、「[Enable the Azure Firewall public preview (Azure Firewall パブリック プレビューを有効にする)](../public-preview.md)」を参照してください。

このサンプル スクリプトでは、ファイアウォールとテスト ネットワーク環境を作成します。 ネットワークには、*AzureFirewallSubnet*、*ServersSubnet*、および *JumpboxSubnet* の 3 つのサブネットを含む 1 つの VNet があります。 ServersSubnet と JumpboxSubnet には、それぞれ 1 つの 2 コア Windows Server があります。

ファイアウォールは AzureFirewallSubnet にあり、www.microsoft.com へのアクセスを許可する単一のルールを含むアプリケーション ルール コレクションが構成されています。

ユーザー定義のルートが作成されます。このルートは、ファイアウォール規則が適用されるファイアウォールを経由する ServersSubnet からのネットワーク トラフィックを指します。

このスクリプトは、Azure [Cloud Shell](https://shell.azure.com/powershell) から、またはローカルの PowerShell インストールから実行できます。 

PowerShell をローカルで実行する場合、このスクリプトでは AzureRM PowerShell モジュール バージョン 6.4.0 以降が必要になります。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 

アップグレードが必要な場合は、Windows 10 および Windows Server 2016 に組み込まれている `PowerShellGet` を使用できます。

> [!NOTE]
>他の Windows バージョンでは、使用する前に `PowerShellGet` をインストールする必要があります。 `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` を実行すると、このツールがシステムにインストールされているかどうかを確認できます。 出力が空白の場合は、最新の [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) をインストールする必要があります。

詳細については、「[Install Azure PowerShell on Windows with PowerShellGet (PowerShellGet を使用した Windows への Azure PowerShell のインストール)](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)」を参照してください

Web Platform Installer を使用してインストールされた既存の Azure PowerShell は PowerShellGet のインストールと競合するため、削除する必要があります。

さらに、AzureRM.Network (バージョン 6.4.0) のプレビュー版をインストールする必要があります。 古いモジュールを使用している場合は、`Uninstall-Module AzureRM.Network -Force` を実行して削除してください。 次に、以下を実行します。

 `Install-Module -Name AzureRM.Network -Repository PSGallery -RequiredVersion 6.4.0-preview -AllowPrerelease -Force`

これで、バージョン 6.4.0 がインストールされます。

PowerShell をローカルで実行している場合は `Connect-AzureRmAccount` を実行して Azure との接続を作成する必要もあることを思い出してください。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、およびすべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、リソース グループ、仮想ネットワーク、およびネットワーク セキュリティ グループを作成します。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | サブネット構成オブジェクトを作成します。 |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Azure 仮想ネットワークとフロントエンド サブネットを作成します。 |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | ネットワーク セキュリティ グループに割り当てるセキュリティ規則を作成します。 |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |特定のサブネットに対して特定のポートを許可またはブロックする NSG ルールを作成します。 |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | NSG をサブネットに関連付けます。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | インターネットから VM にアクセスするためのパブリック IP アドレスを作成します。 |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | 仮想ネットワーク インターフェイスを作成し、それらを仮想ネットワークのフロントエンドおよびバックエンド サブネットに接続します。 |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 仮想マシンを作成します。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
|New-AzureRmFirewall| 新しい Azure Firewall データベースを作成します。|
|Get-AzureRmFirewall|Azure Firewall オブジェクトを取得します。|
|New-AzureRmFirewallApplicationRule|新しい Azure Firewall アプリケーション ルールを作成します。|
|Set-AzureRmFirewall|Azure Firewall オブジェクトへの変更をコミットします。|


## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

