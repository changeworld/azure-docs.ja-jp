<properties
   pageTitle="テンプレートを使用した Windows VM のカスタム スクリプト | Microsoft Azure"
   description="カスタム スクリプト拡張機能と Resource Manager テンプレートを使用して Windows VM 構成タスクを自動化する"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="vturecek"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Azure Resource Manager テンプレートでの Windows VM のカスタム スクリプト拡張機能の使用

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md)。

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Windows VM のテンプレートの例

テンプレートの Resource セクションで、次のリソースを定義します。

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }
     
上記の例で、ファイルの URL とファイル名を独自の設定に置き換えます。

テンプレートを作成したら、Azure PowerShell を使用してそのテンプレートをデプロイできます。

カスタム スクリプト拡張機能を使用して、VM でアプリケーションを構成する完全なサンプルについては、次の例をご覧ください。

* [Windows VM のカスタム スクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->
