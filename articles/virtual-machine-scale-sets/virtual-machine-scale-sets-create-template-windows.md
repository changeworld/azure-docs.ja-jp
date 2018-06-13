---
title: Azure テンプレートを使用して Windows 仮想マシン スケール セットを作成する | Microsoft Docs
description: サンプル アプリのデプロイと自動スケール ルールの構成を行う Azure Resource Manager テンプレートを使用して Windows 仮想マシン スケール セットをすばやく作成する方法について説明します
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2018
ms.locfileid: "30201072"
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Azure テンプレートを使用して Windows 仮想マシン スケール セットを作成する
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 この入門記事では、Azure Resource Manager テンプレートを使用して仮想マシン スケール セットを作成します。 スケール セットは、[Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md)、[Azure PowerShell](virtual-machine-scale-sets-create-powershell.md)、または [Azure ポータル](virtual-machine-scale-sets-create-portal.md)を使用して作成することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 4.4.1 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzureRmAccount` を実行して Azure との接続を作成することも必要です。


## <a name="define-a-scale-set-in-a-template"></a>テンプレート内にスケール セットを定義する
Azure Resource Manager テンプレートを使用して、関連するリソースのグループをデプロイできます。 テンプレートは JavaScript Object Notation (JSON) で記述され、アプリケーションの Azure インフラストラクチャ環境全体を定義します。 1 つのテンプレートで、仮想マシン スケール セットの作成、アプリケーションのインストール、および自動スケール ルールの構成を実行できます。 変数やパラメーターを使用してこのテンプレートを再利用することで、既存のスケール セットを更新したり、追加のスケール セットを作成したりできます。 テンプレートは、Azure ポータル、Azure CLI 2.0、Azure PowerShell を介してデプロイすることも、継続的インテグレーション/継続的デリバリー (CI/CD) パイプラインから呼び出すこともできます。

テンプレートの詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)」を参照してください。

テンプレートは、各リソースの種類の構成を定義します。 仮想マシン スケール セットのリソースの種類は、個々の VM に似ています。 仮想マシン スケール セットのリソースの種類のコア部分は次のとおりです。

| プロパティ                     | プロパティの説明                                  | テンプレート値の例                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| 型                         | 作成する Azure リソースの種類                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | スケール セットの名前                                       | myScaleSet                                |
| location                     | スケール セットを作成する場所                     | 米国東部                                   |
| sku.name                     | 各スケール セット インスタンスの VM サイズ                  | Standard_A1                               |
| sku.capacity                 | 最初に作成する VM インスタンスの数           | 2                                         |
| upgradePolicy.mode           | 変更が発生した場合の VM インスタンスのアップグレード モード              | 自動                                 |
| imageReference               | VM インスタンスに使用するプラットフォームまたはカスタム イメージ | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | 各 VM インスタンス名のプレフィックス                     | myvmss                                    |
| osProfile.adminUsername      | 各 VM インスタンスのユーザー名                        | azureuser                                 |
| osProfile.adminPassword      | 各 VM インスタンスのパスワード                        | P@ssw0rd!                                 |

 次の例は、コア スケール セットのリソース定義を示しています。 スケール セット テンプレートをカスタマイズするために、VM サイズや初期容量を変更したり、別のプラットフォームやカスタム イメージを使用したりできます。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 サンプルを短くするために、仮想ネットワーク インターフェイス カード (NIC) の構成は示されていません。 ロード バランサーなどの追加コンポーネントも示されていません。 [この記事の最後に](#deploy-the-template)完全なスケール セット テンプレートが示されています。


## <a name="install-an-application"></a>アプリケーションをインストールする
スケール セットをデプロイするとき、VM 拡張機能によって、デプロイ後の構成とオートメーション タスク (アプリのインストールなど) を提供できます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。 拡張機能をスケール セットに適用するには、上記のリソース例に *extensionProfile* セクションを追加します。 拡張機能プロファイルは、通常、次のプロパティを定義します。

- 拡張機能の種類
- 拡張機能の発行元
- 拡張機能のバージョン
- 構成またはインストール スクリプトの場所
- VM インスタンスで実行するコマンド

[Windows 上の ASP.NET アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) サンプル テンプレートでは、PowerShell DSC 拡張機能を使用して、IIS で実行される ASP.NET MVC アプリをインストールします。 

インストール スクリプトは、GitHub からダウンロードされます。これは *url* として定義されます。 次に、拡張機能は、*IISInstall.ps1* スクリプトの *InstallIIS* を実行します。これらは *Script*と *function* として定義されます。 ASP.NET アプリ自体は Web 配置パッケージとして提供され、GitHub からダウンロードされます。これは、*WebDeployPackagePath* として定義されます。

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ
[Windows 上の ASP.NET MVC アプリケーション](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) テンプレートは、次の **[Deploy to Azure]** ボタンを使用してデプロイできます。 このボタンは、Azure ポータルを開き、完全なテンプレートを読み込み、スケール セットの名前、インスタンス数、管理者の資格情報などのいくつかのパラメーターの入力を求めます。

[![テンプレートを Azure にデプロイする](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

次のように、Azure PowerShell で [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) を使用して ASP.NET アプリケーションを Windows にインストールすることもできます。

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

プロンプトに応答して、VM インスタンスのスケール セットの名前と管理者の資格情報を指定します。 スケール セットが作成され、拡張機能を適用してアプリを構成するのに 10 ～ 15 分かかる可能性があります。


## <a name="test-your-sample-application"></a>サンプル アプリケーションをテストする
アプリが動いているのを確認するには、[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用してロード バランサーのパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

ロード バランサーのパブリック IP アドレスを *http://publicIpAddress/MyApp* 形式で Web ブラウザーに入力します。 ロード バランサーは、次の例に示すように、VM インスタンスのいずれかにトラフィックを配分します。

![実行中の IIS サイト](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ
必要がなくなったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループ、スケール セット、およびすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>次の手順
この入門記事では、Azure テンプレートを使用して Windows スケール セットを作成し、PowerShell DSC 拡張機能を使用して基本的な ASP.NET アプリを VM インスタンスにインストールしました。 スケーラビリティとオートメーションを高めるために、次の操作方法に関する記事を参照してスケール セットを拡張してください。

- [仮想マシン スケール セットへのアプリケーションのデプロイ](virtual-machine-scale-sets-deploy-app.md)
- [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md)[Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)または [Azure ポータル](virtual-machine-scale-sets-autoscale-portal.md)を使用した自動的なスケーリング
- [スケール セット VM インスタンスに対する OS の自動アップグレードの使用](virtual-machine-scale-sets-automatic-upgrade.md)
