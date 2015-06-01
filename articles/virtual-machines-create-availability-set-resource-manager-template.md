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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Azure リソース マネージャー テンプレートを使用した可用性セットの作成

仮想マシンの可用性セットは、Azure PowerShell (または xplat-cli) とリソース マネージャー テンプレートを使用して簡単に作成できます。このテンプレートによって可用性セットが作成されます。
 
実際の作業を開始する前に、Azure PowerShell と xplat-cli が構成され、準備が整っていることを確認してください。

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## Azure PowerShell からリソース マネージャー テンプレートを使って[実行する作業]

Github テンプレート リポジトリ内のリソース マネージャー テンプレートと Azure PowerShell を使用して[実行する作業]を行うには、以下の手順に従います。

### 手順 1. JSON ファイルをダウンローする

JSON テンプレート ファイルの場所としてローカル フォルダーを指定し、ファイルを作成します (例: C:\\Azure\\Templates[thing])。

以下のコマンドをコピーして実行します。フォルダー名は適宜置き換えてください。

	$folderName="<folder name, such as C:\Azure\Templates[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### 手順 2. (省略可能) パラメーターを表示する

テンプレートを使って[実行する作業]を行うには、一連の構成パラメーターを指定する必要があります。仮想マシンを作成するためのコマンドを実行する前に、ローカル JSON ファイルのテンプレートで指定する必要のあるパラメーターを確認するには、適切なツールまたはテキスト エディターで目的の JSON ファイルを開きます。 
ファイルの先頭にある "parameters" セクションを探してください。テンプレートで仮想マシンを構成するために必要な一連のパラメーターが指定されています。azuredeploy.json テンプレートの **"parameters"** セクションを次に示します。

[ライターへのメモ: コード例として、azuredeploy.json の "parameters" セクションを貼り付けてください。]

### 手順 3. [テンプレートを完成させるために必要な情報]を入手する

[ライターへのメモ: パラメーターの値を収集するための任意セクションです (必要な場合)。]

### 手順 4. テンプレートを使用して[作業を実行]する

Azure のデプロイメント名、リソース グループ名、Azure の場所、JSON ファイルの保存先フォルダーを指定して、以下のコマンドを実行します。

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates[thing]>" 
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

[ライターへのメモ: プロンプトで要求される最初のいくつかのパラメーターについて、PowerShell の表示を貼り付け、次の内容を置き換えてください。]

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


## xplat-cli からリソース マネージャー テンプレートを使って[実行する作業]

Github テンプレート リポジトリ内のリソース マネージャー テンプレートと xplat-cli を使用して[実行する作業]を行うには、以下の手順に従います。

### 手順 1. テンプレートの JSON ファイルをダウンロードする

JSON テンプレート ファイルの場所としてローカル フォルダーを指定し、ファイルを作成します (例: C:\\Azure\\Templates[thing])。

フォルダー名を指定して以下のコマンドを実行します。

[テンプレート ファイルをダウンロードする xplat コマンド]

### 手順 2. (省略可) テンプレートのパラメーターを確認する

テンプレートを使って[実行する作業]を行うには、一連の構成パラメーターを指定する必要があります。仮想マシンを作成するためのコマンドを実行する前に、ローカル JSON ファイルのテンプレートで指定する必要のあるパラメーターを確認するには、適切なツールまたはテキスト エディターで目的の JSON ファイルを開きます。 
ファイルの先頭にある "parameters" セクションを探してください。テンプレートで仮想マシンを構成するために必要な一連のパラメーターが指定されています。azuredeploy.json テンプレートの **"parameters"** セクションを次に示します。

[ライターへのメモ: コード例として、azuredeploy.json の "parameters" セクションを貼り付けてください。]

### 手順 3. [テンプレートを完成させるために必要な情報]を入手する

[ライターへのメモ: パラメーターの値を収集するための任意セクションです (必要な場合)。]

### 手順 4. テンプレートを使用して[作業を実行]する

[必要な情報} を入力して以下のコマンドを実行します。

[テンプレート ファイルを実行する xplat コマンド]

[xplat によるテンプレート実行の説明]


以下に示したのは、テンプレートに使用する一連の xplat-cli コマンドの例です。

[xplat コマンドの例]

次のような結果が表示されます。

[ライターへのメモ: プロンプトで要求される最初のいくつかのパラメーターについて、xplat の表示を貼り付けてください。]


このリソース グループとそのすべてのリソース ([リソース グループの内容]) を削除するには、次のコマンドを使用します。

[xplat コマンド]




<!--HONumber=52-->
