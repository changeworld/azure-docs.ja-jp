<properties
	pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)"
	description="Azure VM に Trend Micro Security をインストールし、構成する方法について説明します。"
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
	ms.date="07/15/2015"
	ms.author="dkshir"/>


# How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい VM に Trend Micro Deep Security as a Service をインストールして構成する方法を説明します。Deep Security as a Service には、ウイルス対策、ファイアウォール、侵入防止システム、変更監視などの保護機能が含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合には、Deep Security Agent と同時に VM エージェントをインストールします。既存の仮想マシンで、VM エージェントがインストールされていない場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Trend Micro のオンプレミスのソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、Trend Micro ブログの Deep Security 用の [Microsoft Azure VM エージェント拡張機能](http://go.microsoft.com/fwlink/p/?LinkId=403945)に関する投稿を参照してください。

## 新しい仮想マシンに Deep Security Agent をインストールする

[Azure 管理ポータル](http://manage.windowsazure.com)を使用すると、**From Gallery** オプションを使用して仮想マシンを作成する際に、VM エージェントと Trend Micro のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Trend Micro の保護機能を追加する最も簡単な方法です。

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Trend Micro のセキュリティ拡張機能をインストールします。一般的な手順については、「[Windows Server を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)」を参照してください。ウィザードの最後のページで、次の手順を実行します。

1.	[VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスをオンにします。

2.	[セキュリティ拡張機能] で **[Trend Micro Deep Security エージェント]** チェック ボックスをオンにします。

	![Install the VM Agent and the Deep Security Agent](./media/virtual-machines-install-trend/InstallVMAgentandTrend.png)

3.	チェック マークをクリックして、仮想マシンを作成します。

## 既存の仮想マシンに Deep Security Agent をインストールする

このインストールには、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.2 以降がローカル コンピューターにインストールされていること。インストールした Azure PowerShell のバージョンは、**Get-Module azure | format-table version** コマンドで確認できます。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)」を参照してください。

- VM エージェントがターゲットの仮想マシンにインストールされていること。

最初に、VM エージェントがインストールされていることを確認します。クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。引用符内のすべての文字 (< and > を含む) を置き換えます。

	$CSName = "<cloud service name>"
	$VMName = "<virtual machine name>"
	$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
	write-host $vm.VM.ProvisionGuestAgent

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての仮想マシンの情報が表示されます。

**write-host** コマンドによって **True** が返された場合は、VM エージェントがインストールされています。**False** が返された場合は、Azure ブログの[VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?LinkId=403947) で、手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合は、次のコマンドを実行します。

	$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

	Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## 次のステップ

エージェントがインストールされると、起動までに数分かかります。起動後、仮想マシン上で Deep Security をアクティブにする必要があります。これは Deep Security Manager で管理できるようにするためです。詳細については、次をご覧ください。

- このソリューションに関する Trend の記事「[Instant-On Cloud Security for Microsoft Azure (Microsoft Azure 用の Instant-On クラウド セキュリティ)](http://go.microsoft.com/fwlink/?LinkId=404101)」
- 仮想マシンを構成するための [Windows PowerShell サンプル スクリプト](http://go.microsoft.com/fwlink/?LinkId=404100)
- 上記サンプルについての[説明](http://go.microsoft.com/fwlink/?LinkId=404099)

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法]

[Azure VM 拡張機能とその機能]


<!--Link references-->
[Windows Server が実行されている仮想マシンにログオンする方法]: virtual-machines-log-on-windows-server.md
[Azure VM 拡張機能とその機能]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

<!---HONumber=July15_HO4-->