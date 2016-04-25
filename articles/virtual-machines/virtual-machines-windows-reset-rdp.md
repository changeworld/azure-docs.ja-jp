<properties
	pageTitle="Windows VM でパスワードまたはリモート デスクトップをリセットする |Microsoft Azure"
	description="リソース マネージャーのデプロイ モデルで作成された Windows VM で管理者のパスワードまたはリモート デスクトップ サービスをリセットします。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/17/2016"
	ms.author="dkshir"/>

# Windows VM でリモート デスクトップ サービスまたはそのログイン パスワードをリセットする方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)].


パスワードを忘れたか、リモート デスクトップ サービスの構成に問題があるために Windows 仮想マシンに接続できない場合は、ローカル管理者パスワードをリセットするか、リモート デスクトップ サービスの構成をリセットしてください。

仮想マシンのデプロイ モデルに応じて、Azure ポータルを使用するか、Azure PowerShell で VM Access 拡張機能を使用できます。PowerShell を使用する場合は、最新の PowerShell を作業コンピューターにインストールし、Azure サブスクリプションにサインインするようにします。詳細な手順については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。


> [AZURE.TIP] インストールされている PowerShell のバージョンを確認するには、`Get-Module azure | format-table version` コマンドを使用します。


## クラシック デプロイ モデルにおける Windows VM

### Azure ポータル

クラシック デプロイ モデルを使用して仮想マシンを作成した場合、[Azure ポータル](https://portal.azure.com)を使用してリモート デスクトップ サービスをリセットできます。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[リモートのリセット]** の順にクリックします。次のページが表示されます。


![RDP 構成のリセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

ローカル管理者アカウントの名前とパスワードをリセットすることもできます。**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Windows 仮想マシン*、**[すべての設定]**、**[パスワードのリセット]** の順にクリックします。次のページが表示されます。

![パスワード リセット ページ](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

新しいユーザー名とパスワードを入力して、**[保存]**をクリックします。

### VMAccess 拡張機能と PowerShell

仮想マシンに VM エージェントがインストールされていることを確認します。VM エージェントを使用するには、VMAccess 拡張機能を使用前にインストールしておく必要があります。次のコマンドを使用して VM エージェントがインストールされていることを確認します。("myCloudService" と "myVM" は、それぞれ実際のクラウド サービスの名前と VM に置き換えます。これらの情報は、パラメーターを指定せずに `Get-AzureVM` を実行します。)

	$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
	write-host $vm.VM.ProvisionGuestAgent

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。**False** が表示される場合は、Azure ブログの[VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)で手順とダウンロード用リンクをご確認ください。

ポータルで仮想マシンを作成した場合、`$vm.GetInstance().ProvisionGuestAgent` を実行して **True** が返されるかどうかを確認します。作成していない場合は、次のコマンドを使用して設定できます。

	$vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のステップで **Set-AzureVMExtension** コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。

#### **ローカル管理者アカウント パスワードのリセット**

現在のローカル管理者アカウント名と新しいパスワードでサインイン資格情報を作成してから、次のように `Set-AzureVMAccessExtension` を実行します。

	$cred=Get-Credential
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

現在のアカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのサインアウトが発行されます。ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

これらのコマンドはリモート デスクトップ サービスの構成もリセットします。

#### **リモート デスクトップ サービスの構成のリセット**

リモート デスクトップ サービスの構成をリセットするには、次のコマンドを実行します。

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 拡張機能によって仮想マシンで次の 2 つのコマンドが実行されます。

a. `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

このコマンドで、組み込みの Windows ファイアウォール グループが有効になり、TCP ポート 3389 を使用するリモート デスクトップの着信トラフィックが許可されます。

b. `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

このコマンドは fDenyTSConnections レジストリ値を 0 に設定します。これでリモート デスクトップ接続が有効になります。


## リソース マネージャー デプロイ モデルにおける Windows VM

現在、Azure ポータルは、Azure Resource Manager を使用して作成された仮想マシンのリモート アクセスと資格情報のリセットをサポートしていません。


### VMAccess 拡張機能と PowerShell

Azure PowerShell 1.0 以降がインストールされ、`Login-AzureRmAccount` コマンドレットを使用してアカウントにサインインしていることを確認します。

#### **ローカル管理者アカウント パスワードのリセット**

管理者パスワードまたはユーザー名をリセットするには、[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell コマンドを使用します。

次のコマンドを使用してローカル管理者アカウントの資格情報を作成します。

	$cred=Get-Credential

現在のアカウントと異なる名前を入力すると、以下の VMAccess 拡張機能コマンドによってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのログオフが発行されます。ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

VM アクセス拡張機能を使用して、次のように新しい資格情報を設定します。

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password


`myRG`、`myVM`、`myVMAccess`、および場所を、自分のセットアップに関連する値に置き換えます。


#### **リモート デスクトップ サービスの構成のリセット**

VM へのリモート アクセスをリセットするには、次のように [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) または Set-AzureRmVMAccessExtension を使用します。(`myRG`, `myVM`、`myVMAccess`、場所は、実際の値で置き換えます。)

	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus

または:<br>

	Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus


> [AZURE.TIP] どちらのコマンドでも、新しい名前の VM アクセス エージェントが仮想マシンに追加されます。同時に VM に存在する VM アクセス エージェント数は 1 つのみです。続けて VM アクセス エージェントのプロパティを設定するには、`Remove-AzureRmVMAccessExtension` または `Remove-AzureRmVMExtension` を使用して以前に設定したアクセス エージェントを削除します。Azure PowerShell バージョン 1.2.2 以降では、`-ForceRerun` オプションを指定した `Set-AzureRmVMExtension` を実行することで、この手順を回避できます。`-ForceRerun` を使用するときに、必ず以前のコマンドで設定したものと同じ名前を VM アクセス エージェントに使用します。


それでも仮想マシンにリモート接続できない場合は、「[Windows を実行する Azure 仮想マシンへの Remote Desktop 接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)」の手順を参照してください。


## その他のリソース

[Azure VM 拡張機能とその機能](virtual-machines-windows-extensions-features.md)

[RDP または SSH を使用した Azure Virtual Machines への接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)

<!---HONumber=AcomDC_0413_2016-->