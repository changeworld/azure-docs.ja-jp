<properties linkid="manage-services-networking-active-directory-forest" urlDisplayName="Active Directory フォレスト" pageTitle="Windows Azure ネットワークでの Active Directory フォレストのインストール" metaKeywords="" description="Windows Azure の仮想ネットワーク上の仮想マシン (VM) に新しい Active Directory フォレストを作成する方法を説明したチュートリアルです。" metaCanonical="" services="active-directory,virtual-network" documentationCenter="" title="Windows Azure での新しい Active Directory フォレストのインストール" authors=""  solutions="" writer="" manager="" editor=""  />




#Windows Azure での新しい Active Directory フォレストのインストール

このチュートリアルでは、[Windows Azure の仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)上の仮想マシン (VM) に新しい Active Directory フォレストを作成する手順について説明します。このチュートリアルでは、VM の仮想ネットワークが会社のネットワークに接続されません。Windows Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Windows Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン](http://msdn.microsoft.com/en-us/library/windowsazure/jj156090.aspx)」を参照してください。

##目次##

* [前提条件](#Prerequisites)
* [手順 1: 最初の仮想マシン (VM) を作成する](#Step1)
* [手順 2: 追加ディスクを VM に接続する](#Step2)
* [手順 3: Active Directory ドメイン サービスをインストールする](#Step3)
* [手順 4: インストールを検証する](#Step4)
* [手順 5: ドメイン コントローラーをバックアップする](#Step5)
* [手順 6: 起動時にドメインに参加させる仮想マシンをプロビジョニングする](#Step6)


<h2><a id="Prerequisites"></a>前提条件</h2>

Windows Azure の仮想マシンに Active Directory ドメイン サービス (AD DS) をインストールする前に、以下の方法のどれかを使用して仮想ネットワークを作成する必要があります。

-	**別のネットワークに "接続しない"** 仮想ネットワークを作成する。**次の手順を実行します。
1.  最初に、[Windows Azure で仮想ネットワークを作成します](http://www.windowsazure.com/en-us/manage/services/networking/create-a-virtual-network/)。
2.  次に、以下のチュートリアルで説明する手順を使用して AD DS をインストールします。
	
-	内部設置型の Active Directory 環境などの**別のネットワークに "接続する"** 仮想ネットワークを作成する**。次の手順を実行します。
1.	最初に、[クロスプレミス接続用の仮想ネットワークを作成します](../cross-premises-connectivity/)。
2.	次に、[仮想ネットワークに仮想マシンを追加します](../add-a-vm-to-a-virtual-network/)。
3.	最後に、「[Install a Replica Active Directory Domain Controller in Windows Azure Virtual Networks (Windows Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール)](../replica-domain-controller/)」の手順に従って AD DS をインストールします。

<h2><a id="Step1"></a>手順 1: 最初の仮想マシン (VM) を作成する</h2>

最初の VM は、Windows Azure の管理ポータルか Windows Azure PowerShell を使用して作成できます。

<h3>Windows Azure の管理ポータルを使用して VM を作成するには</h3>
1.	**[新規]**、**[コンピューティング]**、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

	![ギャラリーから VM を作成][create-vm]

2.	イメージを選択します。

	![VM イメージ][vm-image]

3.	新しい VM の名前、ユーザー名、およびパスワードを入力し、バージョンのリリース日とサイズを選択します。

	![VM 構成][vm-configuration]

4.	仮想ネットワークとサブネットを選択し、他の既定値を受け入れます。

	![VM サブネット][vm-subnet]

5.	既定のエンドポイントを受け入れ、チェック マークをオンにしてウィザードを完了します。

	![VM ポート][vm-ports]

<h3>Windows Azure PowerShell を使用して VM を作成するには</h3>

1.	アフィニティ グループと同じリージョンにストレージ アカウントを作成します。アフィニティ グループのリージョンを確認するには、**[ネットワーク]**、**[アフィニティ グループ]** の順にクリックします。ストレージ アカウントを作成するには、以下の手順を実行します。

	a. **[データ サービス]**、**[ストレージ]**、**[簡易作成]** の順にクリックします。

	b. [URL] で、ストレージ アカウントの名前を入力し、**[リージョン/アフィニティ グループ]** でアフィニティ グループのリージョンを選択します ([米国西部] など)。ストレージ アカウントのリージョンを選択することで、仮想ネットワークのどのアフィニティ グループでもそのストレージ アカウントを使用できるようになります。

2.	[Windows Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx) をインストールします。AD DS をインストールする VM は、サービス復旧後もドメイン コントローラーの DNS クライアント設定が残るようにするために、Windows Azure PowerShell を使用して作成する必要があります。Windows Azure PowerShell のインストールには、Microsoft .NET Framework 4.5 と Windows Management Framework 3.0 のインストールが必要です。まだインストールされていない場合、インストールを完了するにはコンピューターを再起動する必要があります。

	a. [https://www.windowsazure.com/en-us/](https://www.windowsazure.com/en-us/) に移動します。

	b. **[ダウンロード]**、**[コマンドライン ツール]**、**[Windows Azure PowerShell]** の順にクリックします。

	c. **[実行]** をクリックします。**[ユーザー アカウント制御]** ダイアログ ボックスが表示されたら、**[はい]** をクリックします。

	d. **[インストール]** をクリックして、インストール ウィザードを進みます。**[同意する]** をクリックし、ウィザードの処理が終了したら、**[完了]** をクリックします。

	e.	**[終了]** をクリックして Web Platform Installer 4.0 を閉じます。

3.	Windows 7 を実行している場合は、**[スタート]** ボタンをクリックし、**[すべてのプログラム]**、**[Windows Azure]** の順にクリックします。次に **[Windows Azure PowerShell]** を右クリックして **[管理者として実行]** をクリックします。**[ユーザー アカウント制御]** ダイアログ ボックスが表示されたら、**[はい]** をクリックします。Windows 8 を実行している場合は、**[スタート]** をクリックし、**[検索]** ボックスに「Windows Azure PowerShell」と入力して Enter キーを押します。

4.	Windows Azure PowerShell で、次のコマンドレットを実行し、「**Y**」と入力してコマンドレットを終了します。

		Set-ExecutionPolicy RemoteSigned

5.	次のコマンドレットを実行します。

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1'

6.	次のコマンドレットを実行します。

		Get-AzurePublishSettingsFile

	コマンドレットの指示に従って、Windows Azure ポータルにサインインし、.publishsettings ファイルを保存します。このファイルは、E:\PowerShell\MyAccount.publishsettings などのディレクトリに保存します。この後、他の Windows Azure PowerShell コマンドレットを実行する場合は、手順 4. ～ 6. を繰り返す必要はありません。この操作を行うのは 1 回だけです。


7.	次のコマンドレットを実行して、Windows Azure PowerShell ISE を開きます。

		powershell ise

8.	次のスクリプトを Windows Azure PowerShell ISE に貼り付け、プレースホルダー (*subscriptionname* など) を実際の値に置き換えて、スクリプトを実行します。必要な場合は、管理ポータルで **[ネットワーク]** をクリックして、サブスクリプション名を確認します。ストレージ アカウント名は、手順 1. で指定した名前です。次のスクリプトで使用しているイメージ名では、Windows Server 2012 がインストールされますが、イメージ名は定期的に更新されます。現在利用可能なイメージの一覧を取得するには、<a href="http://msdn.microsoft.com/en-us/library/windowsazure/jj152878.aspx">Get-AzureVMImage</a> を実行します。Windows Azure の仮想ネットワークでは、Windows Server 2012 以降に存在する仮想ドメイン コントローラー保護機能がサポートされます。仮想ドメイン コントローラー保護機能の詳細については、「<a href="http://technet.microsoft.com/en-us/library/hh831734.aspx">Active Directory ドメイン サービス (AD DS) の仮想化 (レベル 100) の概要</a>」を参照してください。

		cls
		
	    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
	    Import-AzurePublishSettingsFile 'C:\PowerShell\Justinha001.publishsettings'
	    Set-AzureSubscription -SubscriptionName 'Networking Demo Subscription' -CurrentStorageAccount 'yourstorageaccount'
	    Select-AzureSubscription -SubscriptionName 'Networking Demo Subscription'
	
	    #Deploy the Domain Controller in a virtual network
	    #-------------------------------------------------
	
		#Specify my DC's DNS IP (127.0.0.1)
		$myDNS = New-AzureDNS -Name 'myDNS' -IPAddress '127.0.0.1'
		$vmname = 'ContosoDC1'
		# OS Image to Use
		$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201306.01-en.us-127GB.vhd'
		$service = 'ConDC1demosvc'
        $AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
	
		#VM Configuration
		$MyDC = New-AzureVMConfig -name $vmname -InstanceSize 'Small' -ImageName $image |
			Add-AzureProvisioningConfig -Windows -Password P@$$w0rd |
				Set-AzureSubnet -SubnetNames 'BackEndSubnet'

		New-AzureVM -ServiceName $service -VMs $MyDC -AffinityGroup $AG -DnsSettings $myDNS -VNetName $vnet

	スクリプトを再実行する場合は、$service に重複しない値を設定する必要があります。Test-AzureName -Service <i>service name</i> を実行できます。これにより、名前が既に取得されているかどうかが返されます。この Windows Azure PowerShell コマンドレットが正常に完了すると、VM は最初は管理ポータルの画面に停止状態と表示され、プロビジョニング処理が続きます。VM のプロビジョニングが完了したら、次の手順に進んでください。

<h2><a id="Step2"></a>手順 2: 追加ディスクを VM に接続する</h2>

1.	管理ポータルで、作成した VM の名前をクリックし、画面の下部で **[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。


2. ハード ディスクのサイズ (GB 単位) をたとえば「30」と入力します。

	![ディスク サイズを指定][specify-disk-size]

3.	手順 9. および 10. を繰り返して、2 台目のディスクを接続します。


4.	VM の名前をクリックして、**[接続]** をクリックします。

	![接続][connect]


5.	**[開く]** をクリックします。

	![オープン][open]

6.	RDP 接続のダイアログ ボックスで、**[このコンピューターへの接続について今後確認しない]** チェック ボックスをオンにして、**[接続]** をクリックします。

	![RDP を使用した接続][connect-rdp]

7.	<i>VM Name</i>\<i>AdminUserName</i> の形式を使用して資格情報を入力します。Windows Azure PowerShell を使用して VM をプロビジョニングした場合、「<i>VM Name</i>\Administrator」と入力します。

	![資格情報][credentials]


8. [リモート デスクトップ接続] で **[はい]** をクリックします。

	![リモート デスクトップ][remote-desktop]



<h2><a id="Step3"></a>手順 3: Active Directory ドメイン サービスをインストールする</h2>

1.	VM に接続したディスクを初期化し、Active Directory ファイルを格納する新しいボリュームを作成します。

	a.	サーバー マネージャーで、**[ファイル サービスと記憶域サービス]** をクリックします。

	b.	**[ディスク]** をクリックして、接続したディスクを右クリックし、**[初期化]** をクリックしたら、**[はい]** をクリックして操作を確認します。

	c.	初期化が完了したら、ディスクを右クリックして **[ボリューム]** をクリックします。新しいボリューム ウィザードで既定値をそのまま使用してボリュームの作成を完了し、Active Directory のデータベースとログ ファイルを保存するために **NTDS** という名前の新しいフォルダーを作成します。

2.	サーバー マネージャーで、**[管理]** をクリックし、**[役割と機能の追加]** をクリックして役割の追加ウィザードを開始します。Windows Server 2012 への AD DS のインストールの詳細については、「[Active Directory ドメイン サービスをインストールする (レベル 100)](http://technet.microsoft.com/en-us/library/hh472162.aspx)」を参照してください。


	![役割の追加][add-role]


3.	[開始する前に] ページで **[次へ]** をクリックします。


4.	[インストールの種類の選択] ページで、**[役割ベースまたは機能ベースのインストール]** をクリックし、**[次へ]** をクリックします。

	![インストールの種類の選択][select-installation]


5.	[対象サーバーの選択] ページで、**[サーバー プールからサーバーを選択]** をクリックし、サーバーの名前をクリックして **[次へ]** をクリックします。

	![サーバーの選択][select-server]

6.	[サーバーの役割の選択] ページで、**[Active Directory ドメイン サービス]** をクリックし、[役割と機能の追加ウィザード] ダイアログ ボックスで **[機能の追加]** をクリックして **[次へ]** をクリックします。

	![サーバーの役割の選択][select-server-role]

	![ツールを選択][add-tools]


7.	[機能の選択] ページで、インストールする追加機能を選択し、**[次へ]** をクリックします。

	![機能を選択][select-features]


8.	[Active Directory ドメイン サービス] ページで、情報を確認して **[次へ]** をクリックします。

9.	[インストール オプションの確認] ページで、**[インストール]** をクリックします。

	![サーバーの役割の選択][confirm-server-role]

10.	[結果] ページで、インストールが成功したことを確認し、**[このサーバーをドメイン コントローラーに昇格する]** をクリックして Active Directory ドメイン サービス構成ウィザードを開始します。

	![昇格][promote]
	
	注: Active Directory ドメイン サービス構成ウィザードを開始せずにこの時点で役割の追加ウィザードを閉じた場合は、サーバー マネージャーで [タスク] をクリックするともう一度開始できます。

11.	[配置構成] ページで、**[新しいフォレストの追加]** をクリックして、ルート ドメインの名前 (たとえば、fabrikam.com) を入力し、**[次へ]** をクリックします。

	![新しいフォレスト][new-forest]

12.	[ドメイン コントローラー オプション] ページで、ディレクトリ サービスの復元モード パスワードを入力して確認し、他の既定値をそのまま使用して **[次へ]** をクリックします。

	![DC オプション][dc-options]

13.	[DNS オプション] ページ (DNS サーバーをインストールした場合のみ表示されます) で、必要に応じて **[DNS 委任の作成]** をクリックし、**[次へ]** をクリックします。DNS 委任を作成する場合は、親 DNS ゾーンで DNS 委任レコードを作成するアクセス許可を持つ資格情報を入力します。親ゾーンをホストする DNS サーバーを作成できない場合、**[DNS 委任の作成]** オプションは使用できません。

14.	[追加オプション] ページで、新しい NetBIOS 名を入力するか、ドメインの既定の NetBIOS 名を確認し、**[次へ]** をクリックします。

	![その他のオプション][additional-options]

15.	[パス] ページで、Active Directory データベース、ログ ファイル、および SYSVOL フォルダーの場所を入力するか参照し、**[次へ]** をクリックします。	
	
	![パス][paths]

16.	[オプションの確認] ページで、選択内容を確認して見直し、**[次へ]** をクリックします。

	![オプションの確認][review-options]

17.	[前提条件のチェック] ページで、前提条件の検証が完了したことを確認し、**[インストール]** をクリックします。

	![前提条件のチェック][prereq-check]

18.	[結果] ページで、サーバーがドメイン コントローラーとして正常に構成されたことを確認します。サーバーは自動的に再起動され、AD DS のインストールが完了します。

<h2><a id="Step4"></a>手順 4: インストールを検証する</h2>

1.	VM に再接続します。

2.	**[スタート]** ボタンをクリックし、「**コマンド プロンプト**」と入力して [コマンド プロンプト] アイコンをクリックし、**[管理者として実行]** をクリックします。

3.	次のコマンドを入力し、Enter キーを押します。

		Dcdiag /c /v

4.	テストが正常に実行されることを確認します。IP アドレスの検証に関連するテストの一部は失敗する可能性があります。タイム サーバーに関連する検証エラーを防止するには、新しいドメイン コントローラーで [Windows タイム サービスを構成](http://technet.microsoft.com/en-us/library/cc731191.aspx)します。


<h2><a id="Step5"></a>手順 5: ドメイン コントローラーをバックアップする</h2>

1.	VM に接続します。

2.	サーバー マネージャーで、**[役割と機能の追加]** をクリックし、[機能の追加] ページで **[Windows Server バックアップ]** を選択します。指示に従って操作し、Windows Server バックアップをインストールします。

3.	**[スタート]** ボタンをクリックし、**[管理ツール]** をポイントして、**[Windows Server バックアップ]**、**[バックアップ (1 回限り)]** (単発パックアップ) の順にクリックします。
 
4.	**[別のオプション]** をクリックし、**[次へ]** をクリックします。

5.	**[サーバー全体]** をクリックし、**[次へ]** をクリックします。

6.	**[ローカル ドライブ]** をクリックし、**[次へ]** をクリックします。

7.	オペレーティング システム ファイルまたは Active Directory データベースをホストしていない宛先ドライブを選択し、**[次へ]** をクリックします。
    ![DC のバックアップ][backup-dc]

8.	設定した内容を確認して、**[バックアップ]** をクリックします。


<h2><a id="Step6"></a>手順 6: 起動時にドメインに参加させる仮想マシンをプロビジョニングする</h2>
DC が構成されたら、管理ポータルを使用して VM を作成するか、次の Windows PowerShell スクリプトを実行して追加の仮想マシンをプロビジョニングし、プロビジョニング時にドメインに自動的に参加させることができます。VM の DNS クライアント リゾルバーの設定は、VM のプロビジョニング時に構成できます。

管理ポータルを使用して VM を作成する場合、DNS クライアント リゾルバー設定でドメイン コントローラーの内部 IP アドレスを優先 DNS サーバーとして指定します。ドメイン コントローラーの内部 IP アドレスを確認するには、ドメイン コントローラーが実行されている仮想マシンの名前をクリックします。

Windows PowerShell の使い方の詳細については、「[Windows Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx)」および「[Windows Azure Management Cmdlets (Windows Azure 管理コマンドレット)](http://msdn.microsoft.com/en-us/library/windowsazure/jj152841)」を参照してください。

1.	Windows Azure PowerShell を使用して、最初の起動時にドメインに参加させる仮想マシンを追加作成するには、Windows Azure PowerShell ISE を開き、次のスクリプトを貼り付け、プレースホルダー (*ContosoDC13* など) を実際の値に置き換えて実行します。New-AzureDNS コマンドレットの -Name パラメーターにドメイン コントローラーの名前を指定します。

	次の例では、ドメイン コントローラーの内部 IP アドレスは 10.4.3.1 です。Add-AzureProvisioningConfig には、-MachineObjectOU パラメーターも指定できます。このパラメーターを指定する (Active Directory での完全識別名が必要) と、そのコンテナー内のすべての仮想マシンにグループ ポリシー設定を指定できます。

	仮想マシンをプロビジョニングした後、administrator@corp.contoso.com などのユーザー プリンシパル名 (UPN) 形式でドメイン アカウントを指定してログオンします。

		cls
		
	    Import-Module "C:\Program Files (x86)\Microsoft SDKs\Windows Azure\PowerShell\Azure\Azure.psd1"
	    Import-AzurePublishSettingsFile '*E:\PowerShell\MyAccount.publishsettings*'
	    Set-AzureSubscription -SubscriptionName *subscriptionname* -CurrentStorageAccount *storageaccountname*
	    Select-AzureSubscription -SubscriptionName *subscriptionname*
		
		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name '*ContosoDC13*' -IPAddress '*10.4.3.1*'
		
		# OS Image to Use
		$image = 'fb83b3509582419d99629ce476bcb5c8__SQL-Server-2012SP1-CU5-11.0.3373.0-Enterprise-ENU-Win2012'
		$service = '*myazuresvcindomainM1*'
		$AG = '*YourAffinityGroup*'
		$vnet = '*YourVirtualNetwork*'
		$pwd = '*p@$$w0rd*'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain '*corp*' -DomainPassword '*p@$$w0rd*' -DomainUserName 'Administrator' -JoinDomain '*corp.contoso.com*'|
		    Set-AzureSubnet -SubnetNames '*BackEnd*'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet

スクリプトを再実行する場合は、$service に重複しない値を設定する必要があります。Test-AzureName -Service <i>service name</i> を実行できます。これにより、名前が既に取得されているかどうかが返されます。この Windows Azure PowerShell コマンドレットが正常に完了すると、VM は最初は管理ポータルの画面に停止状態と表示され、プロビジョニング処理が続きます。プロビジョニングが完了すると、VM にログオンできるようになります。		

## 関連項目

-  [Windows Azure の仮想ネットワーク](http://msdn.microsoft.com/en-us/library/windowsazure/jj156007.aspx)

-  [Windows Azure PowerShell](http://msdn.microsoft.com/en-us/library/windowsazure/jj156055.aspx)

-  [Windows Azure の管理コマンドレットに関するページ](http://msdn.microsoft.com/en-us/library/windowsazure/jj152841)

-  [Active Directory ドメイン サービス (AD DS) の仮想化 (レベル 100) の概要](http://technet.microsoft.com/en-us/library/hh831734.aspx)

[create-vm]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMFromGallery.png
[vm-image]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMImageSelection.png
[vm-configuration]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfig.png
[vm-subnet]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfigSubnet.png
[vm-ports]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConfigPorts.png
[specify-disk-size]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMAttachDisk.png
[connect]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConnect.png
[open]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMOpenRDP.png
[connect-rdp]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMConnectRDP.png
[credentials]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMCreds.png
[remote-desktop]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetCreateVMRDPCert.png
[add-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAddRole.png
[select-installation]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectInstallationType.png
[select-server]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectDestinationServer.png
[select-server-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectServerRole.png
[add-tools]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAddTools.png
[select-features]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCSelectFeatures.png
[confirm-server-role]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCConfirmRole.png
[promote]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPromote.png
[new-forest]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCNewForest.png
[dc-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCOptions.png
[additional-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCAdditionalOptions.png
[paths]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPaths.png
[review-options]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCReviewOptions.png
[prereq-check]: ./media/active-directory-new-forest-virtual-machine/ADDS_VNetDCPrereqCheck.png
[backup-dc]: ./media/active-directory-new-forest-virtual-machine/BackupDC.png


