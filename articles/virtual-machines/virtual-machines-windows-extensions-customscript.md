<properties
   pageTitle="テンプレートを使用した Windows VM のカスタム スクリプト | Microsoft Azure"
   description="カスタム スクリプト拡張機能と Resource Manager テンプレートを使用して Windows VM 構成タスクを自動化する"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Resource Manager テンプレートでの Windows VM のカスタム スクリプト拡張機能の使用

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
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

上記の例で、ファイルの URL とファイル名を独自の設定に置き換えます。テンプレートを作成したら、Azure PowerShell を使用してそのテンプレートをデプロイできます。

多くのシナリオで、お客様はスクリプトの URL とパラメーターをプライベートとしてを保持することを希望します。これを実現するには、ストレージ アカウント名とキーのみでしかアクセスできないようにし、保護された設定として送信されるように、スクリプトの URL をプライベートとして保持します。さらに、バージョン 1.7 以降の Windows カスタム スクリプト拡張機能では、保護された設定としてスクリプト パラメーターを指定することもできます。

## 保護された設定を持つ Windows VM テンプレートの例

        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
カスタム スクリプト拡張機能の最新バージョンのスキーマについては、[こちら](virtual-machines-windows-extensions-configuration-samples.md)のマニュアルを参照してください。

カスタム スクリプト拡張機能を使用して、VM でアプリケーションを構成する完全なサンプルについては、次の例をご覧ください。

* [Windows VM のカスタム スクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0803_2016-->