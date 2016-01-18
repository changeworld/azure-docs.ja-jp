<properties
	pageTitle="Azure PowerShell のインストールおよび構成方法"
	description="Azure PowerShell のインストールと構成の方法について説明します。"
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Azure PowerShell のインストールおよび構成方法#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Azure PowerShell とは#
Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供するモジュールです。このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。ほとんどの場合、コマンドレットを使用して Microsoft Azure 管理ポータルと同じタスク (クラウド サービス、仮想マシン、仮想ネットワーク、Web アプリの作成や構成など) を実行できます。

<a id="Install"></a>
## 手順 1: インストール

Azure PowerShell は、以下に示す 2 つの方法でインストールできます。WebPI からインストールする方法と PowerShell ギャラリーからインストールする方法です。

> [AZURE.NOTE]Windows PowerShell Integrated Scripting Environment (ISE) のすべてのコマンドを表示するには、インストール後に再起動が必要になる場合があります。

###WebPI から Azure PowerShell をインストールする

Azure PowerShell 1.0 以降は、0.9.x までと同じ方法で WebPI からインストールすることができます。[Azure Powershell](http://aka.ms/webpi-azps) をダウンロードしてインストールを開始します。Azure PowerShell 0.9.x をインストールしている場合は、0.9.x のアンインストールを要求するメッセージが表示されますAzure PowerShell モジュールを PowerShell ギャラリーからインストールした場合、Azure PowerShell 環境の整合性を確保するために、インストール前にモジュールを削除するよう求められます。

> [AZURE.NOTE]PowerShell ギャラリーから Azure モジュールをインストールした場合、それらをアンインストールするよう求められます。これは、インストールしたモジュールとその場所に関して混乱を防ぐための措置です。PowerShell ギャラリー モジュールは通常、**%ProgramFiles%\\WindowsPowerShell\\Modules** にインストールされます。これに対し、WebPI インストーラーを使用した場合、Azure モジュールは **%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell** にインストールされます。モジュールをアンインストールするには **PowerShellGet** を使用します。このとき、アンインストール中にモジュールの依存関係が読み込まれている場合、ロックされている .dll とそのフォルダーはそのまま残されます。PowerShell ギャラリー モジュールをアンインストールしたにもかかわらずインストールでエラーが発生する場合は、**%ProgramFiles%\\WindowsPowerShell\\Modules** フォルダーから Azure* フォルダーを削除してください。

PowerShell ギャラリーからインストールした Azure PowerShell が既に存在する状態で WebPI インストールを使用する必要がある場合は、WebPI からインストールする前に次のコマンドを実行してください。

    # Uninstall the AzureRM component modules
    Uninstall-AzureRM

    # Uninstall AzureRM module
    Uninstall-Module AzureRM

    # Uninstall the Azure module
    Uninstall-Module Azure

    # Or, you can remove all Azure modules
    # Uninstall-Module Azure* -Force

> [AZURE.NOTE]PowerShell **$env:PSModulePath** には、WebPI からのインストール時に生じる既知の問題があります。システムの更新プログラムやその他のインストールでコンピューターの再起動が必要になった場合、Azure PowerShell のインストール パスが **$env:PSModulePath** に反映されない可能性があります。この問題は、コンピューターを再起動するか、**$env:PSModulePath** に Azure PowerShell のパスを追加することによって修正できます。

###ギャラリーから Azure PowerShell をインストールする

Azure PowerShell 1.0 以降をギャラリーからインストールするには、次のコマンドを使用します。

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####前述のコマンドの詳細

- **Install-Module AzureRM** は、AzureRM モジュールのブートストラップ モジュールをインストールします。このモジュールには、安全にかつ整合性のある方法で AzureRM モジュールの更新、アンインストール、インポートを行うために必要なコマンドレットが含まれています。メジャー バージョンの AzureRM に支障をきたす重大な変更を招かないよう、必要なバージョン範囲 (最小と最大) とモジュールのリストが記述されます。セマンティック バージョニングの詳細については、[semver.org](http://semver.org) を参照してください。これを利用し、特定のバージョンの AzureRM を使って独自のコマンドレットを作成することもできます。ブートストラップを介してモジュールをインストールすることによって、重大な変更が紛れ込むのを確実に防ぐことが可能です。
- **Install-AzureRM** は、ブートストラップ モジュールに宣言されているすべてのモジュールをインストールします。
- **Install-Module Azure** は、Azure モジュールをインストールします。これは、Azure PowerShell 0.9.x のサービス管理モジュールとなります。大きな変更はなく、以前のバージョンの Azure モジュールと互換性があります。
- **Import-AzureRM** は、AzureRM モジュールの一連のモジュールとバージョンに該当するすべてのモジュールをインポートします。AzureRM モジュールに必要なバージョン範囲内の Azure PowerShell モジュールを確実に読み込むことができます。
- **Import-Module Azure** は、Azure サービス管理モジュールをインポートします。PowerShell セッションには Azure モジュールと AzureRM モジュールが読み込まれ、両方のモジュールを併用することができます。


## 手順 2: 起動
モジュールをインストールすると、Azure PowerShell 用にカスタマイズされたコンソールもインストールされます。コマンドレットは、標準の Windows PowerShell コンソールまたは Azure PowerShell コンソールから実行できます。どちらのコンソールでも、開く方法は、実行している Windows のバージョンによって異なります。

- Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。**スタート**画面で「power」と入力します。Windows PowerShell および Azure PowerShell が名前に含まれているすべてのアプリケーションが一覧表示されます。コンソールを開くには、いずれかのアプリケーションをクリックします。アプリケーションを**スタート**画面にピン留めするには、そのアイコンを右クリックします。

- Windows 8 よりも前、または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、**[スタート]** メニューを使用します。**[スタート]** メニューから、**[すべてのプログラム]**、**[Azure]**、**[Azure PowerShell]** の順にクリックします。

Windows PowerShell コンソールで行う作業の多くは、**Windows PowerShell ISE** からメニュー項目とキーボード ショートカットを使用して実行することもできます。ISE を使用するには、Windows PowerShell コンソール、Cmd.exe、または **[ファイル名を指定して実行]** ボックスに「**powershell\_ise.exe**」と入力します。

###基本的なコマンド

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## 手順 3: 接続
コマンドレットでサービスを管理するには、サブスクリプションが必要です。サブスクリプションがまだない場合は、Azure サブスクリプションを購入できます。手順については、「[Azure の購入方法](http://go.microsoft.com/fwlink/p/?LinkId=320795)」を参照してください。

1. 「**Login-AzureRmAccount**」と入力します。

2. アカウントに関連付けられている電子メール アドレスとパスワードを入力します。Azure により資格情報が認証および保存され、ウィンドウが閉じます。

または

職場または学校のアカウントにサインインします。

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE]組織アカウントに複数のテナントが関連付けられている場合、TenantId パラメーターを指定します。

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE]この非対話型のログイン方法は、職場または学校のアカウントでのみ機能します。職場または学校のアカウントは、職場または学校によって管理されているユーザーで、職場または学校の Azure Active Directory インスタンスで定義されています。現在、職場または学校のアカウントがなく、Microsoft アカウントを使用して Azure サブスクリプションにログインしている場合は、次の手順を使用して簡単に職場または学校のアカウントを作成できます。

> 1. [Azure 管理ポータル](https://manage.windowsazure.com)にログインし、**[Active Directory]** をクリックします。

> 2. ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。

> 3. ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーは、職場または学校のアカウントを使用してサインインできます。ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は、以降の手順 5. で使用するため保管しておいてください。

> 4. 管理ポータルで **[設定]**、**[Administrators]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、職場または学校のアカウントで Azure サブスクリプションを管理できるようになります。

> 5. 最後に、Azure ポータルからログアウトし、職場または学校のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。

> 職場または学校のアカウントを使用して Microsoft Azure にサインアップする方法の詳細については、[組織としての Microsoft Azure へのサインアップに関するページ](sign-up-organization.md)を参照してください。

> Azure の認証とサブスクリプション管理の詳細については、「[アカウント、サブスクリプション、管理者ロールを管理する](http://go.microsoft.com/fwlink/?LinkId=324796)」を参照してください。

### アカウントおよびサブスクリプションの詳細を表示する

Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。**Add-AzureRmAccount** を繰り返し実行することで、複数のアカウントを追加できます。

使用可能な Azure アカウントを表示するには、「**Get-AzureAccount**」と入力します。

Azure サブスクリプションを表示するには、「**Get-AzureRmSubscription**」と入力します。

##<a id="Help"></a>ヘルプの表示##

これらのリソースは、特定のコマンドレットに関するヘルプ情報を提供します。


-   コンソールから、組み込みのヘルプ システムを使用できます。**Get-Help** コマンドレットを実行すると、ヘルプ システムにアクセスできます。 

- コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

 - [MSDN 上の Azure フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##詳細情報


コマンドレットの使用方法の詳細については、次のリソースを参照してください。

Windows PowerShell の基本的な使用手順については、「[Windows PowerShell を使用する](http://go.microsoft.com/fwlink/p/?LinkId=321939)」を参照してください。

コマンドレットのリファレンスについては、「[Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx)」を参照してください。

スクリプトを使用した Azure の管理に役立つサンプル スクリプトとその使用方法については、「[スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=321940)」を参照してください。

<!---HONumber=AcomDC_0107_2016-->