<properties 
	pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)" 
	description="Azure VM に Symantec Endpoint Protection をインストールし、構成する方法について説明します。" 
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
	ms.date="1/26/2015" 
	ms.author="kathydav"/>

#How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい仮想マシンに Symantec Endpoint Protection クライアントをインストールして構成する方法を説明します。このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。 

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合は、Endpoint Protection クライアントと同時に VM エージェントをインストールします。VM エージェントがない既存の仮想マシンの場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、「[Symantec Endpoint Protection on Microsoft's Azure platform (Microsoft Azure プラットフォームでの Symantec Endpoint Protection)](http://go.microsoft.com/fwlink/p/?LinkId=403942)」を参照してください。このページには、ライセンス情報へのリンクがあります。また、既に Symantec の顧客である場合には、クライアントをインストールする別の方法の説明もあります。

##新しい仮想マシンに Symantec Endpoint Protection をインストールする

**[ギャラリーから]** オプションを使用して仮想マシンを作成する際に、[Azure 管理ポータル](http://manage.windowsazure.com)で、VM エージェントと Symantec のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Symantec の保護機能を追加する最も簡単な方法です。 

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Symantec のセキュリティ拡張機能をインストールします。 

一般的な手順については、「[Windows Server を実行する仮想マシンの作成](http://go.microsoft.com/fwlink/p/?LinkId=403943)」を参照してください。ウィザードの最後のページで、次の手順を実行します。

1.	[VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスがオンになっている必要があります。

2.	[セキュリティ拡張機能] で **[Symantec Endpoint Protection]** チェック ボックスをオンにします。


	![Install the VM Agent and the Endpoint Protection Client](./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png)

3.	ページの下部にあるチェック マークをオンにして、仮想スケジュールを保存します。

## 既存の仮想マシンに Symantec Endpoint Protection をインストールする

開始する前に、次の条件が必要です。

- Azure PowerShell モジュール Version 0.8.2 以降。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法](http://go.microsoft.com/fwlink/p/?LinkId=320552)」を参照してください。  

- VM エージェント。詳細とダウンロード用リンクについては、ブログ投稿「[VM エージェントと拡張機能 - パート 2](http://go.microsoft.com/fwlink/p/?LinkId=403947)」を参照してください。

既存の仮想マシンに Symantec のセキュリティ拡張機能をインストールするには:

1.	クラウド サービス名と仮想マシン名を取得します。これらがわからない場合には、**Get-AzureVM** コマンドを使用します。現在のサブスクリプションのすべての VM に対するこれらの情報が表示されます。次に、引用符内のすべての文字 (< および > を含む) を置き換えて、以下のコマンドを実行します。

	<p>`$servicename = "<YourServiceName>"`
<p>`$name = "<YourVmName>"`
<p>`$vm = Get-AzureVM -ServiceName $servicename -Name $name`
<p>`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`

2.	Get-AzureVMAvailableExtension コマンドの表示からバージョン プロパティのバージョン番号をメモし、以下のコマンドを実行します。

	<p>`$ver=<version number from the Version property>`
<p>`Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -Version $ver -VM $vm.VM`
<p>`Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1.	仮想マシンにログオンします。
2.	Windows Server 2008 R2 の場合は、**[スタート]、[すべてのプログラム]、[Symantec Endpoint Protection]** の順にクリックします。Windows Server 2012 の場合は、スタート画面で「**Symantec**」と入力して **[Symantec Endpoint Protection]** をクリックします。
3.	必要に応じて、ステータス ウィンドウから更新を適用します。

## その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法]

[拡張機能の管理]

<!--Link references-->
[Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/

[拡張機能の管理]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409



<!--HONumber=42-->
