<properties
   pageTitle="Azure VM の拡張機能を使用したテンプレートの作成 |Microsoft Azure"
	description="拡張機能を使用したテンプレートの作成ついて詳しく説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# VM 拡張機能を使用した Azure リソース マネージャー テンプレートの作成

## Azure リソース マネージャー テンプレートの概要

Azure リソース マネージャー テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。Azure リソース マネージャー テンプレートの概要については、次の記事をご覧ください。

<a href="https://azure.microsoft.com/ja-JP/documentation/articles/resource-group-overview/" target="_blank">リソース グループの概要</a><br/><a href="https://azure.microsoft.com/ja-JP/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Azure CLI を使用したテンプレートのデプロイ</a><br/><a href="https://azure.microsoft.com/ja-JP/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Azure Powershell を使用したテンプレートのデプロイ</a>

## VM 拡張機能のサンプル テンプレート スニペット
拡張機能をデプロイするためのテンプレート スニペットは次のようになります。

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

## 任意の拡張機能の発行元、種類、typeHandlerVersion の識別

Azure VM 拡張機能は、マイクロソフトと信頼されたサード パーティの発行元によって発行されています。それぞれの拡張機能は、その発行元、種類、typeHandlerVersion によって一意に識別されます。これらは、次の方法で確認できます。

Azure PowerShell では、次の Azure PowerShell コマンドレットを実行します。

      Get-AzureVMAvailableExtension

Azure CLI では、次の Azure PowerShell コマンドレットを実行します。

      Azure VM Extension list

このコマンドレットによって、次のように発行元の名前、拡張機能名、バージョンが返されます。

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

これら 3 つのプロパティはそれぞれ、前述のテンプレート スニペットに含まれる "publisher"、"type"、"typeHandlerVersion" に対応しています。注: 常に拡張機能の最新バージョンを使用して最新機能を取得することをお勧めします。

## 拡張機能構成パラメーターのスキーマの識別

拡張機能のテンプレートを作成する次の手順では、構成パラメーターを指定するための形式を特定します。それぞれの拡張機能では、独自のパラメーター セットがサポートされています。

Windows 拡張機能のいくつかのサンプル構成を確認するには、[Windows 拡張機能のサンプル](virtual-machines-extensions-configuration-samples-windows.md)に関するドキュメントをクリックします。

Linux 拡張機能のいくつかのサンプル構成を確認するには、[Linux 拡張機能のサンプル](virtual-machines-extensions-configuration-samples-linux.md)に関するドキュメントをクリックします。

VM の拡張機能を使用して完成したテンプレートを取得するには、次の VM テンプレートを参照してください。

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Linux VM のカスタム スクリプト拡張機能</a></br><a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Windows VM のカスタム スクリプト拡張機能</a>

テンプレートを作成したら、Azure CLI または Azure Powershell を使用してそのテンプレートをデプロイできます。

<!---HONumber=September15_HO1-->