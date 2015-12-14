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
	ms.date="12/01/2015"
	ms.author="coreyp"/>

# Azure PowerShell のインストールおよび構成方法#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

##Azure PowerShell とは#
Azure PowerShell は、Windows PowerShell を使用して Azure を管理するためのコマンドレットを提供するモジュールです。このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。ほとんどの場合、コマンドレットを使用して Microsoft Azure 管理ポータルと同じタスク (クラウド サービス、仮想マシン、仮想ネットワーク、Web アプリの作成や構成など) を実行できます。

モジュールとソース コードは、公開されているリポジトリでダウンロードできます。

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Azure PowerShell 1.0.1 のソース コード](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## 手順 1: インストール
[PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) をダウンロードしてインストールします。<a id="Connect"></a>

## 手順 2: 起動
モジュールをインストールすると、Azure PowerShell 用にカスタマイズされたコンソールもインストールされます。コマンドレットは、標準の Windows PowerShell コンソールまたは Azure PowerShell コンソールから実行できます。

## 手順 3: 接続
コマンドレットでサービスを管理するには、サブスクリプションが必要です。サブスクリプションがまだない場合は、Azure サブスクリプションを購入できます。手順については、[Azure の概要](http://go.microsoft.com/fwlink/p/?LinkId=320795)に関するページをご覧ください。

1. 「Add-AzureAccount」と入力します。

2. アカウントに関連付けられている電子メール アドレスとパスワードを入力します。Azure により資格情報が認証および保存され、ウィンドウが閉じます。

または

職場または学校のアカウントにサインインします。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	>
	> [AZURE.NOTE] This non-interactive login method only works with a work or school account. A work or school account is a user that is managed by your work or school, and defined in the Azure Active Directory instance for your work or school. If you do not currently have a work or school account, and are using a Microsoft account to log in to your Azure subscription, you can easily create one using the following steps.
	>
	> 1. Login to the [Azure Management Portal](https://manage.windowsazure.com), and click on **Active Directory**.
	>
	> 2. If no directory exists, select **Create your directory** and provide the requested information.
	>
	> 3. Select your directory and add a new user. This new user can sign in using a work or school account.
	>
	>     During the creation of the user, you will be supplied with both an e-mail address for the user and a temporary password. Save this  information as it is used in another step.
	>
	> 4. From the management portal, select **Settings** and then select **Administrators**. Select **Add**, and add the new user as a co-administrator. This allows the work or school account to manage your Azure subscription.
	>
	> 5. Finally, log out of the Azure portal and then log back in using the work or school account. If this is the first time logging in with this account, you will be prompted to change the password.
	>
	>For more information on signing up for Microsoft Azure with a work or school account, see [Sign up for Microsoft Azure as an Organization](sign-up-organization.md).


### アカウントおよびサブスクリプションの詳細を表示する

Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。**Add-AzureAccount** を繰り返し実行することで、複数のアカウントを追加できます。

利用できる Azure アカウントを表示するには、次のように入力します。

	Get-AzureAccount

Azure サブスクリプションを表示するには、次のように入力します。

	Get-AzureSubscription








## 手順 4: テスト<a id="Ex"></a>


モジュールをインストールし、サブスクリプションに接続するようにコンピューターを構成したら、Azure Web アプリを作成して、すべてが機能していることを確認できます。

1. Azure PowerShell コンソールを開きます。

2. Web アプリの名前を入力します。DNS の名前付け規則に従った名前を付けてください。名前には「a」から「z」の文字、「0」から「9」の数字、ハイフン「-」のみが使用できます。

	Web アプリの名前は、Azure 内で一意である必要があります。この例では「mySite」を使用しますが、アカウント名に数字をプラスした名前などの別の名前を必ず指定してください。

	名前を決めたら、次のようにコマンドを入力します。"mySite" は、Web アプリの名前に置き換えてください。

		New-AzureWebsite mySite

	このコマンドレットは Web アプリを作成し、新しい Web アプリを表すオブジェクトを返します。オブジェクトのプロパティには、Web アプリに関する有用な情報が含まれています。

3. Web アプリに関する情報を取得するには、このコマンドを入力します。このコマンドは、先ほど作成したばかりのものも含む、サブスクリプション内のすべての Web アプリについての簡単な情報を返します。

		Get-AzureWebsite

4. Web アプリに関する詳しい情報を取得するには、コマンドに Web アプリ名を含めます。"mySite" を Web アプリ名で置き換えてください。

		Get-AzureWebsite -Name mySite

5. Web アプリは、作成すると開始されます。Web アプリを停止するには、Web アプリ名を含めてこのコマンドを入力します。

		Stop-AzureWebsite -Name mySite

6. サイトが停止状態 (stopped) であることを確認するには、Get-AzureWebsite コマンドをもう一度実行します。

		Get-AzureWebsite

7. このテストを完了するために、Web アプリを削除します。次のコマンドを入力します。

		Remove-AzureWebsite -Name mySite

7. タスクを完了するために、Web アプリが削除されていることを確認します。

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>ヘルプの表示##

これらのリソースは、特定のコマンドレットに関するヘルプ情報を提供します。


-   コンソールから、組み込みのヘルプ システムを使用できます。**Get-Help** コマンドレットを実行すると、ヘルプ システムにアクセスできます。 



- Azure PowerShell モジュールの参照情報は、Azure ライブラリからも利用できます。詳細については、「[Azure コマンドレット リファレンス](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx)」を参照してください。

コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

- [MSDN 上の Azure フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1203_2015-->