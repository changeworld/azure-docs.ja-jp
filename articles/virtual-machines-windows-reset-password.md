<properties 
	pageTitle="Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法" 
	description="PowerShell コマンドを使用して、Windows 仮想マシンのローカル管理者パスワードまたはリモート デスクトップ サービスをすばやくリセットします。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="josephd"/>

# Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法

パスワードを忘れたか、リモート デスクトップ サービスの構成に問題があるために Windows 仮想マシンに接続できない場合は、VMAccess 拡張機能を使用してローカル管理者パスワードをリセットするか、リモート デスクトップ サービスの構成をリセットします。
 
## 要件

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.5 以降。インストールした Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールと構成方法](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409)」を参照してください。 
- 新しいローカル管理者アカウント パスワード。リモート デスクトップ サービスの構成をリセットする場合、これは必要ありません。 
- VM エージェント。 

VMAccess 拡張機能は使用前にインストールする必要はありません。VM エージェントが仮想マシンにインストールされている場合、**Set-AzureVMExtension** コマンドレットを使用する Azure PowerShell コマンドを実行すると自動的に拡張機能が読み込まれます。
 
最初に、VM エージェントがインストールされていることを確認します。クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。引用符内のすべての文字 (< と > を含む) を置き換えます。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-azurevm** を実行します。現在のサブスクリプションのすべての仮想マシンの情報が表示されます。

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。**False** が表示される場合は、Azure ブログの投稿「[VM エージェントと拡張機能 - パート 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)」で手順とダウンロード用リンクをご確認ください。

Azure 管理ポータルで仮想マシンを作成する場合は、次の追加のコマンドを実行します。

	$vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のセクションで Set-AzureVMExtension コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。 

これで、以下のタスクを実行できます。

- ローカル管理者アカウント パスワードのリセット
- リモート デスクトップ サービスの構成のリセット

## ローカル管理者アカウント パスワードのリセット

現在のローカル管理者アカウント名と新しいパスワードを入力し、次のコマンドを実行します。

	$cred=Get-Credential -Message "Type the name of the current local administrator account and the new password."	
	Set-AzureVMAccessExtension -vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- 現在のアカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのログオフが発行されます。
- ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。
 
これらのコマンドはリモート デスクトップ サービスの構成もリセットします。

## リモート デスクトップ サービスの構成のリセット

リモート デスクトップ サービスの構成をリセットするには、次のコマンドを実行します。

	Set-AzureVMAccessExtension -vm $vm | Update-AzureVM

VMAccess 拡張機能によって VM で次の 2 つのコマンドが実行されます。

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	このコマンドで、組み込みの Windows ファイアウォール グループが有効になり、TCP ポート 3389 を使用するリモート デスクトップの着信トラフィックが許可されます。

- **Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0**

	このコマンドは fDenyTSConnections レジストリ値を 0 に設定します。これでリモート デスクトップ接続が有効になります。

このコマンドを実行してもリモート デスクトップのアクセスに関する問題が解決しない場合は、[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を実行してください。 

1.	このページで **[Microsoft Azure IaaS (Windows) 診断パッケージ]** をクリックして、新しい診断セッションを作成します。
2.	**[Azure VM で発生している問題]** ページで、**[Azure VM との RDP 接続 (再起動が必要)]** の問題を選択します。 

詳細については、[Microsoft Azure IaaS (Windows) 診断パッケージについてのナレッジベースの記事](http://support.microsoft.com/kb/2976864)を参照してください。 

Azure IaaS (Windows) 診断パッケージを実行できないか、実行しても問題が解決しない場合は、「[Windows ベースの Azure Virtual Machines へのリモート デスクトップ接続のトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)」を参照してください。


## その他のリソース

[Azure VM 拡張機能と機能](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[RDP または SSH を使用した Azure 仮想マシンへの接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)



<!--HONumber=52-->