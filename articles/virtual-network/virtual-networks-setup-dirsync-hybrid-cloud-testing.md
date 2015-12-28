<properties 
	pageTitle="Office 365 DirSync のテスト環境 | Microsoft Azure" 
	description="IT プロまたは開発テストのための、ハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) サーバーの構成方法について説明します。" 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/11/2015" 
	ms.author="josephd"/>

# テスト用のハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) の設定

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]リソース マネージャー モデル。
 

このトピックでは、Microsoft Azure にホストされているパスワードの同期を使用して Office 365 ディレクトリ同期 (DirSync) をテストするためのハイブリッド クラウド環境を作成する手順全体を説明します。完成すると次のような構成になります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
この構成は、インターネット上のご使用の場所から Azure 運用環境の DirSync サーバーをシミュレートします。構成は次のとおりです。

- 簡略化されたオンプレミス ネットワーク (Corpnet サブネット)。
- Azure でホストされているクロスプレミス仮想ネットワーク (TestVNET)。
- サイト間 VPN 接続
- Office 365 FastTrack 試用版のサブスクリプション。
- TestVNET 仮想ネットワーク内の Azure AD Connect ツールとセカンダリ ドメイン コントローラーを実行する DirSync サーバー。

この構成を基盤や共通の出発点にして以下を実行できます。

- パスワード同期を使用して、オンプレミスの Active Directory ドメインとの同期に依拠する Office 365 のアプリケーションを開発、テストする。
- このクラウド ベースの IT ワークロードのテストを実行する。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 3 つのフェーズに分かれています。

1.	テスト用のハイブリッド クラウド環境を設定する。
2.	Office 365 FastTrack 試用版を構成する。
3.	DirSync サーバー (DS1) を構成する。

Azure サブスクリプションを持っていない場合は、[Azure の無料試用版のページ](http://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップすることもできます。MSDN サブスクリプションをお持ちの場合は、「[MSDN サブスクライバー向けの Azure の特典](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

## フェーズ 1: ハイブリッド クラウド環境を設定する

「[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)」の指示に従います。このテスト環境では Corpnet サブネット上に APP1 サーバーを配置する必要がないため、シャットダウンしてかまいません。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]フェーズ 1 では、シミュレートされたハイブリッド クラウド テスト環境を設定することもできます。方法については、「[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)」をご覧ください。

## フェーズ 2: Office 365 FastTrack 試用版を構成する

Office 365 FastTrack 試用版を使用し始めるには、仮の会社名と Microsoft アカウントが必要です。会社名には、Microsoft のサンプル コンテンツで使用される仮の会社の会社名 Contoso のバリエーションを使用することをお勧めします。ただし、この会社名の使用は必須ではありません。

次に、新しい Microsoft アカウントにサインアップします。****http://outlook.com** に移動し、user123@outlook.com のような電子メール アドレスを使用してアカウントを作成します。このアカウントで Office 365 FastTrack 試用版にサインアップすることになります。

次に、新しい Office 365 Enterprise E3 試用版にサインアップします。

1.	CORP\\User1 アカウント資格情報で CLIENT1 にログオンします。
2.	Internet Explorer を開き、****https://go.microsoft.com/fwlink/p/?LinkID=403802** にアクセスします。
3.	Office 365 Enterprise E3 試用版にサインアップする手順を実行します。

**ビジネス用電子メール アドレス**の入力を求められたら、新しい Microsoft アカウントを指定します。

ID の作成を求めるメッセージが表示されたら、初期の Office 365 アカウントの名前を入力し、その後に仮の会社名とパスワードを入力します。作成された電子メール アドレス (user123@contoso123.onmicrosoft.com など) とパスワードを安全な場所に記録します。この情報は、フェーズ 3 で Azure AD Connect の構成を完了するために必要になります。

完了したら、Office 365 ポータルのメインのページが表示されます。上部のリボンで、**[管理者]**、**[Office 365]** の順にクリックします。Office 365 管理センター ページが表示されます。CLIENT1 でこのページを開いたままにします。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## フェーズ 3: DirSync サーバー (DS1) を構成する

まず、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、DS1 用に Azure 仮想マシンを作成します。これらのコマンドを実行する前に、変数の値を入力し、< and > の文字を削除します。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

次に、DS1 仮想マシンに接続します。

1.	Microsoft Azure 管理ポータルの仮想マシンのページの DS1 の仮想マシンの [状態] 列で、**[実行中]** をクリックします。
2.	タスク バーで、**[接続]** をクリックします。 
3.	DS1.rdp を開くように求められたら、**[開く]** をクリックします。
4.	リモート デスクトップ接続のメッセージ ボックスが表示されたら、**[接続]** をクリックします。
5.	資格情報の入力を求められたら、次の情報を使用します。
	- 名前: **CORP\\User1**
	- パスワード: [User1 アカウントのパスワード]
6.	証明書に関するリモート デスクトップ接続のメッセージ ボックスが表示されたら、**[はい]** をクリックします。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。DS1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

ping コマンドで IP アドレス 10.0.0.1 からの応答が 4 回成功する必要があります。

次に、Windows PowerShell コマンド プロンプトで次のコマンドを使用して、DS1 に .NET 3.5 をインストールします。

	Add-WindowsFeature NET-Framework-Core

次に、Office 365 FastTrack 試用版でディレクトリ同期を有効にします。

1.	CLIENT1 の **[Office 365 管理センター]** ページの左側ウィンドウで、**[ユーザー]**、**[アクティブなユーザー]** の順にクリックします。
2.	**[Active Directory 同期]** の、**[セットアップ]** をクリックします。
3.	[Active Directory 同期のセットアップと管理] ページで、手順 3. の、**[アクティブ化]** をクリックします。
4.	**"Active Directory 同期をアクティブ化しますか?"** というメッセージが表示されたら、**[アクティブ化]** をクリックします。この後に、手順 3. にメッセージ **"Active Directory 同期がアクティブ化されています。"** が表示されます。
5.	**[Active Directory 同期のセットアップと管理]** ページを CLIENT1 で開いたままにしておきます。

次に、CORP\\User1 アカウントで DC1 にログオンし、管理者レベルで Windows PowerShell コマンド プロンプトを開きます。これらのコマンドを 1 つずつ実行して、contoso\_users と呼ばれる新しい組織単位を作成し、Marci Kaufman と Lynda Meyer 用の新しいユーザー アカウントを 2 つ追加します。

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

各 Windows PowerShell コマンドを実行すると、新しいユーザーのパスワードの入力を求められます。これらのパスワードを記録し、安全な場所に記録します。この情報は後で必要になります。

次に、DS1 に Azure AD Connect ツールをインストールして構成します。

1.	Internet Explorer を実行し、**アドレス** バーに「****https://www.microsoft.com/download/details.aspx?id=47594**」と入力してから、Enter キーを押します。
2.	Microsoft Azure AD Connect のセットアップ プログラムを実行します。
3.	デスクトップで、**[Azure AD Connect]** をダブルクリックします。
4.	**[ようこそ]** ページで、**[ライセンス条項とプライバシーに関する声明に同意します]** を選択し、**[続行]** をクリックします。
5.	**[簡単設定]** ページで、**[簡単設定を使う]** をクリックします。
6.	**[Azure AD に接続]** ページで、フェーズ 2 で Office 365 FastTrack 試用版をセットアップするときに作成した初期アカウントの電子メール アドレスとパスワードを入力します。**[次へ]** をクリックします。
7.	**[Azure AD に接続]** ページで、**[ユーザー名]** に「**CORP\\User1**」と入力し、**[パスワード]** に User1 アカウントのパスワードを入力します。[次へ] をクリックします。
8.	**[構成の準備完了]** ページで、設定を確認し、**[インストール]** をクリックします。
9.	**[構成が完了しました]** ページで、**[終了]** をクリックします。

次に、CORP ドメインのユーザー アカウントが Office 365 に同期されていることを確認します。同期が始まるまで数分かかることがあります。

CLIENT1 の **[Active Directory 同期のセットアップと管理]** ページで、このページの手順 6. の **[ユーザー]** リンクをクリックします。ディレクトリ同期が正常に実行された場合、次のような内容が表示されます。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

**[状態]** 列は、Active Directory ドメインとの同期によってアカウントが取得されたことを示しています。

次に、Lynda Myer の Active Directory アカウントを使用して、Office 365 パスワードの同期について説明します。

1.	CLIENT1 の **[アクティブなユーザー]** ページで、**Lynda Meyer** のアカウントを選択します。
2.	Lynda Meyer のアカウントのプロパティで、**[割り当て済みのライセンス]** の下にある **[編集]** をクリックします。
3.	**[ライセンスの割り当て]** タブで、**[ユーザーの所在地の設定]** で所在地 (米国など) を選択します。
4.	**[Microsoft Office 365 プラン E3]** を選択してから、**[保存]** をクリックします。
5.	Internet Explorer を閉じます。
6.	Internet Explorer を実行し、****http://portal.microsoftonline.com** にアクセスします。
7.	Lynda Meyer の Office 365 の資格情報を使用してログオンします。彼女のユーザー名は lyndam@<*架空の名前*>.onmicrosoft.com になります。パスワードは、Lynda Meyer の Active Directory ユーザー アカウントのパスワードです。
8.	ログオンに成功すると、Office 365 ポータルのメインのページが **"今日は差を付けてみよう"** のメッセージと共に表示されます。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
これで、この環境は、Office 365 DirSync 機能に依拠する Office 365 アプリケーションのテストの実行、または DirSync 機能と DS1 からのパフォーマンスのテストの準備が整いました。

## その他のリソース

[Microsoft Azure での Office 365 ディレクトリ同期 (DirSync) のデプロイ](http://technet.microsoft.com/library/dn635310.aspx)

[Office Servers とクラウドを使用したソリューション](http://technet.microsoft.com/library/dn262744.aspx)

[テスト用のハイブリッド クラウド環境の設定](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[テスト用のハイブリッド クラウドでの SharePoint イントラネット ファームの設定](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[テスト用のシミュレートされたハイブリッド クラウド環境の設定](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Azure ハイブリッド クラウド テスト環境](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Azure インフラストラクチャ サービス実装ガイドライン](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=AcomDC_1217_2015-->