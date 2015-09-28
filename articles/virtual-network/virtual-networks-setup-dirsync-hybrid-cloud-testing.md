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
	ms.date="09/10/2015" 
	ms.author="josephd"/>

# テスト用のハイブリッド クラウドでの Office 365 ディレクトリ同期 (DirSync) の設定

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルを使用したリソースの作成について説明します。

このトピックでは、Microsoft Azure にホストされているパスワードの同期を使用して Office 365 ディレクトリ同期 (DirSync) をテストするためのハイブリッド クラウド環境を作成する手順全体を説明します。完成すると次のような構成になります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
この構成は、インターネット上のご使用の場所から Azure 運用環境の DirSync サーバーをシミュレートします。構成は次のとおりです。

- 簡略化されたオンプレミス ネットワーク (Corpnet サブネット)。
- Azure でホストされているクロスプレミス仮想ネットワーク (TestVNET)。
- サイト間 VPN 接続
- Office 365 FastTrack 試用版のサブスクリプション。
- TestVNET 仮想ネットワーク内の DirSync サーバーとセカンダリ ドメイン コントローラー。

この構成を基盤や共通の出発点にして以下を実行できます。

- パスワード同期を使用して、オンプレミスの Active Directory ドメインとの同期に依拠する Office 365 のアプリケーションを開発とテストする。
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

次に、新しい Office 365 FastTrack 試用版にサインアップします。

1.	CORP\\User1 アカウント資格情報で CLIENT1 にログオンします。
2.	Internet Explorer を開き、****http://fasttrack.office.com** にアクセスします。
3.	**[Getting started with FastTrack]** をクリックします。
4.	[Getting Started with FastTrack] ページで、**[First, sign up for an Office 365 trial]** の下の **[For enterprises, sign up here]** をクリックします。
5.	手順 1. のページで、**[Business email address]** に新しい Microsoft アカウントを指定してページに入力してから、**[Next]** をクリックします。
6.	手順 2. のページの最初のフィールドに初期の Office 365 アカウントの名前を入力し、その後に仮の会社名とパスワードを入力します。作成された電子メール アドレス (user123@contoso123.onmicrosoft.com など) とパスワードを安全な場所に記録します。フェーズ 3 の Active Directory Sync ツール構成ウィザードを完了するときにこの情報が必要になります。**[次へ]** をクリックします。
7.	手順 3. のページで、テキスト メッセージを利用可能な携帯電話やスマート フォンの電話番号を入力し、**[Text me]** をクリックします。
8.	電話でテキスト メッセージを受信した後に、認証コードを入力してから、**[Create my account]** をクリックします。 
9.	Office 365 でアカウントが作成されたら、**[You're ready to go]** をクリックします。
10.	これで、Office 365 ポータルのメインのページが表示されます。上部のリボンで、**[管理者]**、**[Office 365]** の順にクリックします。Office 365 管理センター ページが表示されます。CLIENT1 でこのページを開いたままにします。

現在の構成は次のようになります。

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## フェーズ 3: DirSync サーバー (DS1) を構成する

まず、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、DS1 用に Azure 仮想マシンを作成します。これらのコマンドを実行する前に、変数の値を入力し、< and > の文字を削除します。

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential â€“Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential â€“UserName "CORP\User1" â€“Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM â€“ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

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

次に、DS1 にディレクトリ同期をインストールします。

1.	Internet Explorer を実行し、アドレス バーに「****http://go.microsoft.com/fwlink/?LinkID=278924**」と入力してから、Enter キーを押します。dirsync.exe を実行するように求められたら、**[保存]** の横にある矢印をクリックしてから、**[名前を付けて保存]**、**[保存]** の順にクリックして、ファイルをダウンロード フォルダーに保存します。ツールのインストールの詳細については、「[ディレクトリ同期ツールをインストールまたはアップグレードする](http://technet.microsoft.com/library/jj151800)」をご覧ください。
2.	**ダウンロード** フォルダーを開き、**dirsync** ファイルを右クリックして、**[管理者として実行]** をクリックします。
3.	Active Directory 同期のセットアップ ウィザードの [ようこそ] ページで、**[次へ]** をクリックします。 
4.	[ライセンス条項] ページで、**[同意する]** をクリックしてから、**[次へ]** をクリックします。
5.	[インストール先フォルダーの選択] ページで **[次へ]** をクリックします。インストールが完了するまで数分かかる場合があります。
6.	[完了] ページで、**[構成ウィザードを今すぐ開始する]** をオフにしてから、**[完了]** をクリックします。
7.	スタート画面で、**user1** をクリックして、**[サインアウト]** をクリックします。

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

次に、DS1 のディレクトリ同期を構成します。

1.	CORP\\User1 アカウントで DS1 にログインします。
2.	**[スタート]** 画面で、「**ディレクトリ同期**」と入力します。
3.	**[ディレクトリ同期の構成]** を右クリックし、**[管理者として実行]** をクリックします。これによって、構成ウィザードが開始されます。
4.	[ようこそ] ページで **[次へ]** をクリックします。
5.	[Microsoft Azure Active Directory の資格情報] ページで、フェーズ 2 で Office 365 FastTrack 試用版をセットアップするときに作成した初期アカウントの電子メール アドレスとパスワードを入力します。[次へ] をクリックします。 
6.	[Active Directory の資格情報] ページで、**[ユーザー名]** に「**CORP\\User1**」と入力し、**[パスワード]** に User1 アカウントのパスワードを入力します。**[次へ]** をクリックします。
7.	[混合環境] ページで、**[混合環境を有効にする]** を選択してから、**[次へ]** をクリックします。
8.	[パスワード同期] ページで、**[パスワード同期を有効にする]** を選択してから、**[次へ]** をクリックします。
9.	[構成] ページが表示されます。構成が完了したら、**[次へ]** をクリックします。
10.	[完了] ページで、**[完了]** をクリックします。メッセージが表示されたら、**[OK]** をクリックします。

次に、CORP ドメインのユーザー アカウントが Office 365 に同期されていることを確認します。同期が始まるまで数時間かかることがあります。

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


 

<!---HONumber=Sept15_HO3-->