<properties 
	pageTitle="VMAccess Linux バーチャル マシンを使用する方法" 
	description="Linux 用の VMAccess 拡張機能を使用して、パスワードと SSH 鍵をリセットし、SSH の構成を再送信して、Linux ユーザーを削除する方法" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/17/2015" 
	ms.author="kathydav"/>

# Linux 仮想マシンのパスワードまたは SSH をリセットする方法 #

パスワードを忘れた、Sechre Shell (SSH) キーが正しくない、または SSH の構成に問題があるために Linux 仮想マシンに接続できない場合は、VMAccessforLinux 拡張機能を使用してパスワード、SSH キーをリセットするか、SSH の構成の問題を解決します。 

## 要件

- Microsoft Azure Linux エージェント バージョン 2.0.5 またはそれ以降。仮想マシン ギャラリー内のほとんどの Linux イメージには、バージョン 2.0.5 が含まれています。インストールされているバージョンを確認するには、`waagent -version` を実行します。エージェントを更新するには、[Azure Linux エージェント ユーザー ガイド] の指示に従ってください。
- Azure PowerShell。**Set-AzureVMExtension** コマンドレットでコマンドを使用して、**VMAccessForLinux** 拡張機能を自動的に読み込み、構成します。Azure PowerShell の設定方法の詳細については、「[Azure PowerShell のインストールおよび構成方法]」を参照してください。
- 新しいパスワードまたは一連の SSH キー (いずれかをリセットする場合)。SSH の構成をリセットする場合、これらは必要ありません。 

## インストールは必要ありません

VMAccess 拡張機能は使用前にインストールする必要はありません。Linux エージェントが仮想マシンにインストールされていれば、**Set-AzureVMExtension** コマンドレットを使用する Azure PowerShell コマンドを実行すると自動的に拡張機能が読み込まれます。 

## 拡張機能を使用して、パスワード、SSH キーまたは SSH 構成をリセットするか、ユーザーを削除します。

**Set-AzureVMExtension** コマンドレットを使用して、VMAccess に変更を加えます。すべてのケースで、クラウド サービス名と仮想マシン名を使用して開始して、仮想マシン オブジェクトを取得し、変数に格納します。 

クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで次のコマンドを実行します。引用符内のすべての文字 (< および > を含む) を置換します。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての VM の情報が表示されます。


> [AZURE.NOTE] $ で始まるコマンド ラインは、後で PowerShell コマンドで使用する PowerShell 変数を設定します。

Azure 管理ポータルで仮想マシンを作成する場合は、次の追加のコマンドを実行します。

	$vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のセクションで Set-AzureVMExtension コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。 

次に、以下のタスクを実行できます。

+ [パスワードのリセット](#password)
+ [SSH キーのリセット](#SSHkey)
+ [パスワードと SSH キーのリセット](#both)
+ [SSH 構成のリセット](#config)
+ [ユーザーの削除](#delete)

### <a name="password"></a>パスワードのリセット

Linux の現在のユーザー名と新しいパスワードを入力し、これらのコマンドを実行します。

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 既存のユーザー アカウント用のパスワードまたはの SSH キーをリセットする場合は、必ず正確なユーザー名を入力してください。別の名前を入力した場合は、VMAccess 拡張機能が新しいユーザー アカウントを作成し、そのアカウントにパスワードを割り当てます。


### <a name="SSHKey"></a>SSH キーのリセット

Linux の現在のユーザー名と、SSH キーを含む証明書へのパスを入力し、これらのコマンドを実行します。

	$UserName = "<current Linux user name>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>パスワードと SSH キーのリセット

Linux の現在のユーザー名、新しいパスワード、および SSH キーを含む証明書へのパスを入力し、これらのコマンドを実行します。

	$UserName = "<current Linux user name>"
	$Password = "<new password>"
	$Cert = Get-Content "<certificate path>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>SSH 構成のリセット

SSH の構成にエラーがあると、仮想マシンにアクセスできない場合があります。SSH の構成を既定の状態にリセットして修正することができます。これにより、ユーザー名、パスワード、SSH キーなど、構成ですべての新しいアクセス パラメーターが削除されますが、ユーザー アカウントのパスワードまたは SSH キーは変わりません。拡張機能によって SSH サーバーが再起動し、仮想マシン上の SSH ポートが開いて、SSH 構成が既定値にリセットされます。 

これらのコマンドを実行します。

	$PrivateConfig = '{"reset_ssh": "True"}' 
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH の構成ファイルは、/etc/ssh/sshd_config にあります。

### <a name="delete"></a> ユーザーの削除

削除するには、Linux ユーザー名を入力し、次のコマンドを実行します。

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

## その他のリソース

[Azure VM 拡張機能と機能][]

[RDP または SSH を使用する Azure 仮想マシンへの接続] []


<!--Link references-->
[Azure Linux エージェント ユーザー ガイド]: ../virtual-machines-linux-agent-user-guide
[Azure PowerShell のインストールおよび構成方法]: ../install-configure-powershell
[Azure VM 拡張機能と機能]: http://msdn.microsoft.com/library/azure/dn606311.aspx
[RDP または SSH を使用する Azure 仮想マシンへの接続]: http://msdn.microsoft.com/library/azure/dn535788.aspx






<!--HONumber=45--> 
