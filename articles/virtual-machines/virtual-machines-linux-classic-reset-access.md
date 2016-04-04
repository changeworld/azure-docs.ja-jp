<properties
	pageTitle="Azure CLI からの Linux VM のパスワードのリセットとユーザーの追加 |Microsoft Azure"
	description="Azure ポータルまたは CLI から VMAccess 拡張機能を使用して、Linux VM のパスワードと SSH キーのリセット、SSH 構成のリセット、ユーザー アカウントの追加と削除、ディスクの整合性のチェックを実行する方法について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/15/2015"
	ms.author="cynthn"/>

# Linux 用 Azure VMAccess 拡張機能を使用してアクセスのリセット、ユーザーの管理、ディスクのチェックを実行する方法#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。


パスワードを忘れたため、Secure Shell (SSH) キーが正しくないため、または SSH 構成に問題があるために、Azure の Linux 仮想マシンに接続できない場合は、Azure ポータルを使用するか、Azure CLI で VMAccessforLinux 拡張機能を使用して、パスワードまたは SSH キーのリセット、SSH 構成の修正、ディスクの整合性のチェックを行います。

## Azure ポータル

[Azure ポータル](https://portal.azure.com)で SSH の構成をリセットするには、**[参照]**、**[仮想マシン]**、*ご使用の Linux 仮想マシン*、**[リモート アクセスのリセット]** の順にクリックします。たとえば次のようになります。

![](./media/virtual-machines-linux-classic-reset-access/Portal-RDP-Reset-Linux.png)

sudo 特権または [Azure ポータル](https://portal.azure.com)の SSH 公開キーを使用してユーザー アカウントの名前とパスワードをリセットするには、**[参照]**、**[仮想マシン]**、*ご使用の Linux 仮想マシン*、**[すべての設定]**、**[パスワードのリセット]** の順にクリックします。たとえば次のようになります。

![](./media/virtual-machines-linux-classic-reset-access/Portal-PW-Reset-Linux.png)


## Azure CLI および PowerShell

以下のものが必要になります。

- Microsoft Azure Linux エージェント バージョン 2.0.5 またはそれ以降。仮想マシン ギャラリー内のほとんどの Linux イメージには、バージョン 2.0.5 が含まれています。インストールされているバージョンを確認するには、**waagent -version** を実行します。エージェントを更新するには、「[Azure Linux エージェント ユーザー ガイド]」の指示に従ってください。
- Azure コマンド ライン インターフェイス (CLI)。Azure CLI の設定方法の詳細については、[Azure コマンド ライン インターフェイスのインストールと構成](../xplat-cli-install.md)に関するページを参照してください。
- Azure PowerShell。Set-AzureVMExtension コマンドレットでコマンドを使用して、VMAccessForLinux 拡張機能を自動的に読み込み、構成します。Azure PowerShell の設定方法の詳細については、[Azure PowerShell のインストールと構成の方法]に関するページを参照してください。
- 新しいパスワードまたは一連の SSH キー (いずれかをリセットする場合)。SSH の構成をリセットする場合、これらは必要ありません。

### インストールは必要ありません

VMAccess 拡張機能は使用前にインストールする必要はありません。Linux エージェントが仮想マシンにインストールされていれば、**Set-AzureVMExtension** コマンドレットを使用する Azure PowerShell コマンドを実行すると自動的に拡張機能が読み込まれます。

## Azure CLI の使用

Azure CLI を使用すると、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用してコマンドにアクセスできます。拡張機能の詳しい使用方法については、**azure vm extension set –help** を実行します。

Azure CLI を使用すると、次のタスクを実行できます。

+ [パスワードのリセット](#pwresetcli)
+ [SSH キーのリセット](#sshkeyresetcli)
+ [パスワードと SSH キーのリセット](#resetbothcli)
+ [新しい sudo ユーザー アカウントの作成](#createnewsudocli)
+ [SSH 構成のリセット](#sshconfigresetcli)
+ [ユーザーの削除](#deletecli)
+ [VMAccess 拡張機能の状態の表示](#statuscli)
+ [追加されたディスクの整合性のチェック](#checkdisk)
+ [Linux VM での追加されたディスクの修復](#repairdisk)

### <a name="pwresetcli"></a>パスワードのリセット

手順 1. プレースホルダーの値を置き換えて、次の内容を含む PrivateConf.json という名前のファイルを作成しします。

	{
	"username":"currentusername",
	"password":"newpassword",
	"expiration":"2016-01-01"
	}

手順 2. "vmname" を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>SSH キーのリセット

手順 1. プレースホルダーの値を置き換えて、次の内容を含む PrivateConf.json という名前のファイルを作成しします。

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey"
	}

手順 2. "vmname" を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>パスワードと SSH キーのリセット

手順 1. プレースホルダーの値を置き換えて、次の内容を含む PrivateConf.json という名前のファイルを作成しします。

	{
	"username":"currentusername",
	"ssh_key":"contentofsshkey",
	"password":"newpassword"
	}

手順 2. "vmname" を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>新しい sudo ユーザー アカウントの作成

自身のユーザー名を忘れた場合は、VMAccess を使用して、sudo 権限を持つ新しいユーザーを作成できます。この場合、既存のユーザー名とパスワードは変更されません。

パスワードを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「[パスワードのリセット](#pwresetcli)」のスクリプトを使用して、新しいユーザー名を指定します。

SSH キーを使用したアクセス権を持つ新しい sudo ユーザーを作成するには、「[SSH キーのリセット](#sshkeyresetcli)」のスクリプトを使用して、新しいユーザー名を指定します。

[パスワードと SSH キーをリセット](#resetbothcli)して、パスワードと SSH キーの両方を使用したアクセス権を持つ新しいユーザーを作成することもできます。

### <a name="sshconfigresetcli"></a>SSH 構成のリセット

SSH の構成が望ましい状態でない場合は、VM にアクセスできなくなる可能性もあります。VMAccess 拡張機能を使用して、構成を既定の状態にリセットすることができます。そのために必要なのは、"reset\_ssh" キーを "True" に設定することだけです。拡張機能によって SSH サーバーが再起動し、VM 上の SSH ポートが開いて、SSH 構成が既定値にリセットされます。ユーザー アカウント (名前、パスワード、または SSH キー) は変更されません。

> [AZURE.NOTE] リセットされる SSH の構成ファイルは、/etc/ssh/sshd\_config にあります。

手順 1. 次の内容を含む PrivateConf.json という名前のファイルを作成します。

	{
	"reset_ssh":"True"
	}

手順 2. "vmname" を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>ユーザーの削除

VM にログインせずに直接ユーザー アカウントを削除するには、このスクリプトを使用できます。

手順 1. プレースホルダーの値を置き換えて、次の内容を含む PrivateConf.json という名前のファイルを作成します。

	{
	"remove_user":"usernametoremove"
	}

手順 2. "vmname" を使用中の仮想マシンの名前に置き換えて、次のコマンドを実行します。

	azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>VMAccess 拡張機能の状態の表示

VMAccess 拡張機能の状態を表示するには、次のコマンドを実行します。

	azure vm extension get

### <a name='checkdisk'<</a>追加されたディスクの整合性のチェック

Linux 仮想マシンのすべてのディスクに対して fsck を実行するには、次の手順を実行する必要があります。

手順 1: 次の内容を含む PublicConf.json という名前のファイルを作成します。チェック ディスクは、仮想マシンに接続されているディスクをチェックするかどうかを表すブール値を受け取ります。

    {   
    "check_disk": "true"
    }

手順 2: プレースホルダーの値を置き換えて、次のコマンドを実行します。

   azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

### <a name='repairdisk'></a>Linux 仮想マシンでの追加されたディスクの修復

マウントされていないディスクまたはマウント構成エラーが発生したディスクを修復するには、VMAccess 拡張機能を使用して Linux 仮想マシンでマウント構成をリセットします。

手順 1: 次の内容を含む PublicConf.json という名前のファイルを作成します。

    {
    "repair_disk":"true",
    "disk_name":"yourdisk"
    }

手順 2: プレースホルダーの値を置き換えて、次のコマンドを実行します。

    azure vm extension set vm-name VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## Azure PowerShell の使用

**Set-AzureVMExtension** コマンドレットを使用して、VMAccess に変更を加えます。すべてのケースで、クラウド サービス名と仮想マシン名を使用して開始して、仮想マシン オブジェクトを取得し、変数に格納します。

クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで次のコマンドを実行します。引用符内のすべての文字 (< and > を含む) を置き換えます。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての VM の情報が表示されます。


> [AZURE.NOTE] $ で始まるコマンド ラインは、後で PowerShell コマンドで使用する PowerShell 変数を設定します。

Azure クラシック ポータルで仮想マシンを作成する場合は、次の追加のコマンドを実行します。

	$vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のセクションで Set-AzureVMExtension コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。

次に、以下のタスクを実行できます。

+ [パスワードのリセット](#password)
+ [SSH キーのリセット](#SSHkey)
+ [パスワードと SSH キーのリセット](#both)
+ [SSH 構成のリセット](#config)
+ [ユーザーの削除](#delete)
+ [VMAccess 拡張機能の状態の表示](#status)
+ [追加されたディスクの整合性のチェック](#checkdisk)
+ [Linux VM での追加されたディスクの修復](#repairdisk)

### <a name="password"></a>パスワードのリセット

Linux の現在のユーザー名と新しいパスワードを入力し、これらのコマンドを実行します。

	$UserName = "<current Linux account name>"
	$Password = "<new password>"
	$PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version =  "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 既存のユーザー アカウント用のパスワードまたは SSH キーをリセットする場合は、必ず正確なユーザー名を入力してください。別の名前を入力した場合は、VMAccess 拡張機能が新しいユーザー アカウントを作成し、そのアカウントにパスワードを割り当てます。


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

> [AZURE.NOTE] SSH の構成ファイルは、/etc/ssh/sshd\_config にあります。

### <a name="delete"></a>ユーザーの削除

削除するには、Linux ユーザー名を入力し、次のコマンドを実行します。

	$UserName = "<Linux user name to delete>"
	$PrivateConfig = "{"remove_user": "' + $UserName + '"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>VMAccess 拡張機能の状態の表示

VMAccess 拡張機能の状態を表示するには、次のコマンドを実行します。

	$vm.GuestAgentStatus

### <a name="checkdisk"<</a>追加されたディスクの整合性のチェック

fsck ユーティリティを使用してディスクの整合性をチェックするには、次のコマンドを実行します。

	$PublicConfig = "{"check_disk": "true"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

### <a name="checkdisk"<</a>Linux VM での追加されたディスクの修復

fsck ユーティリティを使用してディスクを修復するには、次のコマンドを実行します。

	$PublicConfig = "{"repair_disk": "true", "disk_name": "my_disk"}"
	$ExtensionName = "VMAccessForLinux"
	$Publisher = "Microsoft.OSTCExtensions"
	$Version = "1.*"
	Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PublicConfiguration $PublicConfig | Update-AzureVM

## その他のリソース

[Azure VM 拡張機能とその機能][]

[RDP または SSH を使用した Azure 仮想マシンへの接続][]


<!--Link references-->
[Azure Linux エージェント ユーザー ガイド]: virtual-machines-linux-agent-user-guide.md
[Azure PowerShell のインストールと構成の方法]: ../install-configure-powershell.md
[Azure VM 拡張機能とその機能]: virtual-machines-windows-extensions-features.md
[RDP または SSH を使用した Azure 仮想マシンへの接続]: http://msdn.microsoft.com/library/azure/dn535788.aspx

<!---HONumber=AcomDC_0323_2016-->