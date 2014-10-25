<properties linkid="manage-services-networking-replica-domain-controller" urlDisplayName="Replica domain controller" pageTitle="Install a replica domain controller in Azure" metaKeywords="" description="A tutorial that teaches you how to install a domain controller from your Corp Active Directory forest on your Azure virtual machine." metaCanonical="" services="virtual-network" documentationCenter="" title="Install a Replica Active Directory Domain Controller in Azure Virtual Networks" authors="Justinha" solutions="" writer="Justinha" manager="TerryLan" editor="LisaToft" />

<tags ms.service="virtual-network" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="Justinha"></tags>

# Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール

このチュートリアルでは、[Azure の仮想ネットワーク][Azure の仮想ネットワーク]上の仮想マシン (VM) に会社の Active Directory フォレストから追加のドメイン コントローラーをインストールする手順について、順をおって詳しく説明します。このチュートリアルでは、VM の仮想ネットワークが会社のネットワークに接続されます。Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「[Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン][Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン]」を参照してください。

## 目次

-   [前提条件][前提条件]
-   [手順 1.YourPrimaryDC の静的 IP アドレスを確認する][手順 1.YourPrimaryDC の静的 IP アドレスを確認する]
-   [手順 2.会社のフォレストをインストールする][手順 2.会社のフォレストをインストールする]
-   [手順 3.サブネットとサイトを作成する][手順 3.サブネットとサイトを作成する]
-   [手順 4.追加のドメイン コントローラーを CloudSite にインストールする][手順 4.追加のドメイン コントローラーを CloudSite にインストールする]
-   [手順 5.インストールを検証する][手順 5.インストールを検証する]
-   [手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする][手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする]
-   [手順 7.ドメイン コントローラーをバックアップする][手順 7.ドメイン コントローラーをバックアップする]
-   [手順 8.認証と権限承認をテストする][手順 8.認証と権限承認をテストする]

## <span id="Prerequisites"></span></a>前提条件

-   Windows Azure の仮想ネットワークと会社のネットワークとの間で構成された[管理ポータルでのサイト間 VPN の構成][管理ポータルでのサイト間 VPN の構成]をする。
-   仮想ネットワークにクラウド サービスを作成する。
-   仮想ネットワーク内のクラウド サービスに 2 つの VM を展開する (VM を配置するサブネットを指定)。詳細については、「[仮想ネットワークへの仮想マシンの追加][仮想ネットワークへの仮想マシンの追加]」を参照してください。いずれかの VM は、2 つのデータ ディスクを接続するために、そのサイズが L 以上であることが必要です。データ ディスクには次の情報が保存される必要があります。
    -   Active Directory のデータベースとログ。
    -   システム状態のバックアップ。
-   1 つの会社のネットワークと 2 つの VM (YourPrimaryDC と FileServer)。
-   ドメイン ネーム システム (DNS) インフラストラクチャ。外部ユーザーの名前を Active Directory 内のアカウントに解決する必要がある場合に展開する必要があります。この場合、ドメイン コントローラーに DNS サーバーをインストールする前に、DNS ゾーン委任を作成する必要があります。Active Directory ドメイン サービス インストール ウィザードを使用してもこの委任を作成できます。DNS ゾーン委任の作成方法の詳細については、「[ゾーンの委任を作成する][ゾーンの委任を作成する]」を参照してください
-   Azure VM にインストールする DC で、DNS クライアント リゾルバーの設定を次のように構成する。
    -   優先 DNS サーバー: 内部設置型の DNS サーバー。
    -   代替 DNS サーバー: ループ バック アドレス、または可能であれば、同じ仮想ネットワークにある DC 上で実行されている別の DNS サーバー。

<div class="dev-callout"> 
<b>注</b>
<p>独自の DNS インフラストラクチャを用意して Azure の仮想ネットワーク上の AD DS をサポートする必要があります。このリリースの Azure で提供される DNS インフラストラクチャは、AD DS に必要ないくつかの機能 (SRV リソース レコードの動的登録など) をサポートしていません。 </p>
</div>

<div class="dev-callout"> 
<b>注</b>
<p>「<a href="/ja-jp/manage/services/networking/active-directory-forest/">Azure での新しい Active Directory フォレストのインストール</a>」の手順を完了していれば、このチュートリアルを開始する前に、Azure の仮想ネットワーク上のドメイン コントローラーから AD DS を削除することが必要になる場合があります。AD DS の削除方法の詳細については、「<a href="http://technet.microsoft.com/ja-jp/library/cc771844(v=WS.10).aspx">ドメインから Windows Server 2008 ドメイン コントローラーを削除する</a>」を参照してください。</p>
</div>

## <span id="verifystaticip"></span></a>手順 1.YourPrimaryDC の静的 IP アドレスを確認する

1.  会社のネットワーク上の YourPrimaryDC にログオンします。

2.  サーバー マネージャーで [ネットワーク接続の表示] をクリックします。

3.  ローカル エリア ネットワーク接続を右クリックし、[プロパティ] をクリックします。

4.  [インターネット プロトコル バージョン 4 (TCP/IPv4)] をクリックし、[プロパティ] をクリックします。

5.  サーバーに静的 IP アドレスが割り当てられていることを確認します。

    ![VerifystaticIPaddressyourPrimaryDC1][VerifystaticIPaddressyourPrimaryDC1]

## <span id="installforest"></span></a>手順 2.会社のフォレストをインストールする

1.  VM の RDP セッションで、**[スタート]** ボタンをクリックし、「**dcpromo**」と入力して、Enter キーを押します。

    ![InstallCorpForest1][InstallCorpForest1]

2.  [ようこそ] ページで **[次へ]** をクリックします。

    ![InstallCorpForest2][InstallCorpForest2]

3.  [オペレーティング システムの互換性] ページで **[次へ]** をクリックします。

    ![InstallCorpForest3][InstallCorpForest3]

4.  [展開構成の選択] ページで、**[新しいフォレストに新しいドメインを作成する]** をクリックし、**[次へ]** をクリックします。

    ![InstallCorpForest4][InstallCorpForest4]

5.  [フォレスト ルート ドメイン名] ページで、フォレスト ルート ドメインの完全修飾ドメイン名 (FQDN) として「**corp.contoso.com**」と入力し、**[次へ]** をクリックします。

    ![InstallCorpForest5][InstallCorpForest5]

6.  [フォレストの機能レベルの設定] ページで、**[Windows Server 2008 R2]** をクリックし、**[次へ]** をクリックします。

    ![InstallCorpForest6][InstallCorpForest6]

7.  [追加のドメイン コントローラー オプション] ページで、**[DNS サーバー]** をクリックし、**[次へ]** をクリックします。

    ![InstallCorpForest7][InstallCorpForest7]

8.  DNS 委任に関する次の警告が表示された場合は、**[はい]** をクリックします。

    ![InstallCorpForest8][InstallCorpForest8]

9.  [Active Directory データベース、ログ ファイル、および SYSVOL の場所] ページで、ファイルの場所を入力または選択し、**[次へ]** をクリックします。

    ![InstallCorpForest9][InstallCorpForest9]

10. [ディレクトリ サービス復元モード Administrator パスワード] ページで、DSRM パスワードを入力し、確認用にもう一度入力して、**[次へ]** をクリックします。

    ![InstallCorpForest10][InstallCorpForest10]

11. [概要] ページで、指定内容を確認し、**[次へ]** をクリックします。

    ![InstallCorpForest11][InstallCorpForest11]

12. Active Directory インストール ウィザードの処理が完了したら、**[完了]** をクリックし、**[今すぐ再起動する]** をクリックしてインストールを完了します。

    ![InstallCorpForest12][InstallCorpForest12]

## <span id="subnets"></span></a>手順 3.サブネットとサイトを作成する

1.  YourPrimaryDC で、[スタート] ボタンをクリックし、[管理ツール]、[Active Directory サイトとサービス] の順にクリックします。
2.  **[サイト]** をクリックし、**[サブネット]** を右クリックして、**[新しいサブネット]** をクリックします。

    ![CreateSubnetsandSites1][CreateSubnetsandSites1]

3.  **[プレフィックス]** に「**10.1.0.0/24**」と入力し、**Default-First-Site-Name** サイト オブジェクトを選択して、**[OK]** をクリックします。

    ![CreateSubnetsandSites2][CreateSubnetsandSites2]

4.  **[サイト]** を右クリックし、**[新しいサイト]** をクリックします。

    ![CreateSubnetsandSites3][CreateSubnetsandSites3]

5.  [名前] に「**CloudSite**」と入力し、**DEFAULTIPSITELINK** を選択して、**[OK]** をクリックします。

    ![CreateSubnetsandSites4][CreateSubnetsandSites4]

6.  **[OK]** をクリックして、サイトが作成されたことを確認します。

    ![CreateSubnetsandSites5][CreateSubnetsandSites5]

7.  **[サブネット]** を右クリックし、**[新しいサブネット]** をクリックします。

    ![CreateSubnetsandSites6][CreateSubnetsandSites6]

8.  **[プレフィックス]** に「**10.4.2.0/24**」と入力し、**CloudSite** サイト オブジェクトを選択し、**[OK]** をクリックします。

    ![CreateSubnetsandSites7][CreateSubnetsandSites7]

## <span id="cloudsite"></span></a>手順 4.追加のドメイン コントローラーを CloudSite にインストールする

1.  YourVMachine にログオンし、**[スタート]** ボタンをクリックし、「**dcpromo**」と入力して、Enter キーを押します。

    ![AddDC1][AddDC1]

2.  [ようこそ] ページで **[次へ]** をクリックします。

    ![AddDC2][AddDC2]

3.  [オペレーティング システムの互換性] ページで **[次へ]** をクリックします。

    ![AddDC3][AddDC3]

4.  [展開構成の選択] ページで、**[既存のフォレスト]**、**[既存のドメインにドメイン コントローラーを追加する]**、**[次へ]** の順にクリックします。

    ![AddDC4][AddDC4]

5.  [ネットワーク資格情報] ページで、ドメイン コントローラーを **corp.contoso.com** ドメインにインストールし、Domain Admins グループのメンバーの資格情報 (または corp\\administrator 資格情報) を入力します。

    ![AddDC5][AddDC5]

6.  [ドメインの選択] ページで **[次へ]** をクリックします。

    ![AddDC6][AddDC6]

7.  [サイトの選択] ページで、CloudSite が選択されていることを確認し、**[次へ]** をクリックします。

    ![AddDC7][AddDC7]

8.  [追加のドメイン コントローラー オプション] ページで **[次へ]** をクリックします。

    ![AddDC8][AddDC8]

9.  静的 IP 割り当ての警告画面で、**[はい、このコンピューターでは、DHCP サーバーによって自動的に割り当てられた IP アドレスを使用します (推奨しません)。]** をクリックします。

    **重要**

    Azure の仮想ネットワークの IP アドレスは動的ですが、そのリース期間は VM の存続期間です。したがって、仮想ネットワークにインストールするドメイン コントローラーに静的 IP アドレスを設定する必要はありません。VM に静的 IP アドレスを設定すると、通信障害の原因になります。

    ![AddDC9][AddDC9]

10. DNS 委任に関する警告画面が表示されたら、**[はい]** をクリックします。

    ![AddDC10][AddDC10]

11. [Active Directory データベース、ログ ファイル、および SYSVOL の場所] ページで、[参照] をクリックし、データ ディスク上の Active Directory ファイルの場所を入力または選択して、**[次へ]** をクリックします。

    ![AddDC11][AddDC11]

12. [ディレクトリ サービス復元モード Administrator パスワード] ページで、DSRM パスワードを入力し、確認用にもう一度入力して、**[次へ]** をクリックします。

    ![AddDC12][AddDC12]

13. [概要] ページで **[次へ]** をクリックします。

    ![AddDC13][AddDC13]

14. Active Directory インストール ウィザードの処理が完了したら、**[完了]** をクリックし、**[今すぐ再起動する]** をクリックしてインストールを完了します。

    ![AddDC14][AddDC14]

## <span id="validate"></span></a>手順 5.インストールを検証する

1.  VM に再接続します。

2.  **[スタート]** ボタンをクリックし、**[コマンド プロンプト]** を右クリックして、**[管理者として実行]** をクリックします。

3.  次のコマンドを入力し、Enter キーを押します。'Dcdiag /c /v'

4.  テストが正常に実行されることを確認します。

ドメイン コントローラーを構成したら、次の Windows PowerShell コマンドレットを実行して、追加の仮想マシンをプロビジョニングし、プロビジョニング後に自動的にドメインに参加させるようにします。VM の DNS クライアント リゾルバーの設定は、VM のプロビジョニング時に構成する必要があります。ドメイン名、VM 名などを正しい名前に置き換えます。

Windows PowerShell の使い方の詳細については、[Azure PowerShell の概要に関するページ][Azure PowerShell の概要に関するページ]および[Azure 管理コマンドレットに関するページ][Azure 管理コマンドレットに関するページ]を参照してください。

## <span id="provisionvm"></span></a>手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする

1.  最初の起動時にドメインに参加させる仮想マシンを追加作成するには、Azure PowerShell ISE を開き、次のスクリプトを貼り付けて、プレースホルダーを実際の値に置き換えて実行します。

    ドメイン コントローラーの内部 IP アドレスを確認するには、ドメイン コントローラーが実行されている仮想ネットワークの名前をクリックします。

    次の例では、ドメイン コントローラーの内部 IP アドレスは 10.4.3.1 です。Add-AzureProvisioningConfig には、-MachineObjectOU パラメーターも指定できます。このパラメーターを指定する (Active Directory での完全識別名が必要) と、そのコンテナー内のすべての仮想マシンにグループ ポリシー設定を指定できます。

    仮想マシンをプロビジョニングした後、<administrator@corp.contoso.com> などのユーザー プリンシパル名 (UPN) 形式でドメイン アカウントを指定してログオンします。

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

## <span id="backup"></span></a>手順 7.ドメイン コントローラーをバックアップする

1.  YourVMachine に接続します。

2.  **[スタート]** ボタンをクリックし、**[サーバー マネージャー]**、**[機能の追加]**、**[Windows Server バックアップの機能]** の順にクリックします。指示に従って操作し、Windows Server バックアップをインストールします。

3.  **[スタート]** ボタンをクリックし、**[Windows Server バックアップ]**、**[バックアップ (1 回限り)]** の順にクリックします。

4.  **[別のオプション]** をクリックし、**[次へ]** をクリックします。

5.  **[フル サーバー]** をクリックし、**[次へ]** をクリックします。

6.  **[ローカル ドライブ]** をクリックし、**[次へ]** をクリックします。

7.  オペレーティング システムまたは Active Directory データベースをホストしていない宛先ドライブを選択し、[次へ] をクリックします。

    ![BackupDC][BackupDC]

8.  選択したバックアップ設定を確認し、**[バックアップ]** をクリックします。

## <span id="test"></span></a>手順 8.認証と権限承認をテストする

1.  認証と権限承認をテストするために、Active Directory でドメイン ユーザー アカウントを作成します。
    各サイトのクライアント VM にログオンし、VM 上に共有フォルダーを作成します。

2.  さまざまなアカウント、グループ、アクセス許可を使用して共有フォルダーへのアクセスをテストします。

## 関連項目

-   [Azure 仮想ネットワーク][Azure の仮想ネットワーク]

-   [Windows Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎][Windows Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎]

-   [Windows Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成][Windows Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成]

-   [Azure PowerShell][Azure PowerShell の概要に関するページ]

-   [Azure の管理コマンドレット][Azure 管理コマンドレットに関するページ]

  [Azure の仮想ネットワーク]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156007.aspx
  [Azure の仮想マシンでの Windows Server Active Directory のデプロイ ガイドライン]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156090.aspx
  [前提条件]: #Prerequisites
  [手順 1.YourPrimaryDC の静的 IP アドレスを確認する]: #verifystaticip
  [手順 2.会社のフォレストをインストールする]: #installforest
  [手順 3.サブネットとサイトを作成する]: #subnets
  [手順 4.追加のドメイン コントローラーを CloudSite にインストールする]: #cloudsite
  [手順 5.インストールを検証する]: #validate
  [手順 6.起動時にドメインに参加させる仮想マシンをプロビジョニングする]: #provisionvm
  [手順 7.ドメイン コントローラーをバックアップする]: #backup
  [手順 8.認証と権限承認をテストする]: #test
  [管理ポータルでのサイト間 VPN の構成]: http://msdn.microsoft.com/ja-jp/library/dn133795.aspx
  [仮想ネットワークへの仮想マシンの追加]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-networks-add-virtual-machine/
  [ゾーンの委任を作成する]: http://technet.microsoft.com/library/cc753500.aspx
  [Azure での新しい Active Directory フォレストのインストール]: /ja-jp/manage/services/networking/active-directory-forest/
  [ドメインから Windows Server 2008 ドメイン コントローラーを削除する]: http://technet.microsoft.com/ja-jp/library/cc771844(v=WS.10).aspx
  [VerifystaticIPaddressyourPrimaryDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/VerifystaticIP.png
  [InstallCorpForest1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest1.png
  [InstallCorpForest2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest2.png
  [InstallCorpForest3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest3.png
  [InstallCorpForest4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest4.png
  [InstallCorpForest5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest5.png
  [InstallCorpForest6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest6.png
  [InstallCorpForest7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest7.png
  [InstallCorpForest8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest8.png
  [InstallCorpForest9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest9.png
  [InstallCorpForest10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest10.png
  [InstallCorpForest11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest11.png
  [InstallCorpForest12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/InstallCorpForest12.png
  [CreateSubnetsandSites1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites1.png
  [CreateSubnetsandSites2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites2.png
  [CreateSubnetsandSites3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites3.png
  [CreateSubnetsandSites4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites4.png
  [CreateSubnetsandSites5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites5.png
  [CreateSubnetsandSites6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites6.png
  [CreateSubnetsandSites7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/CreateSubnetsandSites7.png
  [AddDC1]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC1.png
  [AddDC2]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC2.png
  [AddDC3]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC3.png
  [AddDC4]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC4.png
  [AddDC5]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC5.png
  [AddDC6]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC6.png
  [AddDC7]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC7.png
  [AddDC8]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC8.png
  [AddDC9]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC9.png
  [AddDC10]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC10.png
  [AddDC11]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC11.png
  [AddDC12]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC12.png
  [AddDC13]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC13.png
  [AddDC14]: ./media/virtual-networks-install-replica-active-directory-domain-controller/AddDC14.png
  [Azure PowerShell の概要に関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj156055.aspx
  [Azure 管理コマンドレットに関するページ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj152841
  [BackupDC]: ./media/virtual-networks-install-replica-active-directory-domain-controller/BackupDC.png
  [Windows Azure IT プロフェッショナル IaaS:(01) 仮想マシンの基礎]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01
  [Windows Azure IT プロフェッショナル IaaS:(05) 仮想ネットワークとクロスプレミス接続の作成]: http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05
