<properties 
   pageTitle="リソース マネージャーでのテンプレートを使用した内部ロード バランサーの作成 | Microsoft Azure"
   description="リソース マネージャーでテンプレートを使用して、内部ロード バランサーを作成する方法について説明します"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="sewhee" />

# テンプレートを使用した内部ロード バランサーの作成の概要

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md)

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## [クリックしてデプロイ] を使用してテンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。"クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、[このリンク](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)に従って、**[Azure へのデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## PowerShell を使用してテンプレートをデプロイする

PowerShell を使用してダウンロードしたテンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](../../articles/powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。


2. [パラメーター](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json) ファイルをローカル ディスクにダウンロードします。
3. ファイルを編集し、保存します。
4. テンプレートを使用してリソース グループを作成するには、**New-AzureRmResourceGroupDeployment** コマンドレットを実行します。


		New-AzureRmResourceGroupDeployment -Name TestRG -Location westus `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
		    -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
	
                
		
## Azure CLI を使用してテンプレートをデプロイする

Azure CLI を使用してテンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「[Azure CLI のインストール](../../articles/xplat-cli-install.md)」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、**azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

		azure config mode arm

	上記のコマンドで想定される出力を次に示します。

		info:    New mode is arm

3. [パラメーター ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json)を開き、内容を選択して、コンピューター内のファイルに保存します。この例では、パラメーター ファイルを *parameters.json* に保存しました。

4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用し、**azure group deployment create** コマンドを実行して、新しい内部ロード バランサーをデプロイします。出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

		azure group create -n TestRG -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json -e parameters.json


## 次のステップ

[ソース IP アフィニティを使用したロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->