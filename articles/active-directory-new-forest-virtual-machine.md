<properties 
	pageTitle="Azure の仮想ネットワークでの Active Directory フォレストのインストール" 
	description="Azure の仮想ネットワーク上の仮想マシン (VM) に新しい Active Directory フォレストを作成する手順について説明したチュートリアルです。" 
	services="active-directory, virtual-network" 
	documentationCenter="" 
	authors="Justinha" 
	writer="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="Justinha"/>




#Azure の仮想ネットワークでの Active Directory フォレストのインストール

このトピックでは、[Azure の仮想ネットワーク](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)上の仮想マシン (VM) に新しい Windows Server Active Directory 環境を作成する方法を示します。この場合、Azure の仮想ネットワークは内部設置型のネットワークには接続されません。 

必要に応じて次の関連するトピックも参照してください。

- [管理ポータル ウィザードを使用してサイト間 VPN を構成](http://msdn.microsoft.com/library/windowsazure/dn133795.aspx)した後、新しいフォレストをインストールするか、内部設置型のフォレストを Azure の仮想ネットワークに拡張することもできます。これらの手順については、「[Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)」を参照してください。
-  Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイガイドライン](http://msdn.microsoft.com/library/windowsazure/jj156090.aspx)」を参照してください。
-  AD DS を含む Azure でのテスト ラボ環境を作成する手順については、[「Test Lab Guide: Windows Server 2012 R2 Base Configuration in Azure (テスト ラボ ガイド: Azure での Windows Server 2012 R2 の基本構成) ](http://www.microsoft.com/ja-jp/download/details.aspx?id=41684)」を参照してください。



##目次##

* [内部設置型の場合との違い](#differ)
* [手順 1:Azure の仮想ネットワークを作成する](#createvnet)
* [手順 2:ドメイン コントローラーと DNS サーバーのロールを実行する仮想マシンを作成する](#createvm)
* [手順 3:Windows Server Active Directory をインストールする](#installad)
* [手順 4:Azure の仮想ネットワークの DNS サーバーを設定する](#dns)
* [手順 5:ドメイン メンバーの VM を作成してドメインに参加させる](#domainmembers)


<h2><a id="differ"></a>内部設置型の場合との違い</h2>
ドメイン コントローラーの内部設置型へのインストールと Azure へのインストールにそれほど大きな違いはありません。主な違いを次の表に示します。 

構成対象| 内部設置型  | Azure の仮想ネットワーク	
------------- | -------------  | ------------
**ドメイン コントローラーの IP アドレス**  | ネットワーク アダプターのプロパティの静的 IP アドレスを割り当てる   | Set-AzureStaticVNetIP コマンドレットを実行して静的 IP アドレスを割り当てる
**DNS クライアント リゾルバー**  | ドメイン メンバーのネットワーク アダプターのプロパティの優先および代替 DNS サーバー アドレスを設定する   | 仮想ネットワークのプロパティの DNS サーバー アドレスを設定する
**Active Directory データベース ストレージ**  | 既定の保存先を C:\ から変更する (省略可能)| 既定の保存先を C:\ から変更する (必須)



<h2><a id="createvnet"></a>手順 1.Azure の仮想ネットワークを作成する</h2>
1. [Azure 管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 仮想ネットワークを作成します。[ネットワーク] <b>、</b> [仮想ネットワークの作成] <b>をクリックします</b>。次の表の値を使用してウィザードの手順を完了します。 

	ウィザードのページ  | 指定する値
	------------- | -------------
	**仮想ネットワークの詳細**  | <p>名前:仮想ネットワークの名前を入力する</p><p>リージョン:最寄りのリージョンを選択する</p>
	**DNS と VPN**  | <p>[DNS サーバー] は空白のままにする</p><p>いずれの VPN オプションも選択しない</p>
	**仮想ネットワーク アドレス空間**  | <p>サブネット名:サブネットの名前を入力する</p><p>開始 IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p>



<h2><a id="createvm"></a>手順 2.ドメイン コントローラーと DNS サーバーのロールを実行する仮想マシンを作成する</h2>
 
1.  [新規] <b>、</b> [コンピューティング] <b>、</b> [コンピューティング] <b>、</b> [コンピューティング] <b>の順に</b>クリックします。 
2. 次の表の値を使用してウィザードの手順を完了します。

	ウィザードのページ  | 指定する値
	------------- | -------------
	**オペレーティング システム**  | **[Windows Server 2012 R2 Datacenter]** を選択する
	**仮想マシンの構成**  | <p>リリース日:今日の日付</p><p>コンピューター名:一意の値を指定する</p><p>階層:標準</p><p>サイズ:任意のサイズを選択する</p><p>ユーザー名:ユーザーの名前を入力する。このユーザー アカウントは組み込みの Administrators グループのメンバーになります。 </p><p>パスワード:必ず 8 文字以上で構成し、次の種類の 3 文字を含める</p><ul><li>大文字</li><li>小文字</li><li>数字</li><li>特殊文字</li></ul>
	**クラウド サービス**  | <p>クラウド サービス: <b>[新しいクラウド サービスの作成]</b></p><p>クラウド サービス名:既定値をそのまま使用する</p><p>リージョン/アフィニティ グループ/仮想ネットワーク:作成した仮想ネットワークを選択する</p><p>仮想ネットワーク サブネット:作成したサブネットを選択する </p><p>ストレージ アカウント: <b>[自動的に生成されたストレージ アカウントを使用]</b></p><p>可用性セット: <b>None</b></p><p>エンドポイント:既定値をそのまま使用する</p>
	**VM エージェント**  | **[VM エージェントのインストール]** を選択する

1. VM に既定で割り当てられる動的 IP アドレスはクラウド サービスの期間中に有効です。ただし、VM がシャットダウンされた場合は変更されます。静的 IP アドレスを割り当てるには、[Azure PowerShell コマンドレット Set-AzureStaticVNetIP を実行](http://msdn.microsoft.com/library/windowsazure/dn630228.aspx)してください。これにより、仮想マシンのシャットダウンが必要になっても IP アドレスは保持されます。 
2. Active Directory データベース、ログ、SYSVOL を格納する追加のディスクを VM にアタッチします。 
  3.[VM] <b>、</b> [コンピューティング] <b>、</b> [コンピューティング] <b>の順に</b>クリックします。 
  4.サイズ (10 GB など) を指定し、他のすべての既定値をそのまま使用します。
3. VM にログオンし、追加のディスクをフォーマットします。 
  4.[新規] <b>クリックして</b> VM にログオンし、 <b>[開く] をクリックして</b> RDP セッションを作成します。 <b>クリックして</b> 再びクリックします。
  4.資格情報を新しいユーザー名と指定したパスワードに変更します。
  5.サーバー マネージャーで  <b>[ツール]</b> [コンピューティング] <b>[コンピューターの管理]</b>クリックします。 
  6.[新規] <b>クリックし</b> 、 <b>[OK]</b> をクリックして新しいディスクを初期化します。 
  6.ディスク名を右クリックして [ <b>新しいシンプル ボリューム</b>クリックします。ウィザードの手順を完了して新しいドライブをフォーマットします。 

<h2><a id="installad"></a>手順 3.Windows Server Active Directory をインストールする</h2>
内部設置型に使用する同じ一連の手順 (同じ UI、応答ファイル、または Windows PowerShell) により、[AD DS をインストール](http://technet.microsoft.com/library/jj574166.aspx)します。管理者の資格情報を指定して新しいフォレストをインストールする必要があります。Active Directory データベースの場所、ログ、SYSVOL を指定するには、既定の保存先をオペレーティング システム ドライブから、VM にアタッチした追加のデータ ディスクに変更します。 
<p>DC のインストールが完了したら、仮想マシンに再び接続し、DC にログオンします。必ずドメインの資格情報の指定してください。</p>

<h2><a id="dns"></a>手順 4.Azure の仮想ネットワークの DNS サーバーを設定する</h2>
1. [仮想ネットワーク] <b>をクリックし、</b>作成した仮想ネットワークをダブルクリックして [ <b>構成</b>クリックします。 
2. DNS サーバー <b>で、</b>DC の名前と DIP を入力して、[ <b>保存</B>クリックします。 
3. VM を選択し、[ <b>再起動</b> ] をクリックして VM を起動します。新しい DNS サーバーの IP アドレスを使用して DNS リゾルバーの設定を構成します。 


<h2><a id="domainmembers"></a>手順 5.ドメイン メンバーの VM を作成してドメインに参加させる</h2>
<p>追加の仮想マシンを作成して、ドメイン メンバー コンピューターをプロビジョニングします。UI または Azure PowerShell を使用できます。UI を使用する場合は、最初の VM の作成に使用した同じ手順に従います。その後、内部設置型に使用する同じ手順に従って VM をドメインに参加させます。Azure PowerShell を使用する場合は、VM をプロビジョニングし、初回起動時にドメインに参加させることができます。 </p><p>この例では、Windows Server 2012 R2 Datacenter を実行するドメインに参加している VM が DC2 という名前で作成されます。DC2 がプロビジョニングされたら、Domain Admin としてログインし、DC2 をレプリカ DC に昇格させます。 </p><p>Get-AzureVMImage を実行してイメージ名を取得します。たとえば、Windows Server 2012 R2 のイメージの一覧を返すには、Get-AzureVMImage | where-object {$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} を実行します。</p>
	'

	cls

	Set-AzureSubscription -SubscriptionName "Free Trial" -currentstorageaccountname 'constorageaccount'
	Select-AzureSubscription -SubscriptionName "Free Trial"

	#Deploy a new VM and join it to the domain
	#-------------------------------------------
	#Specify my DC's DNS IP (10.0.0.4)
	$myDNS = New-AzureDNS -Name 'DC1' -IPAddress '10.0.0.4'
	
	# OS Image to Use
	$image = 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd'
	$service = 'DC2'
	$vnet = 'YourVirtualNetwork'
	$pwd = 'P@ssw0rd'
	$size = 'Small'

	#VM Configuration
	$vmname = 'DC2'
	$MyVM3 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
    Add-AzureProvisioningConfig -AdminUserName 'PierreSettles' -WindowsDomain -Password $pwd -Domain 'Contoso' -DomainPassword 'P@ssw0rd' -DomainUserName 'PierreSettles' -JoinDomain 'contoso.com'|
    Set-AzureSubnet -SubnetNames 'FrontEnd' 

	New-AzureVM -ServiceName $service -VMs $MyVM3 -DnsSettings $myDNS -VNetName $vnet   

スクリプトを再実行する場合は、$service に重複しない値を設定する必要があります。Test-AzureName -Service <i>service name を実行できます。</i>これにより、名前が既に取得されているかどうかが返されます。  	

## 関連項目

-  [Azure の仮想マシンでの Windows Server Active Directory の展開ガイドライン](http://msdn.microsoft.com/library/azure/jj156090.aspx)

-  [管理ポータルでのクラウド専用仮想ネットワークの構成](http://msdn.microsoft.com/library/dn631643.aspx)

-  [管理ポータルでのサイト間 VPN の構成](http://msdn.microsoft.com/library/dn133795.aspx)

-  [Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール](http://azure.microsoft.com/documentation/articles/virtual-networks-install-replica-active-directory-domain-controller/)

-  [Windows Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure の仮想ネットワーク](http://msdn.microsoft.com/library/windowsazure/jj156007.aspx)

-  [Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)

-  [Azure PowerShell](http://msdn.microsoft.com/library/windowsazure/jj156055.aspx)

-  [Azure の管理コマンドレット](http://msdn.microsoft.com/library/windowsazure/jj152841)

-  [Azure VM の静的 IP アドレスの設定](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)

-  [Azure VM への静的 IP アドレスの割り当て方法](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)

-  [新しい Active Directory フォレストのインストール](http://technet.microsoft.com/library/jj574166.aspx)

-  [Active Directory ドメイン サービス (AD DS) の仮想化 (レベル 100) の概要](http://technet.microsoft.com/library/hh831734.aspx)

-  [テスト ラボ ガイド:Windows Server 2012 R2 Base Configuration in Azure (テスト ラボ ガイド: Azure での Windows Server 2012 R2 の基本構成)](http://www.microsoft.com/ja-jp/download/details.aspx?id=41684)


<!--HONumber=35.2-->

<!--HONumber=46--> 
