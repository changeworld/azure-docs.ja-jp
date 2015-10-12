<properties
	pageTitle="Windows VM でパスワードまたはリモート デスクトップをリセットする |Microsoft Azure"
	description="リソース マネージャーのデプロイ モデルで作成された Windows VM で管理者のパスワードまたはリモート デスクトップ サービスをリセットします。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="dkshir"/>

# Windows 仮想マシンのパスワードまたはリモート デスクトップ サービスをリセットする方法

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャーのデプロイ モデルを使用して作成した仮想マシンについて説明します。

パスワードを忘れたか、リモート デスクトップ サービスの構成に問題があるために Windows 仮想マシンに接続できない場合は、Azure プレビュー ポータルまたは VMAccess 拡張機能を使用してローカル管理者パスワードをリセットするか、リモート デスクトップ サービスの構成をリセットします。

## プレビュー ポータル

[プレビュー ポータル](https://portal.azure.com)でリモート デスクトップ サービスをリセットするには、**[すべて参照]**、**[仮想マシン (従来のもの)]**、*ご使用の Windows 仮想マシン*、**[リモート アクセスのリセット]** の順にクリックします。次のページが表示されます。


![](./media/virtual-machines-windows-reset-password/Portal-RDP-Reset-Windows.png)

[プレビュー ポータル](https://portal.azure.com)のローカル管理者アカウントの名前とパスワードをリセットするには、**[すべて参照]**、**[仮想マシン (従来のもの)]**、*ご使用の Windows 仮想マシン*、**[すべての設定]**、**[パスワードのリセット]** の順にクリックします。次のページが表示されます。

![](./media/virtual-machines-windows-reset-password/Portal-PW-Reset-Windows.png)


## VMAccess 拡張機能と PowerShell

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.5 以降。インストールした Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法](http://go.microsoft.com/fwlink/p/?linkid=320552&clcid=0x409)」を参照してください。
- 新しいローカル管理者アカウント パスワード。リモート デスクトップ サービスの構成をリセットする場合、これは必要ありません。
- VM エージェント。

VMAccess 拡張機能は使用前にインストールする必要はありません。VM エージェントが仮想マシンにインストールされていれば、**Set-AzureVMExtension** コマンドレットを使用する Azure PowerShell コマンドを実行すると自動的に拡張機能が読み込まれます。

最初に、VM エージェントがインストールされていることを確認します。クラウド サービス名と仮想マシン名を追加して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。引用符内のすべての文字 (< and > を含む) を置き換えます。

	$csName = "<cloud service name>"
	$vmName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $csName -Name $vmName
	write-host $vm.VM.ProvisionGuestAgent

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての仮想マシンの情報が表示されます。

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。**False** が表示される場合は、Azure ブログの[VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409)で手順とダウンロード用リンクをご確認ください。

Azure ポータルで仮想マシンを作成する場合は、次の追加のコマンドを実行します。

	$vm.GetInstance().ProvisionGuestAgent = $true

このコマンドは、次のセクションで **Set-AzureVMExtension** コマンドを実行する場合、"IaaS VM アクセス拡張機能を設定する前に、VM オブジェクトでゲスト エージェントのプロビジョニングを有効にする必要がある" というエラーを防ぐことができます。

これで、以下のタスクを実行できます。

- ローカル管理者アカウント パスワードをリセットする。
- リモート デスクトップ サービスの構成をリセットする。

## ローカル管理者アカウント パスワードのリセット

現在のローカル管理者アカウント名と新しいパスワードを追加し、次のコマンドを実行します。

	$cred=Get-Credential –Message "Type the name of the current local administrator account and the new password."
	Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

- 現在のアカウントと異なる名前を入力すると、VMAccess 拡張機能によってローカル管理者アカウントの名前が変更されて、そのアカウントにパスワードが割り当てられます。さらに、リモート デスクトップのログオフが発行されます。
- ローカル管理者アカウントが無効になっている場合は、VMAccess 拡張機能によって有効に設定されます。

これらのコマンドはリモート デスクトップ サービスの構成もリセットします。

## リモート デスクトップ サービスの構成のリセット

リモート デスクトップ サービスの構成をリセットするには、次のコマンドを実行します。

	Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

VMAccess 拡張機能によって仮想マシンで次の 2 つのコマンドが実行されます。

- **netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes**

	このコマンドで、組み込みの Windows ファイアウォール グループが有効になり、TCP ポート 3389 を使用するリモート デスクトップの着信トラフィックが許可されます。

- **Set-ItemProperty -Path 'HKLM:\\System\\CurrentControlSet\\Control\\Terminal Server' -name "fDenyTSConnections" -Value 0**

	このコマンドは fDenyTSConnections レジストリ値を 0 に設定します。これでリモート デスクトップ接続が有効になります。

このコマンドを実行してもリモート デスクトップのアクセスに関する問題が解決しない場合は、[Azure IaaS (Windows) 診断パッケージ](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)を実行してください。

1.	診断パッケージで、**[Microsoft Azure IaaS (Windows) 診断パッケージ]** をクリックして、新しい診断セッションを作成します。
2.	[**Azure VM で発生している問題**] ページで、[**Azure VM との RDP 接続 (再起動が必要)**] の問題を選択します。

詳細については、「[Microsoft Azure IaaS (Windows) の診断パッケージ](http://support.microsoft.com/kb/2976864)」ナレッジベース記事を参照してください。

Azure IaaS (Windows) 診断パッケージを実行できないか、実行しても問題が解決しない場合は、「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)」を参照してください。


## その他のリソース

[Azure VM 拡張機能とその機能](http://msdn.microsoft.com/library/azure/dn606311.aspx)

[RDP または SSH を使用した Azure 仮想マシンへの接続](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)

<!---HONumber=Oct15_HO1-->