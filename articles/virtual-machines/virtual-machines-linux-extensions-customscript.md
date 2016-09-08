<properties
   pageTitle="テンプレートを使用した Linux VM のカスタム スクリプト | Microsoft Azure"
   description="カスタム スクリプト拡張機能と Resource Manager テンプレートを使用して Linux VM 構成タスクを自動化する"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Azure Resource Manager テンプレートでの Linux VM のカスタム スクリプト拡張機能の使用

この記事では、Linux VM のブートストラップ ワークロード用に、カスタム スクリプト拡張機能を使用して Azure Resource Manager テンプレートを作成する方法の概要を説明します。

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Linux VM のテンプレートの例

テンプレートの Resource セクションで、次の拡張機能リソースを定義します。

   { "type": "Microsoft.Compute/virtualMachines/extensions", "name": "MyCustomScriptExtension", "apiVersion": "2015-05-01-preview", "location": "[parameters('location')]", "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"], "properties": { "publisher": "Microsoft.OSTCExtensions", "type": "CustomScriptForLinux", "typeHandlerVersion": "1.2", "autoUpgradeMinorVersion": true "settings": { "fileUris": [ "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh" ], "commandToExecute": "sh mongo-install-ubuntu.sh" }, "protectedSettings": {} } }

上記の例で、ファイルの URL とファイル名を独自の設定に置き換えます。

テンプレートを作成したら、Azure CLI を使用してそのテンプレートをデプロイできます。

カスタム スクリプト拡張機能を使用して VM でアプリケーションを構成する完全なサンプルについては、次の例をご覧ください。

* [Linux VM のカスタム スクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0824_2016-->