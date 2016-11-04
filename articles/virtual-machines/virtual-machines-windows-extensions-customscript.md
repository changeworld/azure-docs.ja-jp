---
title: テンプレートを使用した Windows VM のカスタム スクリプト | Microsoft Docs
description: カスタム スクリプト拡張機能と Resource Manager テンプレートを使用して Windows VM 構成タスクを自動化する
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Azure Resource Manager テンプレートでの Windows VM のカスタム スクリプト拡張機能の使用
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

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

スクリプトの URL とパラメーターを公開しない場合は、スクリプトの URL を **private** に設定します。スクリプトの URL が **private** に設定されている場合には、保護された設定としてストレージ アカウント名とキーを送信しないとアクセスできません。また、バージョン 1.7 以降のカスタム スクリプト拡張機能では、保護された設定としてスクリプト パラメーターを指定することもできます。

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
カスタム スクリプト拡張機能の最新バージョンのスキーマについては、「[Azure Windows VM 拡張機能の構成サンプル](virtual-machines-windows-extensions-configuration-samples.md)」を参照してください。

カスタム スクリプト拡張機能を使用して、VM でアプリケーションを構成するサンプルについては、「[Windows 仮想マシンでのカスタムのスクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)」を参照してください。

<!---HONumber=AcomDC_0824_2016-->