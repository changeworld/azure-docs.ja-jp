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
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Azure PowerShell のインストールおよび構成方法#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Azure CLI</a></div>

Windows PowerShell を使用して、Azure でさまざまなタスクを実行できます。Windows PowerShell は、コマンド プロンプトで対話形式によって操作する方法でも、スクリプトで自動的に処理する方法でも使用できます。Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、デプロイ、管理できます。ほとんどの場合、このコマンドレットを使用して、Azure 管理ポータルで実行できるタスクと同じタスクを実行できます。たとえば、クラウド サービス、仮想マシン、仮想ネットワーク、Web アプリを作成および構成できます。

モジュールはダウンロード可能なファイルとして配布され、ソース コードは一般公開されているリポジトリで管理されます。ダウンロード可能なファイルへのリンクは、このトピックの後半にあるインストール手順の中で紹介しています。ソース コードの詳細については、[Azure PowerShell のコード リポジトリ](https://github.com/Azure/azure-powershell)を参照してください。

このガイドでは、Azure Platform を管理するための Azure PowerShell のインストールとセットアップに関する基本情報について説明します。

### <a id="Prereq"></a>Azure PowerShell を使用するための前提条件

Azure はサブスクリプション方式のプラットフォームです。つまり、プラットフォームを使用するにはサブスクリプションが必要です。ほとんどの場合、サブスクリプションを使用してコマンドレットでタスクを実行するには、サブスクリプション情報が必要です(ストレージ関連の一部のコマンドレットは、サブスクリプション情報なしで使用できます)。 この情報を提供するため、サブスクリプションに接続するようにコンピューターを構成します。手順については、この記事の「方法: サブスクリプションへの接続」で説明しています。

> [AZURE.NOTE]バージョン 0.8.5 以降、Azure PowerShell モジュールの利用には、Microsoft .NET Framework 4.5 が必要です。

モジュールをインストールすると、インストーラーによって、システムに必要なソフトウェアがチェックされ、正しいバージョンの Windows PowerShell、.NET Framework など、すべての依存関係がインストールされます。

<a id="Install"></a>
## 方法: Azure PowerShell をインストールするには

Azure PowerShell モジュールは、[Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376) を実行してダウンロードおよびインストールすることができます。メッセージが表示されたら、**[実行]** をクリックします。Azure PowerShell モジュールおよびすべての依存関係がインストールされます。表示される指示に従って、インストールを完了します。

> [AZURE.NOTE]PowerShell Installer のダウンロードだけを行う場合は、https://github.com/Azure/azure-powershell/releases にアクセスしてください。PowerShell コマンドレットのソース コードは、このリポジトリにもあります。

Azure に使用できるコマンドライン ツールの詳細については、「[コマンドライン ツール](http://go.microsoft.com/fwlink/?LinkId=320796)」を参照してください。

モジュールをインストールすると、Azure PowerShell 用にカスタマイズされたコンソールもインストールされます。コマンドレットは、標準の Windows PowerShell コンソールまたは Azure PowerShell コンソールから実行できます。

どちらのコンソールでも、開く方法は、実行している Windows のバージョンによって異なります。

- Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。スタート画面で「**power**」と入力します。Windows PowerShell および Azure PowerShell が名前に含まれているすべてのアプリケーションが一覧表示されます。コンソールを開くには、いずれかのアプリケーションをクリックします。(アプリケーションをスタート画面にピン留めするには、そのアイコンを右クリックします)。

- Windows 8 よりも前、または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、[スタート] メニューを使用します。[スタート] メニューから、**[すべてのプログラム]**、**[Azure]**、**[Azure PowerShell]** の順にクリックします。

<a id="Connect"></a>
## 方法: サブスクリプションへの接続

Azure を使用するにはサブスクリプションが必要です。サブスクリプションがない場合は、「[Azure を使ってみる](http://go.microsoft.com/fwlink/p/?LinkId=320795)」を参照してください。

コマンドレットでサービスを管理するには、サブスクリプションが必要です。Windows PowerShell にサブスクリプション情報を提供するには、次の 2 つの方法があります。サブスクリプション情報を含む管理証明書を使用します。または、Microsoft アカウントか、職場または学校のアカウントを使用して Azure にサインインします。サインインすると、Azure Active Directory (Azure AD) により資格情報が認証され、Azure PowerShell がアカウントを管理するためのアクセス トークンが返されます。

以下の情報を考慮して、適切な認証方法を選択してください。

- 通常は、簡単にサブスクリプションへのアクセスを管理できる Azure AD での認証をお勧めします。バージョン 0.8.6 へのアップデートを行うことによって、職場や学校のアカウントを使用している場合でも、Azure AD での認証を使用した自動化のシナリオを利用できます。Azure AD での認証は、Azure リソース マネージャー API でも利用できます。
- 証明書方式では、サブスクリプションおよび証明書が有効な限りサブスクリプション情報を使用できます。ただし、この方式では、複数のユーザーがアクセスを許可されているアカウントなど、共有サブスクリプションへのアクセス管理が難しくなります。また、Azure リソース マネージャー API では、証明書による認証を行うことができません。

Azure の認証とサブスクリプション管理の詳細については、「[アカウント、サブスクリプション、管理者ロールを管理する](http://go.microsoft.com/fwlink/?LinkId=324796)」を参照してください。

### Azure AD 方式を使用する

1. Azure PowerShell コンソール ウィンドウを開きます。手順は「[方法: Azure PowerShell をインストールするには](#Install)」を参照してください。

2. 次のコマンドを入力します。

		Add-AzureAccount

3. ウィンドウで、アカウントに関連付けられている電子メールとパスワードを入力します。

4. Azure により資格情報が認証および保存され、ウィンドウが閉じます。

5. バージョン 0.8.6 以降で、職場または学校のアカウントを使用してサインインする場合、次のコマンドを入力するとポップアップ ウィンドウが表示されなくなります。このコマンドは、職場または学校のアカウントのユーザー名とパスワードを入力するための標準の Windows PowerShell 資格情報ウィンドウをポップアップ ウィンドウとして表示します。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]セキュリティと資格情報の使用の詳細については、[ASP.NET と Azure Websites でパスワードや他の機密情報をデプロイするときのベスト プラクティスに関するページ](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)を参照してください。

	> [AZURE.NOTE]この非対話型のログイン方法は、職場または学校のアカウントでのみ機能します。職場または学校のアカウントは、職場または学校によって管理されているユーザーで、職場または学校の Azure Active Directory インスタンスで定義されています。現在、職場または学校のアカウントがなく、Microsoft アカウントを使用して Azure サブスクリプションにログインしている場合は、次の手順を使用して簡単に職場または学校のアカウントを作成できます。
	>
	> 1. [Azure 管理ポータル](https://manage.windowsazure.com)にログインし、**[Active Directory]** をクリックします。
	>
	> 2. ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。
	>
	> 3. ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーは、職場または学校のアカウントを使用してサインインできます。
	>
	>     ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は別の手順で使用するため保管しておいてください。
	>
	> 4. 管理ポータルで **[設定]**、**[Administrators]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、職場または学校のアカウントで Azure サブスクリプションを管理できるようになります。
	>
	> 5. 最後に、Azure ポータルからログアウトし、職場または学校のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
	>
	>職場または学校のアカウントを使用して Microsoft Azure にサインアップする方法の詳細については、[組織としての Microsoft Azure へのサインアップに関するページ](sign-up-organization.md)を参照してください。

### 証明書方式を使用する

Azure モジュールには、証明書をダウンロードしてインポートするためのコマンドレットが用意されています。

> [AZURE.NOTE]AzureResourceManager モジュールのコマンドレットでは、Azure AD 方式 (Add-AzureAccount) を使用する必要があります。AzureResourceManager モジュールのコマンドレットは、発行設定ファイルをサポートしていません。AzureResourceManager モジュールのコマンドレットの詳細については、「[Azure Resource Manager Cmdlets (Azure リソース マネージャー コマンドレット)](http://go.microsoft.com/fwlink/?LinkID=394765)」を参照してください。


- **Get-AzurePublishSettingsFile** コマンドレットで Azure 管理ポータル上の Web ページを開き、そこからサブスクリプション情報をダウンロードすることができます。この情報は発行設定ファイルに含まれています。

- **Import-AzurePublishSettingsFile** は、モジュールが使用する発行設定ファイルをインポートします。このファイルには、セキュリティ資格情報が記載されている管理証明書が含まれています。

> [AZURE.IMPORTANT]これらの設定をインポートした後、<b>Get-AzurePublishSettingsFile</b> を使用してダウンロードした発行設定ファイルを削除することをお勧めします。管理証明書にはセキュリティ資格情報が含まれているため、未承認ユーザーによるアクセスを防ぐ必要があります。サブスクリプションに関する情報は、[Azure 管理ポータル](http://manage.windowsazure.com/)または [Microsoft Online Services カスタマー ポータル](http://go.microsoft.com/fwlink/p/?LinkId=324875)から入手できます。

1. Azure アカウントの資格情報を使用して、[Azure 管理ポータル](http://manage.windowsazure.com)にサインインします。

2. Azure PowerShell コンソール ウィンドウを開きます。手順は「[方法: Azure PowerShell をインストールするには](#Install)」を参照してください。

3. 次のコマンドを入力します。

		Get-AzurePublishSettingsFile

4. メッセージが表示されたら、発行プロファイルをダウンロードして保存し、発行設定ファイルのパスと名前をメモしておきます。この情報は、**Import-AzurePublishSettingsFile** コマンドレットを実行して設定をインポートする際に必要です。既定の場所とファイル名の形式は次のとおりです。

			C:\\Users<UserProfile>\\Download\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. プレースホルダーを Windows アカウント名、パス、ファイル名に置き換えて、次のようなコマンドを入力します。

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]発行設定をインポートした後、他のサブスクリプションに共同管理者として追加された場合は、このプロセスを繰り返して、新しい発行設定ファイルをダウンロードし、それらの設定をインポートする必要があります。サブスクリプションのサービスを管理する共同管理者の追加の詳細については、[Azure サブスクリプションへの共同管理者の追加と削除に関するページ](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx)を参照してください。

### アカウントおよびサブスクリプションの詳細を表示する

Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。Add-AzureAccount を繰り返し実行すると、複数のストレージ アカウントを追加できます。

利用できる Azure アカウントを表示するには、次のように入力します。

	Get-AzureAccount

Azure サブスクリプションを表示するには、次のように入力します。

	Get-AzureSubscription

## <a id="Ex"></a>コマンドレットの使用方法: 例 ##

モジュールをインストールし、コンピューターでサブスクリプションへの接続を構成したら、Azure Web アプリを作成できます。Azure コマンドレットの使用方法を理解してください。

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


-   コンソールから、組み込みのヘルプ システムを使用できます。**Get-Help** コマンドレットを実行すると、ヘルプ システムにアクセスできます。次の表は、ヘルプを表示するためのコマンドの例です。コンソールで「**help**」と入力すると、詳細情報を表示できます。

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>コマンド</b></td>
	<td><b>結果</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>ヘルプ システムの使用方法を表示します。<p><b>注</b>: この説明には、Azure モジュールには該当しないヘルプ ファイルの情報も含まれています。ヘルプ ファイルは、モジュールのインストール時にインストールされます。個別にダウンロードすることはできません。</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help Azure</td>
	<td>Azure モジュール内のすべてのコマンドレットを取得します。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>language</b>>-dev</td>
	<td>指定された言語でアプリケーションの開発と管理に使用するコマンドレットを取得します。たとえば、help node-dev、help php-dev、または help python-dev のように指定します。</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>></td>
	<td>Windows PowerShell コマンドレットについてのヘルプを取得します。<cmdlet> をコマンドレット名に置き換えます。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Parameter *</td>
	<td>コマンドレット パラメーターの詳細を取得します。アスタリスク (*) は、「すべて」を意味します。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Examples</td>
	<td>コマンドレットを使用するための構文と例を取得します。</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Full</td>
	<td>技術的詳細を含む、コマンドレットのすべてのヘルプを取得します。</td>
</tr>
</tbody>
</table>



- Azure PowerShell モジュールの参照情報は、Azure ライブラリからも利用できます。詳細については、「[Azure コマンドレット リファレンス](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx)」を参照してください。

コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

- [MSDN 上の Azure フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [StackOverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>その他のリソース ##

Azure や Windows PowerShell の使用方法を学習するためのリソースもご利用いただけます。

- Azure Storage コンポーネントにアクセスする方法の詳細については、[Azure Storage での Azure PowerShell の使用に関するページ](storage-powershell-guide-full.md)を参照してください。

- コマンドレットについてのフィードバックや問題の報告をする場合や、ソース コードを閲覧する場合は、[Azure PowerShell のコード リポジトリ](https://github.com/WindowsAzure/azure-sdk-tools)を参照してください。

- Windows PowerShell コマンド ラインおよびスクリプト環境については、[TechNet スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=320211)を参照してください。

- Windows PowerShell のインストール、学習、使用、カスタマイズについては、「[Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210)」を参照してください。

- Windows PowerShell のスクリプトの説明および使用方法については、「[スクリプトのサポート](http://go.microsoft.com/fwlink/p/?LinkId=320627)」を参照してください。スクリプトの作成、スクリプトを実行するためのコンピューターの構成に関する基本情報を説明しています。

- Azure AD のコマンドレットについては、「[Windows PowerShell を使用して Azure AD を管理する](http://go.microsoft.com/fwlink/p/?LinkId=320628)」を参照してください。





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO3-->