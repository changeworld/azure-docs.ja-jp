<properties pageTitle="VMAccess Linux バーチャル マシンを使用する方法" description="Linux 用の VMAccess 拡張機能を使用して、パスワード、SSH キー、および SSH 構成をリセットする方法" services="virtual-machines" documentationCenter="" authors="KBDAzure" manager="timlt" editor=""/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="kathydav"/>

#Linux 仮想マシンの SSH、パスワードをリセットする方法#

パスワード、SSH キーを忘れた、またはSSH の構成に問題のために Linux 仮想マシンに接続できない場合は、VMAccessforLinux 拡張機能を使用してパスワード、SSH キー、または、SSH の構成をリセットします。 


##要件

- Microsoft Azure Linux エージェント バージョン 2.0.5 またはそれ以降。仮想マシン ギャラリー内のほとんどの Linux イメージには、バージョン 2.0.5 が含まれています。インストールされているバージョンを確認するには、 `waagent -version` を実行します。エージェントを更新するには、[Azure Linux エージェント ユーザー ガイド] の指示に従ってください。

- Azure PowerShell モジュール。モジュールには、コマンドを実行して、**VMAccessForLinux** 拡張機能を使用する**Set-AzureVMExtension** コマンドレット が含まれています。モジュールの設定の詳細については、[Azure PowerShell をインストールして構成する方法] を参照してください。

- 新しいパスワードまたは SSH キー (いずれかをリセットする場合)。SSH 構成を修正する場合は、これらは必要ありません。 

##インストールは必要ありません

VMAccess は使用前にインストールする必要はありません。Linux エージェントと Azure モジュールがインストールされていれば、**Set-AzureVMExtension** コマンドレットを呼び出すコマンドを実行すると自動的に拡張機能がロードされます。 

##拡張機能を使用して、パスワード、SSH キーまたは構成をリセットするか、ユーザーを追加します。

**Set-AzureVMExtension** コマンドレットを使用して、VMAccess に変更を加えます。すべてのケースで、クラウド サービス名と仮想マシン名を使用して開始して、仮想マシン オブジェクトを取得し、変数に格納します。   

Azure PowerShell を開き、コマンド プロンプトで次を入力します。必ず、MyServiceName と MyVMName プレース ホルダーを実際の名前に置き換えます。

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

次に、以下のタスクを実行できます。

+ [パスワードのリセット](#password)
+ [SSH キーのリセット](#SSHkey)
+ [パスワードと SSH キーのリセット](#both)
+ [SSH 構成のリセット](#config)

### <a name="password"></a>パスワードのリセット
ユーザー名とパスワードを入力して変数に保存し、次のコマンドで使用できるように単一の変数を作成して値を格納します。

	PS C:\> $UserName = "CurrentName"
	PS C:\> $Password = "NewPassword"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}' 

変数に、名前、発行元、およびバージョン番号を格納します。 

	PS C:\> ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'

変数に格納されているすべての必須の値で、次のコマンドを実行します。

	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 既存のユーザー アカウント用のパスワードまたはの SSH キーをリセットする場合は、必ず正確なユーザー名を入力してください。別の名前を入力した場合は、VMAccess 拡張機能が新しいユーザー アカウントを作成し、そのアカウントにパスワードを割り当てます。

### <a name="SSHkey"></a>SSH キーのリセット

ユーザー名と、新しい SSH 公開キーのパスを入力し、変数に格納します。

	PS C:\> $UserName = "CurrentName"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'

次のコマンドを実行します。

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="both"></a>パスワードと SSH キーのリセット

現在のユーザーの場合、SSH 公開キーで新しいパスワードと新しい証明書のパスを入力して、変数に格納します。 

	PS C:\> $UserName = "CurrentName"	
	PS C:\> $Password = "NewPassword"
	PS C:\> $cert = Get-Content "CertPath"
	PS C:\> $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}' 

次のコマンドを実行します。

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

###  <a name="config"></a>SSH 構成のリセット

SSH の構成にエラーがあると、VM にアクセスできない場合があります。その場合、構成を既定値にリセットして修正できます。これによって、構成 (ユーザー名、パスワード、または SSH キー) のすべての新しいアクセス パラメーターが削除されます。ユーザー アカウントのパスワードまたは SSH キーは変更されません。拡張機能によって SSH サーバーが再起動し、VM 上の SSH ポートが開いて、SSH 構成が既定値にリセットされます。  

構成をリセットすることを示すフラグを設定し、変数に格納します。 
	
	PS C:\> $PrivateConfig = '{"reset_ssh": "True"}' 

次のコマンドを実行します。

	PS C:\> $ExtensionName = 'VMAccessForLinux'
	PS C:\> $Publisher = 'Microsoft.OSTCExtensions'
	PS C:\> $Version =  '1.0'
	PS C:\> Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH の構成ファイルは、/etc/ssh/sshd_config にあります。

## トラブルシューティング

**Set-AzureVMExtension** コマンドレットを使用する場合は、このエラーが発生することがあります。「IaaS VM Access 拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要があります」 

これは、管理ポータルを使用して Linux VM を作成した場合にゲスト エージェントのプロパティの値が「True」に設定されていないために発生します。この問題を解決するには、次のコマンドを実行します。

	PS C:\> $vm = Get-AzureVM -ServiceName 'MyServiceName' -Name 'MyVMName'

	PS C:\> $vm.GetInstance().ProvisionGuestAgent = $true

#その他のリソース
[Azure VM 拡張機能と機能] []

[RDP または SSH を使用する Azure の仮想マシンへの接続] []


<!--Link references-->
[Azure Linux エージェント ユーザー ガイド]: ../virtual-machines-linux-agent-user-guide
[Azure PowerShell をインストールして構成する方法]: ../install-configure-powershell
[Azure VM 拡張機能と機能]: http://msdn.microsoft.com/ja-jp/library/azure/dn606311.aspx
[RDP または SSH を使用する Azure の仮想マシンへの接続]: http://msdn.microsoft.com/ja-jp/library/azure/dn535788.aspx

<!--HONumber=42-->
