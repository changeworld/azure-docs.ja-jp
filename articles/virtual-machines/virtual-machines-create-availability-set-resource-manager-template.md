<properties 
	pageTitle="Azure リソース マネージャー テンプレートを使用した可用性セットの作成" 
	description="可用性セット テンプレートの使用方法とテンプレートの構文について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="kathydav"/>

# Azure リソース マネージャー テンプレートを使用した可用性セットの作成

仮想マシンの可用性セットは、Azure PowerShell (または Azure コマンド ライン (CLI)) とリソース マネージャー テンプレートを使用して簡単に作成できます。このテンプレートによって可用性セットが作成されます。
 
実際の作業を開始する前に、Azure PowerShell と Azure CLI が構成され、準備が整っていることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## リソース マネージャー テンプレートを使用した可用性セットの作成

GitHub テンプレート リポジトリ内のリソース マネージャー テンプレートと Azure PowerShell を使用して仮想マシンの可用性セットを作成するには、以下の手順に従います。

### 手順 1. JSON ファイルをダウンローする

JSON テンプレート ファイルの場所としてローカル フォルダーを指定し、ファイルを作成します (例: C:\Azure\Templates\availability)。

以下のコマンドをコピーして実行します。フォルダー名は適宜置き換えてください。

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### 手順 2. 必須パラメーターの詳細情報を収集する

テンプレートを使用する場合は、場所やセット名などの詳細情報を入力する必要があります。テンプレートにどのようなパラメーターが必要かを確認するには、次のいずれかを実行します。

- [こちら](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/)でパラメーターの一覧を確認する。
- 任意のツールまたはテキスト エディターで、JSON ファイルを開く。ファイルの先頭にある "parameters" セクションを探してください。テンプレートで仮想マシンを構成するために必要な一連のパラメーターが指定されています。 

入力できるように、必要な情報を収集します。テンプレートをデプロイするコマンドを実行すると、情報を入力するよう求められます。

### 手順 3. 可用性セットを作成する

以降のセクションでは、Azure PowerShell または Azure CLI を使用してこの操作を行う方法について説明します。

### Azure PowerShell の使用

Azure のデプロイ名、リソース グループ名、Azure の場所、JSON ファイルの保存先フォルダーを指定して、以下のコマンドを実行します。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

**New-AzureResourceGroupDeployment** コマンドを実行すると、JSON ファイルの **"parameters"** セクションのパラメーターの値を指定するよう求められます。パラメーターの値を指定すると、リソース グループと可用性セットが作成されます。

以下に示したのは、テンプレートに使用する一連の PowerShell コマンドの例です。

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

次のような結果が表示されます。

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

このリソース グループとそのすべてのリソース (ストレージ アカウント、仮想マシン、仮想ネットワーク) を削除するには、次のコマンドを使用します。

	Remove-AzureResourceGroup –Name "<resource group name>"


## Azure CLI の使用

GitHub テンプレート リポジトリ内のリソース マネージャー テンプレートと Azure CLI コマンドを使用して可用性セットを作成するには、以下の手順に従います。

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json





 

<!---HONumber=58_postMigration-->