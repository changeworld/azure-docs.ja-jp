---
title: "Azure 仮想マシン スケール セットにアプリをデプロイする | Microsoft Docs"
description: "スケール セット内の Linux と Windows の仮想マシン インスタンスにアプリケーションを展開する方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 288bcdf6628f60d0b08fe151e630784d665db56f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>仮想マシン スケール セットへのアプリケーションのデプロイ
スケール セット内の仮想マシン (VM) インスタンスでアプリケーションを実行する　には、まず、アプリケーション コンポーネントと必要なファイルをインストールする必要があります。 この記事では、スケール セット内のインスタンス用にカスタム VM イメージを構築する、または既存の VM インスタンスにインストール スクリプトを自動的に実行する方法について説明します。 また、スケール セットのアプリケーションまたは OS 更新プログラムを管理する方法についても説明します。


## <a name="build-a-custom-vm-image"></a>カスタム VM イメージの構築
Azure プラットフォーム イメージの 1 つを使用してスケール セット内にインスタンスを作成するために、追加のソフトウェアをインストールしたり構成する必要はありません。 これらのコンポーネントのインストールは自動化できますが、スケール セットへ VM インスタンスをプロビジョニングするには時間がかかります。 VM インスタンスに多くの構成変更を適用する場合は、これらの構成スクリプトとタスクを実行するための管理オーバーヘッドが必要になります。

スケール セットにインスタンスがプロビジョニングされると同時にアプリケーションを実行できるカスタム VM イメージを構築すれば、構成管理と VM のプロビジョニングにかかる時間を短縮できます。 スケール セット インスタンスのためのカスタム VM イメージを作成する全体的なプロセスは次のとおりです。

1. スケール セット インスタンスのためのカスタム VM イメージを作成するには、VM にログインし、アプリケーションをインストール、構成します。 Packer を使用して [Linux](../virtual-machines/linux/build-image-with-packer.md) または [Windows](../virtual-machines/windows/build-image-with-packer.md) VM イメージを定義および構築できます。 または、手動で VM を作成し構成することができます。

    - [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md)、[Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)、または[ポータル](../virtual-machines/linux/quick-create-portal.md)を使用して Linux VM を作成します。
    - [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md)、[Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md)、または[ポータル](../virtual-machines/windows/quick-create-portal.md)を使用して Windows VM を作成します。
    - [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) または [Windows](../virtual-machines/windows/connect-logon.md) VM にログインします。
    - 必要なアプリケーションとツールをインストールし、構成します。 特定のバージョンのライブラリまたはランタイムが必要な場合、カスタム VM イメージを使用することでバージョンを定義でき、さらに 

2. [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) または [Azure PowerShell](../virtual-machines/windows/capture-image.md) を使用して仮想マシンをキャプチャできます。 この手順で作成したカスタム VM イメージは、スケール セットにインスタンスを展開するために使用されます。

3. [スケール セットを作成](virtual-machine-scale-sets-create.md)し、上記の手順で作成したカスタム VM イメージを指定します。


## <a name="already-provisioned"></a>カスタム スクリプト拡張機能を使用してアプリケーションをインストールする
カスタム スクリプト拡張機能は、Azure VM でスクリプトをダウンロードし、実行します。 この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。 スクリプトは、Azure ストレージや GitHub からダウンロードできます。また、拡張機能の実行時に Azure Portal に提供することもできます。

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。 

詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/windows/extensions-customscript.md)」を参照してください。


### <a name="use-azure-powershell"></a>Azure PowerShell の使用
PowerShell では、ハッシュテーブルを使用してダウンロードするファイルおよび実行するコマンドを格納します。 次の例をご覧ください。

- GitHub からスクリプトをダウンロードするよう VM インスタンスに指示する - *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- インストール スクリプトを実行するよう拡張機能を設定する - `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) を使用して、スケール セットに関する情報を取得する
- [Update-AzureRmVms](/powershell/module/azurerm.compute/update-azurermvmss) を使用して、VM インスタンスに拡張機能を適用する

*myResourceGroup* という名前のリソース グループ内の *myScaleSet* VM インスタンスにカスタム スクリプト拡張機能が適用されます。 独自の名前を次のように入力します。

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

スケール セットのアップグレード ポリシーが*手動*の場合は、[Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance) を使用して VM インスタンスを更新します。 このコマンドレットは、更新されたスケール セットの構成をVM インスタンスに適用し、アプリケーションをインストールします。


### <a name="use-azure-cli-20"></a>Azure CLI 2.0 の使用
カスタム スクリプト拡張機能を Azure CLI で使用するには、取得するファイルと実行するコマンドが定義された JSON ファイルを作成します。 これらの JSON 定義は、一貫したアプリケーション インストールを適用するためにスケール セット デプロイメント全体で再利用することができます。

現在のシェルで、*customConfig.json* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloudConfig.json` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

[az vmss 拡張機能セット](/cli/azure/vmss/extension#az_vmss_extension_set)を使用して、カスタム スクリプト拡張機能構成をスケール セット内の VM インスタンスに適用します。 次の例では、*customConfig.json* 構成を *myResourceGroup* という名前のリソース グループ内の *myScaleSet* VM インスタンスに適用します。 独自の名前を次のように入力します。

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

スケール セットのアップグレード ポリシーが*手動*の場合は、[az vmss update-instances](/cli/azure/vmss#update-instances) を使用して VM インスタンスを更新します。 このコマンドレットは、更新されたスケール セットの構成をVM インスタンスに適用し、アプリケーションをインストールします。


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>PowerShell DSC を使用して Windows VM にアプリをインストールする
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) は、ターゲット マシンの構成を定義するための管理プラットフォームです。 DSC 構成では、マシンにインストールするものと、ホストを構成する方法を定義します。 Local Configuration Manager (LCM) エンジンは、プッシュされた構成に基づいて要求されたアクションを処理する各ターゲット ノードで実行されます。

PowerShell DSC 拡張機能を使用すると、PowerShell を使用してスケール セット内の VM インスタンスをカスタマイズできます。 次の例をご覧ください。

- DSC パッケージを GitHub からダウンロードするよう VM インスタンスに指示する - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- インストール スクリプトを実行するよう拡張機能を設定する - `configure-http.ps1`
- [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) を使用して、スケール セットに関する情報を取得する
- [Update-AzureRmVms](/powershell/module/azurerm.compute/update-azurermvmss) を使用して、VM インスタンスに拡張機能を適用する

*myResourceGroup* という名前のリソース グループ内の *myScaleSet* VM インスタンスに DSC 拡張機能が適用されます。 独自の名前を次のように入力します。

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

スケール セットのアップグレード ポリシーが*手動*の場合は、[Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance) を使用して VM インスタンスを更新します。 このコマンドレットは、更新されたスケール セットの構成をVM インスタンスに適用し、アプリケーションをインストールします。


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>cloud-init を使用して Linux VM にアプリをインストールする
[cloud-Init](https://cloudinit.readthedocs.io/latest/) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 初回起動処理中に cloud-init が実行されるので、構成を適用するために追加の手順や必要なエージェントはありません。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

詳細については、*cloud-init.txt* ファイルの例を含む「[Use cloud-init to customize Azure VMs](../virtual-machines/linux/using-cloud-init.md)」(cloud-init を使用して Azure VM をカスタマイズする) を参照してください。

スケール セットを作成して cloud-init ファイルを使用するには、[az vmss create](/cli/azure/vmss#az_vmss_create) コマンドに `--custom-data` パラメーターを追加し、cloud-init ファイルの名前を指定します。 次の例では、*myScaleSet* という名前のスケール セットを *myResourceGroup* に作成し、*cloud-init.txt* という名前のファイルを使用して VM インスタンスを構成します。 独自の名前を次のように入力します。

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="install-applications-as-a-set-scales-out"></a>セット スケール アウトとしてアプリケーションをインストールする
スケール セットを使用すると、アプリケーションを実行する VM インスタンスの数を増やせます。 このスケール アウトのプロセスは、手動で起動するか、あるいは CPU またはメモリ使用率などのメトリックに基づいて自動的に起動できます。

スケール セットにカスタム スクリプト拡張機能を適用した場合は、新しい VM インスタンスごとに、アプリケーションがインストールされます。 スケール セットで、事前にアプリケーションがインストールされているカスタム イメージを使用している場合は、新しい VM インスタンスはそれぞれ使用可能な状態で配置されます。 

スケール セット VM インスタンスがコンテナー ホストの場合は、カスタム スクリプト拡張機能を使用してプルし、必要なコンテナー イメージを実行することができます。 カスタム スクリプト拡張機能は、Azure Container Service などのオーケストレーターに新しい VM インスタンスを登録することもできます。


## <a name="deploy-application-updates"></a>アプリケーションの更新プログラムをデプロイする
アプリケーション コード、ライブラリ、またはパッケージを更新する場合は、スケール セット内の VM インスタンスにアプリケーションの最新状態をプッシュできます。 カスタム スクリプト拡張機能を使用した場合、アプリケーションの更新プログラムは自動的に展開されません。 たとえば、更新後のバージョン名のインストール スクリプトを指すように、カスタム スクリプトの構成を変更します。 前の例では、カスタム スクリプト拡張機能は *automate_nginx.sh* という名前のスクリプトを使用しています。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

アプリケーションに加えた更新は、そのインストール スクリプトが変更されない限り、カスタム スクリプト拡張機能には公開されません。 アプリケーションがリリースされるたびに増分するバージョン番号を含めることも 1 つの方法です。 そうすると、カスタム スクリプト拡張機能は、次のように *automate_nginx_v2.sh* を参照できます。

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

これで、カスタム スクリプト拡張機能は、VM インスタンスに対して実行され、最新のアプリケーションの更新プログラムが適用されます。


### <a name="install-applications-with-os-updates"></a>OS 更新プログラムを使用してアプリケーションをインストールする
OS の新しいリリースが使用できる場合は、新しいカスタム イメージを使用または構築して、スケール セットに [OS の更新プログラムを展開](virtual-machine-scale-sets-upgrade-scale-set.md)できます。 各 VM インスタンスは、指定した最新のイメージにアップグレードされます。 アプリケーションが事前にインストールされたカスタム イメージ、カスタム スクリプト拡張機能、または PowerShell DSC を使用して、アップグレードを実行すると同時にアプリケーションが自動的に提供されるようにできます。 このプロセスを実行する場合、アプリケーションの保守を計画して、バージョンの互換性の問題がないことを確認する必要があります。

アプリケーションが事前にインストールされているカスタム VM イメージを使用すると、アプリケーションの更新プログラムをデプロイメント パイプラインに統合して新しいイメージを構築し、スケール セット全体に OS のアップグレードを展開することができます。 この方法を使用すると、パイプラインが最新のアプリケーション ビルドを選択し、VM を作成および認証し、スケール セットの VM インスタンスをアップグレードできます。 複数のカスタム VM イメージ間にわたってアプリケーション更新プログラムを構築し展開するデプロイメント パイプラインを実行するには、[Visual Studio Team Services](https://www.visualstudio.com/team-services/)、[Spinnaker](https://www.spinnaker.io/) または [Jenkins](https://jenkins.io/) を使用できます。


## <a name="next-steps"></a>次の手順
アプリケーションを構築してスケール セットに展開します。[スケール セットの設計の概要](virtual-machine-scale-sets-design-overview.md)を参照してください。 スケール セットの管理方法の詳細については、[PowerShell を使用してスケール セットを管理する](virtual-machine-scale-sets-windows-manage.md)を参照してください。
