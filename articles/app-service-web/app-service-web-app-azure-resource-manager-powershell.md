<properties
	pageTitle="Azure Resource Manager ベースの PowerShell コマンドを使用して Azure Web アプリを管理する | Microsoft Azure"
	description="Azure Resource Manager ベースの新しい PowerShell コマンドを使用して Azure Web アプリを管理する方法について説明します。"
	services="app-service\web"
	documentationCenter=""
	authors="ahmedelnably"
	manager="stefsch"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="aelnably"/>

# Azure Resource Manager ベースの PowerShell を使用して Azure Web アプリを管理する#

Microsoft Azure PowerShell Version 1.0.0 のリリースに伴い、新しいコマンドが追加されました。今後は Azure Resource Manager ベースの PowerShell コマンドを使って Web Apps を管理することができます。

リソース グループの管理については、「[Azure Resource Manager での Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

Web アプリの管理に使用される Azure Resource Manager PowerShell コマンドレットのパラメーターの全一覧とオプションについては、[Azure Resource Manager ベースの PowerShell コマンドレットを使って Web アプリを管理するための詳しいコマンドレット リファレンス](https://msdn.microsoft.com/library/mt619237.aspx)を参照してください。

## App Service プランの管理 ##

### App Service プランを作成する ###
新しい App Service プランを作成するには、**New-AzureRmAppServicePlan** コマンドレットを使用します。

以下、各パラメーターについて説明します。

- 	**Name**: App Service プランの名前。
- 	**Location**: サービス プランの場所。
- 	**ResourceGroupName**: 新たに作成された App Service プランが属するリソース グループ。
- 	**Tier**: 必要な価格レベル。既定値は Free です。それ以外に Shared、Basic、Standard、Premium を選択できます。
- 	**WorkerSize**: worker のサイズ。Tier パラメーターに Basic、Standard、Premium が指定された場合の既定値は S です。それ以外に M と L を選択できます。
- 	**NumberofWorkers**: App Service プランの worker の数。既定値は 1 です。 

コマンドレットの使用例:

    New-AzureRmAppServicePlan -Name ContosoAppServicePlan -Location "South Central US" -ResourceGroupName ContosoAzureResourceGroup -Tier Premium -WorkerSize Large -NumberofWorkers 10

### 既存の App Service プランの一覧表示 ###

既存の App Service プランを一覧表示するには、**Get-AzureRmAppServicePlan** コマンドレットを使用します。

自分のサブスクリプションのすべての App Service プランを一覧表示するには、**Get-AzureRmAppServicePlan** を使用します。

特定のリソース グループのすべての App Service プランを一覧表示するには、次のように入力します。

    Get-AzureRmAppServicePlan -ResourceGroupname ContosoAzureResourceGroup

特定の App Service プランを取得するには、次のように入力します。

    Get-AzureRmAppServicePlan -Name ContosoAppServicePlan


### 既存の App Service プランの構成 ###

既存の App Service プランの設定を変更するには、**Set-AzureRmAppServicePlan** コマンドレットを使用します。価格レベル、worker サイズ、worker 数を変更することができます。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard -WorkerSize Medium -NumberofWorkers 9

#### App Service プランのスケーリング ####

既存の App Service プランをスケーリングするには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -NumberofWorkers 9

#### App Service プランの worker サイズの変更 ####

既存の App Service プランの worker のサイズを変更するには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -WorkerSize Medium

#### App Service プランの価格レベルの変更 ####

既存の App Service プランの価格レベルを変更するには、次のように入力します。

    Set-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Tier Standard

### 既存の App Service プランの削除 ###

既存の App Service プランを削除するにはまず、割り当てられているすべての Web アプリを移動するか削除しておく必要があります。その後、**Remove-AzureRmAppServicePlan** コマンドレットを使用して App Service プランを削除できます。

    Remove-AzureRmAppServicePlan -Name ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup

## App Service Web Apps の管理 ##

### 新しい Web アプリの作成 ###

新しい Web アプリを作成するには、**New-AzureRmWebApp** コマンドレットを使用します。

以下、各パラメーターについて説明します。

- **Name**: Web アプリの名前。
- **AppServicePlan**: Web アプリのホストとなるサービス プランの名前。
- **ResourceGroupName**: App Service プランのホストとなるリソース グループ。
- **Location**: Web アプリの場所。

コマンドレットの使用例:

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"

### App Service Environment で新しい Web アプリを作成する ###

App Service Environment (ASE) に新しい Web アプリを作成するには、同じ **New-AzureRmWebApp** コマンドに別途パラメーターを追加し、ASE の名前とその ASE が属するリソース グループの名前を指定します。

    New-AzureRmWebApp -Name ContosoWebApp -AppServicePlan ContosoAppServicePlan -ResourceGroupName ContosoAzureResourceGroup -Location "South Central US"  -ASEName ContosoASEName -ASEResourceGroupName ContosoASEResourceGroupName

App Service Environment の詳細については、「[App Service 環境の概要](app-service-app-service-environment-intro.md)」を参照してください。

### 既存の Web アプリの削除 ###

既存の Web アプリを削除するには、**Remove-AzureRmWebApp** コマンドレットを使用します。Web アプリの名前とリソース グループの名前を指定する必要があります。

    Remove-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup


### 既存の Web アプリの一覧表示 ###

既存の Web アプリを一覧表示するには、**Get-AzureRmWebApp** コマンドレットを使用します。

自分のサブスクリプションのすべての Web アプリを一覧表示するには、次のように入力します。

    Get-AzureRmWebApp

特定のリソース グループのすべての Web アプリを一覧表示するには、次のように入力します。

    Get-AzureRmWebApp -ResourceGroupname ContosoAzureResourceGroup

特定の Web アプリを取得するには、次のように入力します。

    Get-AzureRmWebApp -Name ContosoWebApp

### 既存の Web アプリの構成 ###

既存の Web アプリの設定と構成を変更するには、**Set-AzureRmWebApp** コマンドレットを使用します。全パラメーターの一覧については、[コマンドレット リファレンス リンク](https://msdn.microsoft.com/library/mt652487.aspx)を参照してください。

例 (1): 接続文字列を変更する

	$connectionstrings = @{ ContosoConn1 = @{ Type = “MySql”; Value = “MySqlConn”}; ContosoConn2 = @{ Type = “SQLAzure”; Value = “SQLAzureConn”} }
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -ConnectionStrings $connectionstrings

例 (2): アプリ設定の例を追加する

	$appsettings = @{appsetting1 = "appsetting1value"; appsetting2 = "appsetting2value"}
	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -AppSettings $appsettings


例 (3): 64 ビット モードで動作するように Web アプリを設定する

	Set-AzureRmWebApp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -Use32BitWorkerProcess $False

### 既存の Web アプリの状態を変更する ###

#### Web アプリの再起動 ####

Web アプリを再起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    Restart-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Web アプリの停止 ####

Web アプリを停止するには、Web アプリの名前とリソース グループを指定する必要があります。

    Stop-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

#### Web アプリの起動 ####

Web アプリを起動するには、Web アプリの名前とリソース グループを指定する必要があります。

    Start-AzureRmWebapp -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Web アプリの発行プロファイルの管理 ###

Web アプリにはそれぞれ、アプリを発行するときに使用する発行プロファイルがあります。発行プロファイルには、さまざまな操作を実行することができます。

#### 発行プロファイルの取得 ####

Web アプリの発行プロファイルを取得するには、次のように入力します。

    Get-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup -OutputFile .\publishingprofile.txt

この例では、発行プロファイルをコマンド ラインにエコーすると共に、発行プロファイルをテキスト ファイルに出力していることに注目してください。

#### 発行プロファイルのリセット ####

Web アプリの Web デプロイと FTP の発行パスワードを両方ともリセットするには、次のように入力します。

    Reset-AzureRmWebAppPublishingProfile -Name ContosoWebApp -ResourceGroupName ContosoAzureResourceGroup

### Web アプリの証明書の管理 ###

Web アプリの証明書を管理する方法については、[PowerShell を使用した SSL 証明書のバインド](app-service-web-app-powershell-ssl-binding.md)に関するページを参照してください。



### 次のステップ ###
- Azure Resource Manager の PowerShell 対応については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。
- App Service Environment については、「[App Service 環境の概要](app-service-app-service-environment-intro.md)」を参照してください。
- PowerShell を使用した App Service SSL 証明書の管理については、[PowerShell を使用した SSL 証明書のバインド](app-service-web-app-powershell-ssl-binding.md)に関するページを参照してください。
- Azure Web Apps に使用する Azure Resource Manager ベースの PowerShell コマンドレットの全一覧については、[Azure Resource Manager の PowerShell コマンドレットを使って Web アプリを管理するための Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/mt619237.aspx)を参照してください。

<!---HONumber=AcomDC_0601_2016-->