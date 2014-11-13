<properties title="Azure VM に Trend をインストールして構成する方法" pageTitle="How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)" description="Azure VM に Trend Micro Security をインストールし、構成する方法について説明します。" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kathydav" />

# How to install and configure Trend Micro Deep Security as a Service on an Azure VM (Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法)

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい仮想マシンに Trend Micro Deep Security as a Service をインストールして構成する方法を説明します。Deep Security as a Service には、ウイルス対策、ファイアウォール、侵入防止システム、変更監視などの保護機能が含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。新しい仮想マシンの場合には、Deep Security Agent と同時に VM エージェントをインストールします。既存の仮想マシンで、VM エージェントがインストールされていない場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。この記事では、この両方の場合について説明します。

Trend のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。このソリューションの詳細については、ブログ投稿「[Microsoft Azure VM Agent Extension For Deep Security (Deep Security 用の Microsoft Azure VM エージェント拡張機能)][Microsoft Azure VM Agent Extension For Deep Security (Deep Security 用の Microsoft Azure VM エージェント拡張機能)]」を参照してください。

## 新しい仮想マシンに Deep Security Agent をインストールする

**[ギャラリーから]** オプションを使用して仮想マシンを作成する際に、[Azure 管理ポータル][Azure 管理ポータル]で、VM エージェントと Trend のセキュリティ拡張機能をインストールできます。仮想マシンを 1 つだけ作成する場合には、この方法が Trend の保護機能を追加する最も簡単な方法です。

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。ウィザードの最後のページで、VM エージェントと Trend のセキュリティ拡張機能をインストールします。一般的な手順については、「[Windows Server を実行する仮想マシンの作成][Windows Server を実行する仮想マシンの作成]」を参照してください。ウィザードの最後のページで、次の手順を実行します。

1.  [VM エージェント] で **[VM エージェントをインストールする]** チェック ボックスをオンにします。

2.  [セキュリティ拡張機能] で **[Trend Micro Deep Security エージェント]** チェック ボックスをオンにします。

3.  チェック マークをクリックして、仮想マシンを作成します。

    ![Install the VM Agent and the Deep Security Agent][Install the VM Agent and the Deep Security Agent]

## 既存の仮想マシンに Deep Security Agent をインストールする

このインストールには、次の条件が必要です。

-   Azure PowerShell モジュール Version 0.8.2 以降。最新バージョンの説明とダウンロード用リンクについては、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

-   VM エージェント。詳細とダウンロード用リンクについては、ブログ投稿「[VM エージェントと拡張機能 - パート 2][VM エージェントと拡張機能 - パート 2]」を参照してください。

Azure PowerShell セッションを開き、次のコマンドを実行します。プレースホルダーは、必ず実際の値 (MyServiceName など) に置き換えてください。

1.  クラウド サービス名、仮想マシン名、および VM を取得し、それぞれを変数に保存して、次のコマンドで使用できるようにします。

    `$servicename = MyServiceName`

    `$name = MyVmName`

    `$vm = Get-AzureVM -ServiceName $servicename -Name $name`

    > [WACOM.NOTE] クラウド サービス名と VM 名がわからない場合には、Get-AzureVM を実行します。現在のサブスクリプションのすべての VM に対するこれらの情報が表示されます。

2.  仮想マシンに Deep Security Agent を追加します。

    `Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA -VM $vm.VM`

    > [WACOM.NOTE] 特定のバージョンをインストールする場合は、次のコマンドを実行して、利用できるバージョンの一覧を表示します。`Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA`。次に、Set-AzureVMExtension を実行するときに、目的のバージョンのパラメーターを含めます。

3.  次のコードで VM を更新します。これによって Deep Security Agent がインストールされます。

    `Update-AzureVM -ServiceName $servicename -Name $name -VM $vm.VM`

## 次のステップ

エージェントがインストールされると、起動までに数分かかります。起動後、仮想マシン上で Deep Security をアクティブにする必要があります。これは Deep Security Manager で管理できるようにするためです。次の記事を参照してください。

-   このソリューションに関する Trend の記事「[Instant-On Cloud Security for Microsoft Azure (Microsoft Azure 用の Instant-On クラウド セキュリティ)][Instant-On Cloud Security for Microsoft Azure (Microsoft Azure 用の Instant-On クラウド セキュリティ)]」
-   仮想マシンを構成するための [Windows PowerShell サンプル スクリプト][Windows PowerShell サンプル スクリプト]
-   上記サンプルについての[説明][説明]

## その他のリソース

[Windows Server が実行されている仮想マシンにログオンする方法][Windows Server が実行されている仮想マシンにログオンする方法]

[拡張機能の管理][拡張機能の管理]

<!--Link references-->

  [Microsoft Azure VM Agent Extension For Deep Security (Deep Security 用の Microsoft Azure VM エージェント拡張機能)]: http://go.microsoft.com/fwlink/p/?LinkId=403945
  [Azure 管理ポータル]: http://manage.windowsazure.com
  [Windows Server を実行する仮想マシンの作成]: http://go.microsoft.com/fwlink/p/?LinkId=403943
  [Install the VM Agent and the Deep Security Agent]: ./media/virtual-machines-install-trend/InstallVMAgentandTrend.png
  [Azure PowerShell のインストールおよび構成方法]: http://go.microsoft.com/fwlink/p/?LinkId=320552
  [VM エージェントと拡張機能 - パート 2]: http://go.microsoft.com/fwlink/p/?LinkId=403947
  [Instant-On Cloud Security for Microsoft Azure (Microsoft Azure 用の Instant-On クラウド セキュリティ)]: http://go.microsoft.com/fwlink/?LinkId=404101
  [Windows PowerShell サンプル スクリプト]: http://go.microsoft.com/fwlink/?LinkId=404100
  [説明]: http://go.microsoft.com/fwlink/?LinkId=404099
  [Windows Server が実行されている仮想マシンにログオンする方法]: ../virtual-machines-log-on-windows-server/
  [拡張機能の管理]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
