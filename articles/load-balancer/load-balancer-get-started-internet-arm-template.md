<properties 
   pageTitle="テンプレートを使用したリソース マネージャーでのインターネット接続ロード バランサーの作成 | Microsoft Azure"
   description="ARM テンプレートを使用して、リソース マネージャーでインターネット接続ロード バランサーを作成する方法について説明します。"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
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
   ms.date="12/18/2015"
   ms.author="joaoma" />

# ARM テンプレートを使用したインターネットに接続するロード バランサーの作成の開始

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]この記事では、リソース マネージャーのデプロイ モデルについて説明します。[従来のデプロイ モデルを使用してインターネットに接続するロード バランサーを作成する方法](load-balancer-get-started-internet-classic-portal.md)についても説明します。


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## [デプロイ] をクリックして ARM テンプレートをデプロイする

パブリック リポジトリで使用できるサンプル テンプレートは、上記のシナリオの生成に使用した既定値を含むパラメーター ファイルを使用します。"クリックしてデプロイ" を使用してこのテンプレートをデプロイするには、[このリンク](http://go.microsoft.com/fwlink/?LinkId=544801)に従って、**[Azure へのデプロイ]** をクリックし、必要に応じて既定のパラメーター値を置き換えて、ポータルの指示に従います。

## PowerShell を使用して ARM テンプレートをデプロイする

PowerShell を使用してダウンロードした ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure PowerShell を初めて使用する場合は、[Azure PowerShell のインストールおよび構成方法](powershell-install-configure.md)を参照し、このページにある手順をすべて最後まで実行し、Azure にサインインしてサブスクリプションを選択します。

2. テンプレートを使用してリソース グループを作成するには、**New-AzureRmResourceGroup** コマンドレットを実行します。

		New-AzureRmResourceGroup -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'	

## Azure CLI を使用して ARM テンプレートをデプロイする

Azure CLI を使用して ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、[Azure CLI のインストールと構成](xplat-cli.md)に関するページを参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、**azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。

		azure config mode arm

	上記のコマンドで想定される出力を次に示します。

		info:    New mode is arm

3. ブラウザーから ****https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json** に移動し、json ファイルの内容をコピーして、コンピューター内の新しいファイルに貼り付けます。このシナリオでは、次の値を **c:\\lb\\azuredeploy.parameters.json** という名前のファイルにコピーします。
4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用し、**azure group deployment create** コマンドレットを実行して、新しいロード バランサーをデプロイします。出力の後に表示される一覧では、使用されたパラメーターについて説明されています。

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

## 次のステップ

[内部ロード バランサーの構成の開始](load-balancer-internal-getstarted.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1223_2015-->