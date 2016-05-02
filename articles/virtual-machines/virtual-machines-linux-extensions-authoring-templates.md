<properties
   pageTitle="Linux VM の拡張機能を使用したテンプレートの作成 |Microsoft Azure"
   description="Linux VM の拡張機能を使用した Azure Resource Manager テンプレートの作成について説明します。"
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

# Linux VM 拡張機能を使用した Azure Resource Manager テンプレートの作成

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]クラシック デプロイ モデル。

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Azure CLI から次のコマンドを実行します。

      Azure VM extension list

このコマンドによって、発行元の名前、拡張機能名、バージョンが次のように返されます。

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

これら 3 つのプロパティはそれぞれ、前述のテンプレート スニペットに含まれる "publisher"、"type"、"typeHandlerVersion" に対応しています。

>[AZURE.NOTE]常に拡張機能の最新バージョンを使用して最新機能を取得することをお勧めします。

## 拡張機能構成パラメーターのスキーマの識別

拡張機能のテンプレートを作成する次の手順では、構成パラメーターを指定するための形式を特定します。それぞれの拡張機能では、独自のパラメーター セットがサポートされています。

Linux 拡張機能のサンプル構成については、[Linux 拡張機能のサンプル](virtual-machines-linux-extensions-configuration-samples.md)に関するドキュメントをクリックして参照してください。

VM の拡張機能を使用して完成したテンプレートを取得するには、以下を参照してください。

[Linux VM のカスタム スクリプト拡張機能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

テンプレートを作成したら、Azure CLI を使用してそのテンプレートをデプロイできます。

<!---HONumber=AcomDC_0420_2016-->