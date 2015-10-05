<properties
   pageTitle="テンプレートを使用した VM のカスタム スクリプト | Microsoft Azure"
   description="カスタム スクリプト拡張機能とリソース マネージャー テンプレートを使用して Windows および Linux Azure VM 構成タスクを自動化する"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="07/01/2015"
   ms.author="kundanap"/>

# Azure リソース マネージャー テンプレートでのカスタム スクリプト拡張機能の使用

この記事では、Linux VM または Windows VM のブートストラップ ワークロード用に、カスタム スクリプト拡張機能を使用して Azure リソース マネージャー テンプレートを作成する方法の概要を説明します。

カスタム スクリプト拡張機能の概要については、[こちらの記事](virtual-machines-extensions-customscript.md)をご覧ください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャー デプロイ モデルを使用したリソースの作成について説明します。リソースは、[クラシック デプロイ モデル](virtual-machines-extensions-customscript.md)でも作成できます。

カスタム スクリプト拡張機能は、導入以来、Windows VM と Linux VM でワークロードを構成するために広く使用されてきました。Azure リソース マネージャー テンプレートの導入により、VM をプロビジョニングするだけでなく、VM でワークロードも構成する単一のテンプレートを作成できるようになりました。

## Azure リソース マネージャー テンプレートの概要

Azure リソース マネージャー テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。Azure リソース マネージャー テンプレートの概要については、次の記事をご覧ください。

<a href="https://azure.microsoft.com/ja-JP/documentation/articles/resource-group-overview/" target="_blank">リソース グループの概要</a><br/><a href="https://azure.microsoft.com/ja-JP/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Azure CLI を使用したテンプレートのデプロイ</a><br/><a href="https://azure.microsoft.com/ja-JP/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Azure Powershell を使用したテンプレートのデプロイ</a>

### カスタム スクリプト拡張機能を実行する前提条件

1. <a href="http://azure.microsoft.com/downloads" target="_blank">ここ</a>から最新の Azure PowerShell コマンドレットまたは Azure CLI をインストールします。
2. スクリプトが既存の VM で実行されている場合、VM エージェントがその VM 上で有効であることをご確認ください。そうでない場合は、<a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">こちらの記事</a>の手順に従ってインストールしてください。
3. VM で実行するスクリプトを Azure ストレージにアップロードします。スクリプトは、1 つか複数のストレージ コンテナーから取得できます。
4. スクリプトを Github アカウントにアップロードすることもできます。
5. スクリプトは、拡張機能によって起動されるエントリ スクリプトが、他のスクリプトを順に起動するように記述されている必要があります。

## テンプレートでのカスタム スクリプト拡張機能の使用方法の概要

テンプレートを使用したデプロイでは、Azure サービス管理 API で使用できるバージョンと同じバージョンのカスタム スクリプト拡張機能を使用します。拡張機能では、同じパラメーターとシナリオ (Azure ストレージ アカウントまたは Github へのファイルのアップロードなど) をサポートします。テンプレートで使用する際の重要な違いは、拡張機能のバージョンを majorversion.* 形式で指定するのではなく、正確なバージョンを指定する必要があることです。

 ## Linux VM のカスタム スクリプト拡張機能のテンプレート スニペット

テンプレートの Resource セクションで、次の拡張機能リソースを定義します。

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Windows VM のカスタム スクリプト拡張機能のテンプレート スニペット

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

テンプレートを作成したら、Azure CLI または Azure Powershell を使用してテンプレートをデプロイできます。

カスタム スクリプト拡張機能を使用して、VM でアプリケーションを構成する完全なサンプルについては、次の例をご覧ください。

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Linux VM のカスタム スクリプト拡張機能</a></br><a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Windows VM のカスタム スクリプト拡張機能</a>

<!---HONumber=Sept15_HO4-->