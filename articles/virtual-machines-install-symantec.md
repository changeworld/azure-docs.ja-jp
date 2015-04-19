<properties 
	pageTitle="Azure VM に Symantec Endpoint Protection をインストールし、構成する方法" 
	description="Azure の新規または既存の VM における、Symantec Endpoint Protection のセキュリティ拡張機能のインストールと構成について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="kathydav"/>

# Azure VM に Symantec Endpoint Protection をインストールし、構成する方法

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい仮想マシンに Symantec Endpoint Protection クライアントをインストールして構成する方法を説明します。このクライアントには、ウイルスとスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。 

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合は、Endpoint Protection クライアントと同時に VM エージェントをインストールします。VM エージェントがない既存の仮想マシンの場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、[Microsoft の Azure プラットフォーム上の Symantec Endpoint Protection](http://go.microsoft.com/fwlink/p/?LinkId=403942) に関するページをご覧ください。このページには、ライセンス情報へのリンクがあります。また、既に Symantec の顧客である場合には、クライアントをインストールする別の方法の説明もあります。

## 新しい仮想マシンに Symantec Endpoint Protection をインストールする

**[ギャラリーから]** オプションを使用して仮想マシンを作成する際に、[Azure 管理ポータル](http://manage.windowsazure.com)で、VM エージェントと Symantec のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Symantec の保護機能を追加する最も簡単な方法です。 

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Symantec のセキュリティ拡張機能をインストールします。 

一般的な手順については、「[Windows を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」をご覧ください。ウィザードの最後のページで、次の手順を実行します。

1.	[VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスがオンになっている必要があります。

2.	[セキュリティ拡張機能] で **[Symantec Endpoint Protection]** チェック ボックスをオンにします。


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	ページの下部にあるチェック マークをオンにして、仮想スケジュールを保存します。

## 既存の仮想マシンに Symantec Endpoint Protection をインストールする

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.2 以降。インストールした Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールと構成方法](install-configure-powershell.md)」をご覧ください。  

- VM エージェント。 

最初に、VM エージェントがインストールされていることを確認します。クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。引用符内のすべての文字 (< と > を含む) を置換します。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName 
	write-host $vm.VM.ProvisionGuestAgent

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-azurevm** を実行します。現在のサブスクリプションのすべての仮想マシンの情報が表示されます。

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。**False** が表示される場合は、Azure ブログの投稿「[VM エージェントと拡張機能 - パート 2](http://go.microsoft.com/fwlink/p/?LinkId=403947)」で手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合、これらのコマンドを実行して Symantec Endpoint Protection エージェントをインストールします。

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection
	Set-AzureVMExtension -Publisher Symantec -Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1.	仮想マシンにログオンします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)」をご覧ください。
2.	Windows Server 2008 R2 の場合は、**[スタート]、[Symantec Endpoint Protection]** の順にクリックします。Windows Server 2012 または Windows Server 2012 R2 で、スタート画面から、「**Symantec**」と入力し、**[Symantec Endpoint Protection]** をクリックします。
3.	**[状態: Symantec Endpoint Protection]** ウィンドウの **[状態]** タブで、更新を適用するか、必要に応じて再起動します。

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)

[拡張機能の管理](https://msdn.microsoft.com/library/dn606311.aspx)


<!--HONumber=47-->
