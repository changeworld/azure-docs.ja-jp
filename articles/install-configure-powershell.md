<properties linkid="Install-Config-Windows-Azure-PowerShell" UrlDisplayName="Windows Azure PowerShell" pageTitle="Windows Azure PowerShell のインストールおよび構成方法" description="Windows Azure PowerShell をインストールして構成する方法について説明します。" umbracoNaviHide="0" disqusComments="1" writer="kathydav" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="" title="Windows Azure PowerShell のインストールおよび構成方法" />

# Windows Azure PowerShell のインストールおよび構成方法#

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" Title="PowerShell" class="current">PowerShell</a> <a href="/en-us/manage/install-and-configure-cli/" Title="クロス プラットフォーム CLI">クロス プラットフォーム CLI</a></div>

Windows PowerShell を使用して、Windows Azure でさまざまなタスクを実行できます。コマンド プロンプトで対話形式によって操作する方法とスクリプトで自動的に処理する方法があります。Windows Azure PowerShell は、Windows PowerShell から Windows Azure を管理するコマンドレットを提供するモジュールです。このコマンドレットを使用して、Windows Azure プラットフォームから配信されるソリューションやサービスを、作成、テスト、デプロイ、および管理できます。ほとんどの場合、このコマンドレットを使用して、Windows Azure の管理ポータルで実行できるタスクと同じタスクを実行できます。たとえば、クラウド サービス、仮想マシン、仮想ネットワーク、Web サイトを作成および構成できます。

モジュールはダウンロード可能なファイルとして配布され、ソース コードは一般公開されているリポジトリで管理されます。ダウンロード可能なファイルへのリンクは、このトピックの後半にあるインストール手順の中で紹介しています。ソース コードの詳細については、[Windows Azure PowerShell のコード リポジトリ](https://github.com/WindowsAzure/azure-sdk-tools)を参照してください。

このガイドでは、Windows Azure プラットフォームを管理するための Windows Azure PowerShell のインストールとセットアップに関する基本情報について説明します。

## 目次
  
 * [Windows Azure PowerShell を使用するための前提条件](#Prereq)
 * [Windows Azure PowerShell のインストール方法](#Install)
 * [サブスクリプションに接続する方法](#Connect)
 * [コマンドレットの使用方法: 例](#Ex)
 * [ヘルプの表示](#Help)
 * [その他のリソース](#Resources)


### <a id="Prereq"></a>Windows Azure PowerShell を使用するための前提条件

Windows Azure はサブスクリプション方式のプラットフォームです。つまり、プラットフォームを使用するにはサブスクリプションが必要です。ほとんどの場合、サブスクリプションを使用してコマンドレットでタスクを実行するには、サブスクリプション情報が必要です (ストレージ関連の一部のコマンドレットは、サブスクリプション情報なしで使用できます)。この情報を提供するため、サブスクリプションに接続するようにコンピューターを構成します。手順については、この記事の「サブスクリプションに接続する方法」で説明しています。

**注**: さまざまなサブスクリプション オプションを使用できます。詳細については、「[Windows Azure を使ってみる](http://go.microsoft.com/fwlink/p/?LinkId=320795)」を参照してください。

モジュールをインストールすると、インストーラーによって、システムに必要なソフトウェアがチェックされ、正しいバージョンの Windows PowerShell、.NET Framework など、すべての依存関係がインストールされます。

<h2> <a id="Install"></a>Windows Azure PowerShell のインストール方法</h2>

Windows Azure PowerShell モジュールは、[Microsoft Web プラットフォーム インストーラー](http://go.microsoft.com/fwlink/p/?LinkId=320376)を実行してダウンロードおよびインストールすることができます。メッセージが表示されたら、[**実行**] をクリックします。Microsoft Web プラットフォーム インストーラーにより、インストールに使用できる **Windows Azure PowerShell** が読み込まれます。Windows Azure PowerShell コマンドレットのすべての依存関係がインストールされます。表示される指示に従って、インストールを完了します。

Windows Azure に使用できるコマンド ライン ツールの詳細については、「[コマンド ライン ツール]( http://go.microsoft.com/fwlink/?LinkId=320796)」を参照してください。

モジュールをインストールすると、Windows PowerShell 用にカスタマイズされたコンソールもインストールされます。コマンドレットは、標準の Windows PowerShell コンソールまたは Windows Azure PowerShell コンソールから実行できます。

どちらのコンソールでも、開く方法は、実行している Windows のバージョンによって異なります。

- Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。スタート画面で「**power**」と入力します。これにより、Windows PowerShell および Windows Azure PowerShell が名前に含まれているすべてのアプリケーションが一覧表示されます。どちらかのアプリケーションをクリックして、コンソール ウィンドウを開きます (アプリケーションをスタート画面にピン留めするには、そのアイコンを右クリックします)。

- Windows 8 または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、[スタート] メニューを使用します。[スタート] メニューから、**[すべてのプログラム]**、**[Windows Azure]**、**[Windows Azure PowerShell]** の順にクリックします。


<h2><a id="Connect"></a>サブスクリプションに接続する方法</h2>

Windows Azure を使用するにはサブスクリプションが必要です。サブスクリプションがない場合は、「[Windows Azure を使ってみる](http://go.microsoft.com/fwlink/p/?LinkId=320795)」を参照してください。

コマンドレットでサービスを管理するために、サブスクリプション情報が必要です。.0.7 リリースの時点のモジュールでは、この情報は 2 つの方法で提供できます。サブスクリプション情報を含む管理証明書をダウンロードして使用できます。もう 1 つの方法では、Microsoft アカウントまたは組織 ID を使用して Windows Azure にログインします。ログインすると、Windows Azure Active Directory (Windows Azure AD) により資格情報が認証されます。

以下の情報を考慮して、適切な認証方法を選択してください。

- Windows Azure AD の方式では、サブスクリプションへのアクセスを簡単に管理できますが、自動化は利用できません。この資格情報は、Windows Azure PowerShell で 12 時間使用できます。有効期限が切れたら、もう一度ログインする必要があります。
- 証明書方式では、サブスクリプションおよび証明書が有効な限りサブスクリプション情報を使用できます。この方式は、長時間実行するタスクの自動化に適しています。サブスクリプション情報を一度ダウンロードしてインポートしたら、再度提供する必要はありません。ただし、この方式では、複数のユーザーがアクセスを許可されているアカウントなど、共有サブスクリプションへのアクセス管理が難しくなります。

Windows Azure での認証とサブスクリプション管理の詳細については、[この記事](http://go.microsoft.com/fwlink/?LinkId=324796)を参照してください。

<h3>Windows Azure AD 方式を使用する</h3>

1.Windows Azure PowerShell コンソール ウィンドウを開きます。手順は「[Windows Azure PowerShell のインストール方法](#Install)」を参照してください。

2.次のコマンドを入力します。

`Add-AzureAccount`

3.ウィンドウで、アカウントに関連付けられている電子メールとパスワードを入力します。

4.Windows Azure により資格情報が認証および保存され、ウィンドウが閉じます。

<h3>証明書方式を使用する</h3>

Windows Azure PowerShell モジュールには、証明書をダウンロードしてインポートするためのコマンドレットが用意されています。

- **Get-AzurePublishSettingsFile** コマンドレットで Windows Azure の管理
ポータル上の Web ページを開き、そこからサブスクリプション情報
をダウンロードすることができます。この情報は .publishsettings ファイルに含まれています。

- **Import-AzurePublishSettingsFile** は、モジュールが使用する .publishsettings ファイルをインポートします。このファイルには、セキュリティ資格情報が記載されている管理証明書が含まれています。

<div class="dev-callout"> 
<b>重要</b>
<p><b>Get-AzurePublishSettingsFile</b> を使用してダウンロード
した発行プロファイルは、その設定をインポートした後で削除することを
お勧めします。管理証明書にはセキュリティ資格情報が含まれているため、許可のない
ユーザーによるアクセスを防ぐ必要があります。サブスクリプションに関する
情報は、<a href="http://manage.windowsazure.com/">Windows Azure の管理ポータル</a>または <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services カスタマー ポータル</a>から入手できます。</p>
</div>

1.Windows Azure アカウントの資格情報を使用して、[Windows Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。

2.Windows Azure PowerShell コンソール ウィンドウを開きます。手順は「[Windows Azure PowerShell のインストール方法](#Install)」を参照してください。

3.次のコマンドを入力します。

`Get-AzurePublishSettingsFile`

4.メッセージが表示されたら、発行プロファイルをダウンロードして保存し、.publishsettings ファイルの
パスと名前をメモしておきます。この情報は、**Import-AzurePublishSettingsFile** 
コマンドレットを実行して設定をインポートする際に必要です。既定の場所
とファイル名の形式は次のとおりです。

	C:\\Users\&lt;UserProfile&gt;\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5.プレースホルダーを Windows アカウント名、パス、ファイル名に置き換えて、次のようなコマンドを入力します。

Import-AzurePublishSettingsFile C:\Users\&lt;UserProfile&gt;\Downloads\&lt;SubscriptionName&gt;-credentials.publishsettings

**注**: 
発行設定をインポートした後、他のサブスクリプションに共同管理者として追加された場合は、このプロセスを繰り返して、
新しい .publishsettings ファイルをダウンロードし、それらの設定をインポート
する必要があります。サブスクリプションのサービスを管理する共同管理者の追加については、
「[Windows Azure サブスクリプションの共同管理者の追加および削除](http://msdn.microsoft.com/en-us/library/windowsazure/gg456328.aspx)」を参照してください。

<h3> アカウントおよびサブスクリプションの詳細を表示する</h3>
Windows Azure PowerShell で使用するためのアカウントとサブスクリプションを複数を所有することができます。Add-AzureAccount を繰り返し実行すると、複数のストレージ アカウントを追加できます。

利用できるアカウントを表示するには、次のように入力します。

	`Get-AzureAccount`

サブスクリプション情報を表示するには、次のように入力します。

	`Get-AzureSubscription`

## <a id="Ex"></a>コマンドレットの使用方法: 例##

モジュールをインストールし、コンピューターでサブスクリプションへの接続を構成したら、コマンドレットの使用方法を理解するための例として、Web サイトを作成しましょう。

1.Windows Azure PowerShell を開きます (まだ開いていない場合)。

2.サイトに付ける名前を決めます。DNS の名前付け規則に従った名前にする必要があります。名前には「a」から「z」の文字、「0」から「9」の数字、ハイフン「-」のみを使用できます。また、Windows Azure 内で一意になっている必要があります。アカウント名の形式を使用すると、簡単に一意の名前を作成できます。

	名前を決めたら、次のようにコマンドを入力します。たとえば、複数使用できるようにアカウント名と番号を組み合わせて使用して Web サイトを作成するには、次のように入力します (実際のアカウント名に置き換えてください)。

	`New-AzureWebsite account-name-1`

	このコマンドレットは Web サイトを作成し、その新しい Web サイトに関する情報を一覧表示します。

3.Web サイトの状態を確認するには、次のように入力します。

	`Get-AzureWebsite`

	このコマンドレットは、新しい Web サイトの名前、状態、ホスト名を一覧表示します。

	**注**: このように実行すると、現在のサブスクリプションに関連付けられているすべての Web サイトの情報が一覧表示されます。

4.Web サイトは作成すると開始されます。Web サイトを停止するには、次のように入力します。

	`Stop-AzureWebsite -Name account-name-1`

5.Get-AzureWebsite コマンドをもう一度入力して、サイトが停止状態 (stopped) であることを確認します。
  
5.このテストを完了するために、Web サイトを削除します。次のコマンドを入力します。

	`Remove-AzureWebsite -Name account-name-1`

	削除されたことを確認して、このタスクを完了します。

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
		<td>help</td>
		<td>ヘルプ システムの使用方法を表示します。<p><b>注</b>: この説明には、Windows Azure モジュールには該当しないヘルプ ファイルの情報も含まれています。ヘルプ ファイルは、モジュールのインストール時にインストールされます。個別にダウンロードすることはできません。</p>
</td>
    </tr>
    <tr align="left" valign="top">
		<td>help azure</td>
		<td>Windows Azure PowerShell モジュールのすべてのコマンドレットを一覧表示します。</td>
    </tr>
	<tr align="left" valign="top">
		<td>help &lt;<b>language</b>&gt;-dev</td>
		<td>特定の言語でアプリケーションの開発と管理に使用するコマンドレットを一覧表示します。たとえば、help node-dev、help php-dev、または help python-dev のように指定します。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt;</td>
		<td>Windows PowerShell コマンドレットについてのヘルプを表示します。</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -parameter *</td>
		<td>コマンドレットのパラメーターの定義を表示します。たとえば、help get-azuresubscription -parameter と指定します。*</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -examples</td>
		<td> コマンドレットのコマンド例の構文と説明を表示します。</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -full</td>
		<td>コマンドレットの技術的な要件を表示します。</td>
    </tr>
    </tbody>
    </table>



- Windows Azure PowerShell モジュールのコマンドレットに関する参照情報は、Windows Azure ライブラリからも利用できます。詳細については、「[Windows Azure コマンドレット リファレンス](http://msdn.microsoft.com/en-us/library/windowsazure/jj554330.aspx)」を参照してください。

コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

- [MSDN 上の Windows Azure フォーラム]( http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>その他のリソース##

Windows Azure や Windows PowerShell の使用方法を学習するためのリソースもご利用いただけます。

- コマンドレットのフィードバック、レポートの問題、ソース コードの参照については、[Windows Azure PowerShell のコード リポジトリ](https://github.com/WindowsAzure/azure-sdk-tools)を参照してください。

- Windows PowerShell コマンド ラインおよびスクリプト環境については、[TechNet スクリプト センター](http://go.microsoft.com/fwlink/p/?LinkId=320211)を参照してください。

- Windows PowerShell のインストール、学習、使用、カスタマイズについては、「[Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210)」を参照してください。

- Windows PowerShell のスクリプトの説明および使用方法については、「[スクリプトのサポート](http://go.microsoft.com/fwlink/p/?LinkId=320627)」を参照してください。スクリプトの作成、スクリプトを実行するためのコンピューターの構成に関する基本情報を説明しています。

- Windows Azure AD のコマンドレットについては、「[Windows PowerShell を使用して Windows Azure AD を管理する](http://go.microsoft.com/fwlink/p/?LinkId=320628)」を参照してください。



  
  
  [Microsoft Online Services カスタマー ポータル]: https://mocp.microsoftonline.com/site/default.aspx
 
  


