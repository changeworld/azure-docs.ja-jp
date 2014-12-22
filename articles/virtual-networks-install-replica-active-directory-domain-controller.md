<properties urlDisplayName="Replica domain controller" pageTitle="Azure でのレプリカ ドメイン コントローラーのインストール" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha" />




#Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール

このチュートリアルでは、[Azure Virtual Network](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx) 上の仮想マシン (VM) に会社の Active Directory フォレストから追加のドメイン コントローラーをインストールする手順について、順を追って詳しく説明します。このチュートリアルでは、VM の仮想ネットワークが会社のネットワークに接続されます。Azure Virtual Network に Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156090.aspx)」を参照してください。

##目次##

* [前提条件](#Prerequisites)
* [手順 1.YourPrimaryDC の静的 IP アドレスを確認する](#verifystaticip)
* [手順 2.会社のフォレストをインストールする](#installforest)
* [手順 3.サブネットとサイトを作成する](#subnets)
* [手順 4.追加のドメイン コントローラーを CloudSite にインストールする](#cloudsite)
* [手順 5.インストールを検証する](#validate)
* [手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする](#provisionvm)
* [手順 7.ドメイン コントローラーをバックアップする](#backup)
* [手順 8.認証と権限承認をテストする](#test)


<h2><a id="Prerequisites"></a>前提条件</h2>

Azure Virtual Network と会社のネットワークとの間で構成された-	[管理ポータルでのサイト間 VPN の構成](http://msdn.microsoft.com/ja-jp/library/dn133795.aspx)を実行する。
-	仮想ネットワークにクラウド サービスを作成する。
-	仮想ネットワーク内のクラウド サービスに 2 つの VM をデプロイする (VM を配置するサブネットを指定)。詳細については、「[仮想ネットワークへの仮想マシンの追加](http://azure.microsoft.com/ja-jp/documentation/articles/virtual-networks-add-virtual-machine/)」を参照してください。いずれかの VM は、2 つのデータ ディスクを接続するために、そのサイズが L 以上であることが必要です。データ ディスクには次の情報が保存される必要があります。
	- Active Directory のデータベースとログ。
	- システム状態のバックアップ。
-	1 つの会社のネットワークと 2 つの VM (YourPrimaryDC と FileServer)。
-	ドメイン ネーム システム (DNS) インフラストラクチャ。外部ユーザーの名前を Active Directory 内のアカウントに解決する必要がある場合にデプロイする必要があります。この場合、ドメイン コントローラーに DNS サーバーをインストールする前に、DNS ゾーン委任を作成する必要があります。Active Directory ドメイン サービス インストール ウィザードを使用してもこの委任を作成できます。DNS ゾーン委任の作成方法の詳細については、「[ゾーンの委任を作成する](http://technet.microsoft.com/library/cc753500.aspx)」を参照してください。
-	Azure VM にインストールする DC で、DNS クライアント リゾルバーの設定を次のように構成する。
	- 優先 DNS サーバー: 内部設置型の DNS サーバー。
	- 代替 DNS サーバー: ループ バック アドレス、または可能であれば、同じ仮想ネットワークにある DC 上で実行されている別の DNS サーバー。

<div class="dev-callout"> 
<b>注</b>
<p>独自の DNS インフラストラクチャを用意して Azure Virtual Network 上の AD DS をサポートする必要があります。このリリースの Azure で提供される DNS インフラストラクチャは、AD DS に必要ないくつかの機能 (SRV リソース レコードの動的登録など) をサポートしていません。 </p>
</div>

<div class="dev-callout"> 
<b>注</b>
<p>「<a href="/ja-jp/manage/services/networking/active-directory-forest/">Install a new Active Directory forest in Azure (Azure での新しい Active Directory フォレストのインストール)</a>」の手順を完了していれば、このチュートリアルを開始する前に、Azure Virtual Network 上のドメイン コントローラーから AD DS を削除することが必要になる場合があります。AD DS の削除方法の詳細については、「<a href="http://technet.microsoft.com/ja-jp/library/cc771844(v=WS.10).aspx">ドメインから Windows Server 2008 ドメイン コントローラーを削除する</a>」を参照してください。</p>
</div>


<h2><a id="verifystaticip"></a>手順 1.YourPrimaryDC の静的 IP アドレスを確認する</h2>

1. 会社のネットワーク上の YourPrimaryDC にログオンします。

2. サーバー マネージャーで [ネットワーク接続の表示] をクリックします。

3. ローカル エリア ネットワーク接続を右クリックし、[プロパティ] をクリックします。

4. [インターネット プロトコル バージョン 4 (TCP/IPv4)] をクリックし、[プロパティ] をクリックします。

5. サーバーに静的 IP アドレスが割り当てられていることを確認します。 

	![VerifystaticIPaddressyourPrimaryDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png)


<h2><a id="installforest"></a>手順 2.会社のフォレストをインストールする</h2>

1. VM の RDP セッションで、**[スタート]** ボタンをクリックし、「**dcpromo**」と入力して、Enter キーを押します。

	![InstallCorpForest1](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png)


2. [ようこそ] ページで **[次へ]** をクリックします。

	![InstallCorpForest2](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png)



3. [オペレーティング システムの互換性] ページで **[次へ]** をクリックします。

	![InstallCorpForest3](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png)

4. [展開の構成の選択] ページで、**[新しいフォレストに新しいドメインを作成する]** をクリックし、**[次へ]** をクリックします。 

	![InstallCorpForest4](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png)


5. [フォレスト ルート ドメイン名] ページで、フォレスト ルート ドメインの完全修飾ドメイン名 (FQDN) として「**corp.contoso.com**」と入力し、**[次へ]** をクリックします。

	![InstallCorpForest5](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png)


6. [フォレストの機能レベルの設定] ページで、**[Windows Server 2008 R2]** をクリックし、**[次へ]** をクリックします。

	![InstallCorpForest6](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png)

7. [追加のドメイン コントローラー オプション] ページで、**[DNS サーバー]** をクリックし、**[次へ]** をクリックします。

	![InstallCorpForest7](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png)

8. DNS 委任に関する次の警告が表示された場合は、**[はい]** をクリックします。

	![InstallCorpForest8](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png)


9. [Active Directory データベース、ログ ファイル、および SYSVOL の場所] ページで、ファイルの場所を入力または選択し、**[次へ]** をクリックします。

	![InstallCorpForest9](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png)


10. [ディレクトリ サービス復元モード Administrator パスワード] ページで、DSRM パスワードを入力し、確認用にもう一度入力して、**[次へ]** をクリックします。

	![InstallCorpForest10](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png)


11. [概要] ページで、指定内容を確認し、**[次へ]** をクリックします。

	![InstallCorpForest11](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png)

12. Active Directory インストール ウィザードの処理が完了したら、**[完了]** をクリックし、**[今すぐ再起動する]** をクリックしてインストールを完了します。

	![InstallCorpForest12](./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png)



<h2><a id="subnets"></a>手順 3.サブネットとサイトを作成する</h2>

1. YourPrimaryDC で、[スタート] ボタンをクリックし、[管理ツール]、[Active Directory サイトとサービス] の順にクリックします。
2. **[サイト]** をクリックし、**[サブネット]** を右クリックして、**[新しいサブネット]** をクリックします。

	![CreateSubnetsandSites1](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png)

3. **[プレフィックス]** に「**10.1.0.0/24**」と入力し、**Default-First-Site-Name** サイト オブジェクトを選択して、**[OK]** をクリックします。

	![CreateSubnetsandSites2](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png)

4. **[サイト]** を右クリックし、**[新しいサイト]** をクリックします。

	![CreateSubnetsandSites3](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png)


5. [名前] に「**CloudSite**」と入力し、**DEFAULTIPSITELINK** を選択して、**[OK]** をクリックします。

	![CreateSubnetsandSites4](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png)


6. **[OK]** をクリックして、サイトが作成されたことを確認します。

	![CreateSubnetsandSites5](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png)

7. **[サブネット]** を右クリックし、**[新しいサブネット]** をクリックします。

	![CreateSubnetsandSites6](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png)

8. **[プレフィックス]** に「**10.4.2.0/24**」と入力し、**CloudSite** サイト オブジェクトを選択して、**[OK]** をクリックします。

	![CreateSubnetsandSites7](./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png)


<h2><a id="cloudsite"></a>手順 4.追加のドメイン コントローラーを CloudSite にインストールする</h2>

1. YourVMachine にログオンし、**[スタート]** ボタンをクリックし、「**dcpromo**」と入力して、Enter キーを押します。

	![AddDC1](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png)

2. [ようこそ] ページで **[次へ]** をクリックします。

	![AddDC2](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png)


3. [オペレーティング システムの互換性] ページで **[次へ]** をクリックします。

	![AddDC3](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png)

4. [デプロイ構成の選択] ページで、**[既存のフォレスト]**、**[既存のドメインにドメイン コントローラーを追加する]**、**[次へ]** の順にクリックします。

	![AddDC4](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png)


5. [ネットワーク資格情報] ページで、ドメイン コントローラーを **corp.contoso.com** ドメインにインストールし、Domain Admins グループのメンバーの資格情報 (または corp\administrator 資格情報) を入力します。

	![AddDC5](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png)


6. [ドメインの選択] ページで **[次へ]** をクリックします。

	![AddDC6](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png)


7. [サイトの選択] ページで、CloudSite が選択されていることを確認し、**[次へ]** をクリックします。

	![AddDC7](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png)

8. [追加のドメイン コントローラー オプション] ページで **[次へ]** をクリックします。

	![AddDC8](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png)


9. 静的 IP 割り当ての警告画面で、**[はい、このコンピューターでは、DHCP サーバーによって自動的に割り当てられた IP アドレスを使用します (推奨しません)。]**

	**重要**

	Azure Virtual Network の IP アドレスは動的ですが、そのリース期間は VM の存続期間です。したがって、仮想ネットワークにインストールするドメイン コントローラーに静的 IP アドレスを設定する必要はありません。VM に静的 IP アドレスを設定すると、通信障害の原因になります。


	![AddDC9](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png)

10. DNS 委任に関する警告画面が表示されたら、**[はい]** をクリックします。

	![AddDC10](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png)


11. [Active Directory データベース、ログ ファイル、および SYSVOL の場所] ページで、[参照] をクリックし、データ ディスク上の Active Directory ファイルの場所を入力または選択して、**[次へ]** をクリックします。

	![AddDC11](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png)

12. [ディレクトリ サービス復元モード Administrator パスワード] ページで、DSRM パスワードを入力し、確認用にもう一度入力して、**[次へ]** をクリックします。

	![AddDC12](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png)

13. [概要] ページで **[次へ]** をクリックします。

	![AddDC13](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png)

14. Active Directory インストール ウィザードの処理が完了したら、**[完了]** をクリックし、**[今すぐ再起動する]** をクリックしてインストールを完了します。

	![AddDC14](./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png)


<h2><a id="validate"></a>手順 5.インストールを検証する</h2>

1. VM に再接続します。

2. **[スタート]** ボタンをクリックし、**[コマンド プロンプト]** を右クリックして、**[管理者として実行]** をクリックします。

3. 次のコマンドを入力し、Enter キーを押します。'Dcdiag /c /v'

4. テストが正常に実行されることを確認します。 

ドメイン コントローラーを構成したら、次の Windows PowerShell コマンドレットを実行して、追加の仮想マシンをプロビジョニングし、プロビジョニング後に自動的にドメインに参加させるようにします。VM の DNS クライアント リゾルバーの設定は、VM のプロビジョニング時に構成する必要があります。ドメイン名、VM 名などを正しい名前に置き換えます。 

Windows PowerShell の使い方の詳細については、「[Azure PowerShell](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156055.aspx)」および「[Azure Management Cmdlets (Azure 管理コマンドレット)](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj152841)」を参照してください。


<h2><a id="provisionvm"></a>手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする</h2>

1. 最初の起動時にドメインに参加させる仮想マシンを追加作成するには、Azure PowerShell ISE を開き、次のスクリプトを貼り付けて、プレースホルダーを実際の値に置き換えて実行します。

	ドメイン コントローラーの内部 IP アドレスを確認するには、ドメイン コントローラーが実行されている仮想ネットワークの名前をクリックします。

	次の例では、ドメイン コントローラーの内部 IP アドレスは 10.4.3.1 です。Add-AzureProvisioningConfig には、-MachineObjectOU パラメーターも指定できます。このパラメーターを指定する (Active Directory での完全識別名が必要) と、そのコンテナー内のすべての仮想マシンにグループ ポリシー設定を指定できます。

	仮想マシンをプロビジョニングした後、administrator@corp.contoso.com などのユーザー プリンシパル名 (UPN) 形式でドメイン アカウントを指定してログオンします。 

		#Deploy a new VM and join it to the domain
		#-------------------------------------------
		#Specify my DC's DNS IP (10.4.3.1)
		$myDNS = New-AzureDNS -Name 'ContosoDC13' -IPAddress '10.4.3.1'
		
		# OS Image to Use
		$image = 'MSFT__Sql-Server-11EVAL-11.0.2215.0-08022012-ja-jp-30GB.vhd'
		$service = 'myazuresvcindomainM1'
		$AG = 'YourAffinityGroup'
		$vnet = 'YourVirtualNetwork'
		$pwd = 'p@$$w0rd'
		$size = 'Small'
		
		#VM Configuration
		$vmname = 'MyTestVM1'
		$MyVM1 = New-AzureVMConfig -name $vmname -InstanceSize $size -ImageName $image |
		    Add-AzureProvisioningConfig -WindowsDomain -Password $pwd -Domain 'corp' -DomainPassword 'p@$$w0rd' -DomainUserName 'Administrator' -JoinDomain 'corp.contoso.com'|
		    Set-AzureSubnet -SubnetNames 'BackEnd'
		
		New-AzureVM -ServiceName $service -AffinityGroup $AG -VMs $MyVM1 -DnsSettings $myDNS -VNetName $vnet
		

<h2><a id="backup"></a>手順 7.ドメイン コントローラーをバックアップする</h2>


1. YourVMachine に接続します。

2. **[スタート]** ボタンをクリックし、**[サーバー マネージャー]**、**[機能の追加]**、**[Windows Server バックアップの機能]** の順にクリックします。指示に従って操作し、Windows Server バックアップをインストールします。

3. **[スタート]** ボタンをクリックし、**[Windows Server バックアップ]**、**[バックアップ (1 回限り)]** の順にクリックします。
 
4. **[別のオプション]** をクリックし、**[次へ]** をクリックします。

5. **[フル サーバー]** をクリックし、**[次へ]** をクリックします。

6. **[ローカル ドライブ]** をクリックし、**[次へ]** をクリックします。

7. オペレーティング システムまたは Active Directory データベースをホストしていない宛先ドライブを選択し、[次へ] をクリックします。

	![BackupDC](./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png)


8. 選択したバックアップ設定を確認し、**[バックアップ]** をクリックします。

<h2><a id="test"></a>手順 8.認証と権限承認をテストする</h2>

1. 認証と権限承認をテストするために、Active Directory でドメイン ユーザー アカウントを作成します。 
各サイトのクライアント VM にログオンし、VM 上に共有フォルダーを作成します。

2. さまざまなアカウント、グループ、アクセス許可を使用して共有フォルダーへのアクセスをテストします。

## 関連項目

-  [Azure 仮想ネットワーク ](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx)

-  [Windows Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)

-  [Windows Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

-  [Azure PowerShell](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156055.aspx)

-  [Azure の管理コマンドレット](http://msdn.microsoft.com/ja-jp/library/windowsazure/jj152841)
