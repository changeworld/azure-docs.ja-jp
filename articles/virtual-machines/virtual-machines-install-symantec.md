<properties
	pageTitle="VM に Symantec Endpoint Protection をインストールする | Microsoft Azure"
	description="クラシック デプロイメント モデルで作成された新しいまたは既存の Azure VM に Symantec Endpoint Protection のセキュリティ拡張機能をインストールして構成する方法を説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2015"
	ms.author="dkshir"/>

# How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメントモデルで作成された仮想マシンにアプリケーションをインストールする方法を説明します。

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい VM に Symantec Endpoint Protection クライアントをインストールして構成する方法を説明します。このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合は、Endpoint Protection クライアントと同時に VM エージェントをインストールします。VM エージェントがない既存の仮想マシンの場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、「[Symantec Endpoint Protection on Microsoft's Azure platform (Microsoft Azure プラットフォームでの Symantec Endpoint Protection)][Symantec]」を参照してください。このページには、Symantec の顧客向けのライセンス情報とクライアントをインストールする方法の説明へのリンクもあります。

## 新しい仮想マシンに Symantec Endpoint Protection をインストールする

[[ギャラリーから]][Portal] オプションを使用して仮想マシンを作成する際に、**Azure 管理ポータル**で、VM エージェントと Symantec のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Symantec の保護機能を追加する最も簡単な方法です。

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Symantec のセキュリティ拡張機能をインストールします。

一般的な手順については、「[Windows Server を実行する仮想マシンの作成][Create]」を参照してください。ウィザードの最後のページで、次の手順を実行します。

1.	[VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスがオンになっている必要があります。

2.	[セキュリティ拡張機能] で **[Symantec Endpoint Protection]** チェック ボックスをオンにします。


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	ページの下部にあるチェック マークをオンにして、仮想マシンを保存します。

## 既存の仮想マシンに Symantec Endpoint Protection をインストールする

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.2 以降がコンピューター上に存在すること。インストールした Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法][PS]」を参照してください。Azure サブスクリプションへのログインを確認してください。

- Azure 仮想マシンで実行されている VM エージェント。

最初に、VM エージェントが仮想マシンに既にインストールされていることを確認します。クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。引用符内のすべての文字 (< and > を含む) を置き換えます。

> [AZURE.TIP]クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての仮想マシンの名前が表示されます。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。**False** が表示される場合は、Azure ブログの[VM エージェントと拡張機能に関する記事のパート 2][Agent] で、手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合、これらのコマンドを実行して Symantec Endpoint Protection エージェントをインストールします。

	$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

	Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1.	仮想マシンにログオンします。詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][Logon]」を参照してください。
2.	Windows Server 2008 R2 の場合は、**[スタート]、[Symantec Endpoint Protection]** の順にクリックします。Windows Server 2012 または Windows Server 2012 R2 で、スタート画面から、「**Symantec**」と入力し、**[Symantec Endpoint Protection]** をクリックします。
3.	**[状態: Symantec Endpoint Protection]** ウィンドウの **[状態]** タブで、更新を適用するか、必要に応じて再起動します。

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[Azure VM 拡張機能とその機能][Ext]


<!--Link references-->
[Symantec]: http://go.microsoft.com/fwlink/p/?LinkId=403942

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-log-on-windows-server.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493

<!---HONumber=Oct15_HO1-->