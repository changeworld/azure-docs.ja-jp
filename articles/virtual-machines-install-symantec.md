<properties title="How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)" pageTitle="How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)" description="Azure VM に Symantec Endpoint Protection をインストールし、構成する方法について説明します。" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="7/16/2014" ms.author="kathydav" />

# How to install and configure Symantec Endpoint Protection on an Azure VM (Azure VM に Symantec Endpoint Protection をインストールし、構成する方法)

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい仮想マシンに Symantec Endpoint Protection クライアントをインストールして構成する方法を説明します。このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合は、Endpoint Protection クライアントと同時に VM エージェントをインストールします。VM エージェントがない既存の仮想マシンの場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、「[Symantec Endpoint Protection on Microsoft's Azure platform (Microsoft Azure プラットフォームでの Symantec Endpoint Protection)][Symantec Endpoint Protection on Microsoft's Azure platform (Microsoft Azure プラットフォームでの Symantec Endpoint Protection)]」を参照してください。このページには、ライセンス情報へのリンクがあります。また、既に Symantec の顧客である場合には、クライアントをインストールする別の方法の説明もあります。

## 新しい仮想マシンに Symantec Endpoint Protection をインストールする

**[ギャラリーから]** オプションを使用して仮想マシンを作成する際に、[Azure 管理ポータル][Azure 管理ポータル]で、VM エージェントと Symantec のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Symantec の保護機能を追加する最も簡単な方法です。

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Symantec のセキュリティ拡張機能をインストールします。

一般的な手順については、「[Windows Server を実行する仮想マシンの作成][Windows Server を実行する仮想マシンの作成]」を参照してください。ウィザードの最後のページで、次の手順を実行します。

1.  [VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスをオンにします。

2.  [セキュリティ拡張機能] で **[Symantec Endpoint Protection]** チェック ボックスをオンにします。

    ![Install the VM Agent and the Endpoint Protection Client][Install the VM Agent and the Endpoint Protection Client]

3.  ページの下部にあるチェック マークをオンにして、仮想スケジュールを保存します。

## 既存の仮想マシンに Symantec Endpoint Protection をインストールする

既存の仮想マシンに Endpoint Protection Agent をインストールするには、次の条件が必要です。

-   Azure PowerShell モジュール Version 0.8.2 以降。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

-   VM エージェント。詳細とダウンロード用リンクについては、ブログ投稿「[VM エージェントと拡張機能 - パート 2][VM エージェントと拡張機能 - パート 2]」を参照してください。

Azure PowerShell セッションを開き、次のコマンドを実行します。プレースホルダーは、必ず実際の値 (MyServiceName など) に置き換えてください。

1.  クラウド サービス名、仮想マシン名、および VM を取得し、それぞれを変数に保存して、次のコマンドで使用できるようにします。

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] クラウド サービス名と VM 名がわからない場合には、Get-AzureVM を実行します。現在のサブスクリプションのすべての VM に対するこれらの情報が表示されます。

2.  仮想マシンに Symantec Endpoint Protection Agent を追加します。最新バージョンをインストールするには、次のコマンドを実行します。

    `Set-AzureVMExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection -VM $vm.VM`

    > [WACOM.NOTE] 特定のバージョンをインストールする場合は、次のコマンドを実行して、利用できるバージョンの一覧を表示します。`Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection`
    > 次に、Set-AzureVMExtension を実行するときに、目的のバージョンのパラメーターを含めます。

3.  次のコードで VM を更新します。これによって Symantec Endpoint Protection Agent がインストールされます。

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法][Windows Server が実行されている仮想マシンにログオンする方法]

[拡張機能の管理][拡張機能の管理]

<!--Link references-->

  [Symantec Endpoint Protection on Microsoft's Azure platform (Microsoft Azure プラットフォームでの Symantec Endpoint Protection)]: http://go.microsoft.com/fwlink/p/?LinkId=403942
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Windows Server を実行する仮想マシンの作成]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Endpoint Protection Client]: ./media/virtual-machines-install-symantec/InstallVMAgentandSymantec.png
  [Azure PowerShell のインストールおよび構成方法]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM エージェントと拡張機能 - パート 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
  [拡張機能の管理]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
