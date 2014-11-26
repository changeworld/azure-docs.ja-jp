<properties linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="stevenka" documentationCenter="" services="" solutions="" authors="stevenka" title="How to install and configure Azure PowerShell" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stevenka;juneb" />

# Azure PowerShell のインストールおよび構成方法

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/ja-jp/manage/install-and-configure-cli/" title="クロス プラットフォーム CLI">クロス プラットフォーム CLI</a></div>

Windows PowerShell を使用して、Azure でさまざまなタスクを実行できます。Windows PowerShell は、コマンド プロンプトで対話形式によって操作する方法でも、スクリプトで自動的に処理する方法でも使用できます。Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。このコマンドレットを使用して、Azure Platform から配信されるソリューションやサービスを、作成、テスト、展開、管理できます。ほとんどの場合、このコマンドレットを使用して、Azure 管理ポータルで実行できるタスクと同じタスクを実行できます。たとえば、クラウド サービス、仮想マシン、仮想ネットワーク、Web サイトを作成および構成できます。

モジュールはダウンロード可能なファイルとして配布され、ソース コードは一般公開されているリポジトリで管理されます。ダウンロード可能なファイルへのリンクは、このトピックの後半にあるインストール手順の中で紹介しています。ソース コードの詳細については、[Azure PowerShell のコード リポジトリ][Azure PowerShell のコード リポジトリ]を参照してください。

このガイドでは、Azure Platform を管理するための Azure PowerShell のインストールとセットアップに関する基本情報について説明します。

## 目次

-   [Azure PowerShell を使用するための前提条件][Azure PowerShell を使用するための前提条件]
-   [方法: Azure PowerShell をインストールするには][方法: Azure PowerShell をインストールするには]
-   [方法: サブスクリプションへの接続][方法: サブスクリプションへの接続]
-   [コマンドレットの使用方法: 例][コマンドレットの使用方法: 例]
-   [ヘルプの表示][ヘルプの表示]
-   [その他のリソース][その他のリソース]

### <span id="Prereq"></span></a>Azure PowerShell を使用するための前提条件

Azure はサブスクリプション方式のプラットフォームです。つまり、プラットフォームを使用するにはサブスクリプションが必要です。ほとんどの場合、サブスクリプションを使用してコマンドレットでタスクを実行するには、サブスクリプション情報が必要です (ストレージ関連の一部のコマンドレットは、サブスクリプション情報なしで使用できます)。この情報を提供するため、サブスクリプションに接続するようにコンピューターを構成します。手順については、この記事の「方法: サブスクリプションへの接続」で説明しています。

> [WACOM.NOTE] バージョン 0.8.5 以降、Azure PowerShell モジュールの利用には、Microsoft .NET Framework 4.5 が必要です。

モジュールをインストールすると、インストーラーによって、システムに必要なソフトウェアがチェックされ、正しいバージョンの Windows PowerShell、.NET Framework など、すべての依存関係がインストールされます。

## <span id="Install"></span></a>方法: Azure PowerShell をインストールするには

Azure PowerShell モジュールは、[Microsoft Web Platform Installer][Microsoft Web Platform Installer] を実行してダウンロードおよびインストールすることができます。メッセージが表示されたら、**[実行]** をクリックします。Azure PowerShell モジュールおよびすべての依存関係がインストールされます。表示される指示に従って、インストールを完了します。

Azure に使用できるコマンドライン ツールの詳細については、「[コマンドライン ツール][コマンドライン ツール]」を参照してください。

モジュールをインストールすると、Azure PowerShell 用にカスタマイズされたコンソールもインストールされます。コマンドレットは、標準の Windows PowerShell コンソールまたは Azure PowerShell コンソールから実行できます。

どちらのコンソールでも、開く方法は、実行している Windows のバージョンによって異なります。

-   Windows 8 または Windows Server 2012 以降を実行しているコンピューターでは、組み込みの検索機能を使用できます。スタート画面で「**power**」と入力します。Windows PowerShell および Azure PowerShell が名前に含まれているすべてのアプリケーションが一覧表示されます。コンソールを開くには、いずれかのアプリケーションをクリックします。(アプリケーションをスタート画面にピン留めするには、そのアイコンを右クリックします)。

-   Windows 8 よりも前、または Windows Server 2012 より前のバージョンを実行しているコンピューターでは、[スタート] メニューを使用します。[スタート] メニューから、**[すべてのプログラム]**、**[Azure]**、**[Azure PowerShell]** の順にクリックします。

## <span id="Connect"></span></a>方法: サブスクリプションへの接続

Azure を使用するにはサブスクリプションが必要です。サブスクリプションがない場合は、「[Azure を使ってみる][Azure を使ってみる]」を参照してください。

コマンドレットでサービスを管理するには、サブスクリプションが必要です。Windows PowerShell にサブスクリプション情報を提供するには、次の 2 つの方法があります。1 つ目は、サブスクリプション情報を含む管理証明書を、ダウンロードして使用する方法です。もう 1 つは、Microsoft アカウントまたは組織のアカウントを使用して Azure にログインする方法です。サインインすると、Azure Active Directory (Azure AD) により資格情報が認証され、Azure PowerShell がアカウントを管理するためのアクセス トークンが返されます。

以下の情報を考慮して、適切な認証方法を選択してください。

-   通常は、簡単にサブスクリプションへのアクセスを管理できる Azure AD での認証をお勧めします。バージョン 0.8.6 へのアップデートを行うことによって、組織のアカウントを使用している場合でも、Azure AD 認証を使用した自動化のシナリオを利用できます。Azure AD での認証は、Azure リソース マネージャー API でも利用できます。
-   証明書方式では、サブスクリプションおよび証明書が有効な限りサブスクリプション情報を使用できます。ただし、この方式では、複数のユーザーがアクセスを許可されているアカウントなど、共有サブスクリプションへのアクセス管理が難しくなります。また、Azure リソース マネージャー API では、証明書による認証を行うことができません。

Azure の認証とサブスクリプション管理の詳細については、「[アカウント、サブスクリプション、管理者ロールを管理する][アカウント、サブスクリプション、管理者ロールを管理する]」を参照してください。

### Azure AD 方式を使用する

1.  Azure PowerShell コンソール ウィンドウを開きます。手順は「[方法: Azure PowerShell をインストールするには][方法: Azure PowerShell をインストールするには]」を参照してください。

2.  次のコマンドを入力します。

    `Add-AzureAccount`

3.  ウィンドウで、アカウントに関連付けられている電子メールとパスワードを入力します。

4.  Azure により資格情報が認証および保存され、ウィンドウが閉じます。

5.  バージョン 0.8.6 以降では、組織のアカウントをお持ちの場合、次のコマンドを入力することで、ポップアップ ウィンドウを表示できます。このコマンドは、組織アカウントのユーザー名とパスワードを入力するための標準の Windows PowerShell 資格情報ウィンドウをポップアップ ウィンドウとして表示します。

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  このコマンドを自動化スクリプトで使用する場合に、ポップアップ ウィンドウの表示を回避するには、次のスニペットを使用します。

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] この非対話型のログイン方法は組織のアカウントでのみ機能します。組織のアカウントは、組織で管理されるユーザーであり、組織の Azure Active Directory テナントで定義されています。組織のアカウントが現在なく、Azure サブスクリプションへのログインに Microsoft アカウントを使用している場合は、次に示している手順を使用して組織のアカウントを簡単に作成できます。
    >
    > 1.  [Azure 管理ポータル][Azure 管理ポータル]にログインし、**[Active Directory]** をクリックします。
    >
    > 2.  ディレクトリがない場合は、**[ディレクトリの作成]** を選択し、求められる情報を入力します。
    >
    > 3.  ディレクトリを選択し、新しいユーザーを追加します。この新しいユーザーが組織のアカウントです。
    >
    >     ユーザーの作成時、ユーザーの電子メール アドレスと仮パスワードの両方が通知されます。この情報は別の手順で使用するため保管しておいてください。
    >
    > 4.  管理ポータルで **[設定]**、**[Administrators]** の順に選択します。**[追加]** を選択し、共同管理者として新しいユーザーを追加します。これにより、組織のアカウントで Azure サブスクリプションを管理できるようになります。
    >
    > 5.  最後に、Azure ポータルからログアウトし、新しい組織のアカウントを使用してログインし直します。初めてこのアカウントを使用してログインする場合は、パスワードを変更するように求められます。
    >
    > Microsoft Azure での組織のアカウントの詳細については、「[Microsoft Azure への組織としてのサインアップ][Microsoft Azure への組織としてのサインアップ]」を参照してください。

### 証明書方式を使用する

Azure モジュールには、証明書をダウンロードしてインポートするためのコマンドレットが用意されています。

-   **Get-AzurePublishSettingsFile** コマンドレットで
    Azure 管理ポータル上の Web ページを開き、
    そこからサブスクリプション情報をダウンロードすることができます。この情報は発行設定ファイルに含まれています。

-   **Import-AzurePublishSettingsFile** は、モジュールが使用する発行設定ファイルをインポートします。このファイルには、セキュリティ資格情報が記載されている管理証明書が含まれています。

<div class="dev-callout"> 
<b>注</b>
<p>AzureResourceManager モジュールのコマンドレットでは、Azure AD 方式 (Add-AzureAccount) を使用する必要があります。AzureResourceManager モジュールのコマンドレットは、発行設定ファイルをサポートしていません。AzureResourceManager モジュールのコマンドレットの詳細については、「<a href="http://go.microsoft.com/fwlink/?LinkID=394765">Azure Resource Manager Cmdlets (Azure リソース マネージャー コマンドレット)</a>」を参照してください。</p> 
</div>

<div class="dev-callout"> 
<b>重要</b> 
<p>ここで、
<b>Get-AzurePublishSettingsFile</b> を使用してダウンロードした発行プロファイルは、その設定をインポートした後で
削除する必要があります。管理証明書にはセキュリティ資格情報が含まれているため、
許可のないユーザーによるアクセスを防ぐ必要があります。サブスクリプションに関する情報は、
<a href="http://manage.windowsazure.com/">Azure 管理ポータル</a>または <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Microsoft Online Services カスタマー ポータル</a>から入手できます。</p> 
</div>

1.  Azure アカウントの資格情報を使用して、[Azure 管理ポータル][2]にサインインします。

2.  Azure PowerShell コンソール ウィンドウを開きます。手順は「[方法: Azure PowerShell をインストールするには][方法: Azure PowerShell をインストールするには]」を参照してください。

3.  次のコマンドを入力します。

    `Get-AzurePublishSettingsFile`

4.  メッセージが表示されたら、発行プロファイルをダウンロードして保存し、
    発行設定ファイルのパスと名前をメモしておきます。この情報は、
    **Import-AzurePublishSettingsFile** コマンドレットを実行して設定をインポートする際に必要です。既定の場所と
    ファイル名の形式は次のとおりです。

    `C:\UsersC:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`lt;UserProfile\>\\Download\\[\*MySubscription\*-...]-\*downloadDate\*-credentials.publishsettings</code>

5.  プレースホルダーを Windows アカウント名、パス、ファイル名に置き換えて、次のようなコマンドを入力します。

    `Import-AzurePublishSettingsFile C:\UsersImport-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;UserProfile\>\\Downloads`Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`lt;SubscriptionName\>-credentials.publishsettings</code>

> [WACOM.NOTE] 発行設定をインポートした後、他のサブスクリプションに共同管理者として追加された場合は、このプロセスを繰り返して、新しい発行設定ファイルをダウンロードし、それらの設定をインポートする必要があります。サブスクリプションのサービスを管理する共同管理者の追加については、「Azure サブスクリプションの共同管理者の追加および削除[」を参照してください。][」を参照してください。]

### アカウントおよびサブスクリプションの詳細を表示する

Azure PowerShell で使用するためのアカウントとサブスクリプションは、複数所有することができます。Add-AzureAccount を繰り返し実行すると、複数のストレージ アカウントを追加できます。

利用できる Azure アカウントを表示するには、次のように入力します。

    Get-AzureAccount

Azure サブスクリプションを表示するには、次のように入力します。

    Get-AzureSubscription

## <span id="Ex"></span></a>コマンドレットの使用方法: 例

モジュールをインストールし、コンピューターでサブスクリプションへの接続を構成したら、Azure Web サイトを作成できます。Azure コマンドレットの使用方法を理解してください。

1.  Azure PowerShell コンソールを開きます。

2.  Web サイトの名前を入力します。DNS の名前付け規則に従った名前を付けてください。名前には「a」から「z」の文字、「0」から「9」の数字、ハイフン「-」のみが使用できます。

    Web サイト名は、Azure 内で一意である必要があります。この例では「mySite」を使用しますが、アカウント名に数字をプラスした名前などの別の名前を必ず指定してください。

    名前を決めたら、次のようにコマンドを入力します。「mySite」を Web サイト名に置き換えてください。

    `New-AzureWebsite mySite`

    このコマンドレットは、Web サイトを作成し、作成した新しい Web サイトを表すオブジェクトを返します。オブジェクトのプロパティには、Web サイトについての役に立つ情報が含まれています。

3.  Web サイトに関する情報を取得するには、このコマンドを入力します。このコマンドは、今作成したばかりの Web サイトを含む、サブスクリプションのすべての Web サイトについての簡単な情報を返します。

    `Get-AzureWebsite`

4.  Web サイトに関する詳しい情報を取得するには、コマンドに Web サイト名を追加します。「mySite」を必ず Web サイト名に置き換えてください。

    `Get-AzureWebsite -Name mySite`

5.  Web サイトは作成すると開始されます。Web サイトを停止するには、このコマンドに Web サイト名を追加して入力します。

    `Stop-AzureWebsite -Name mySite`

6.  サイトが停止状態 (stopped) であることを確認するには、Get-AzureWebsite コマンドをもう一度実行します。

    `Get-AzureWebsite`

7.  このテストを完了するために、Web サイトを削除します。次のコマンドを入力します。

    `Remove-AzureWebsite -Name mySite`

8.  タスクを完了するために、Web サイトが削除されていることを確認します。

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>ヘルプの表示

これらのリソースは、特定のコマンドレットに関するヘルプ情報を提供します。

-   コンソールから、組み込みのヘルプ システムを使用できます。**Get-Help** コマンドレットを実行すると、ヘルプ システムにアクセスできます。次の表は、ヘルプを表示するためのコマンドの例です。コンソールで「**help**」と入力すると、詳細情報を表示できます。

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>コマンド</strong></td>
    <td align="left"><strong>結果</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">ヘルプ システムの使用方法を表示します。
    <p><strong>メモ</strong>:この説明には、Azure モジュールには該当しないヘルプ ファイルの情報も含まれています。ヘルプ ファイルは、モジュールのインストール時にインストールされます。個別にダウンロードすることはできません。</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help Azure</td>
    <td align="left">Azure モジュール内のすべてのコマンドレットを取得します。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>language</strong>&gt;-dev</td>
    <td align="left">指定された言語でアプリケーションの開発と管理に使用するコマンドレットを取得します。たとえば、help node-dev、help php-dev、または help python-dev のように指定します。</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">Windows PowerShell コマンドレットについてのヘルプを取得します。<cmdlet> をコマンドレット名に置き換えます。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parameter *</td>
    <td align="left">コマンドレット パラメーターの詳細を取得します。アスタリスク (*) は、「すべて」を意味します。</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Examples</td>
    <td align="left">コマンドレットを使用するための構文と例を取得します。</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Full</td>
    <td align="left">技術的詳細を含む、コマンドレットのすべてのヘルプを取得します。</td>
    </tr>
    </tbody>
    </table>

-   Azure PowerShell モジュールの参照情報は、Azure ライブラリからも利用できます。詳細については、「[Azure コマンドレット リファレンス][Azure コマンドレット リファレンス]」を参照してください。

コミュニティの情報を参照する場合は、以下の人気フォーラムもご覧ください。

-   [MSDN 上の Azure フォーラム][MSDN 上の Azure フォーラム]
-   [Stackoverflow][Stackoverflow]

## <span id="Resources"></span></a> その他のリソース

Azure や Windows PowerShell の使用方法を学習するためのリソースもご利用いただけます。

-   コマンドレットについてのフィードバックや問題の報告をする場合や、ソース コードを閲覧する場合は、[Azure PowerShell のコード リポジトリ][Azure PowerShell のコード リポジトリ]を参照してください。

-   Windows PowerShell コマンド ラインおよびスクリプト環境については、[TechNet スクリプト センター][TechNet スクリプト センター]を参照してください。

-   Windows PowerShell のインストール、学習、使用、カスタマイズについては、「[Windows PowerShell][Windows PowerShell]」を参照してください。

-   Windows PowerShell のスクリプトの説明および使用方法については、「[スクリプトのサポート][スクリプトのサポート]」を参照してください。スクリプトの作成、スクリプトを実行するためのコンピューターの構成に関する基本情報を説明しています。

-   Azure AD のコマンドレットについては、「[Windows PowerShell を使用して Azure AD を管理する][Windows PowerShell を使用して Azure AD を管理する]」を参照してください。

  [Azure PowerShell のコード リポジトリ]: https://github.com/WindowsAzure/azure-sdk-tools
  [Azure PowerShell を使用するための前提条件]: #Prereq
  [方法: Azure PowerShell をインストールするには]: #Install
  [方法: サブスクリプションへの接続]: #Connect
  [コマンドレットの使用方法: 例]: #Ex
  [ヘルプの表示]: #Help
  [その他のリソース]: #Resources
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [コマンドライン ツール]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Azure を使ってみる]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [アカウント、サブスクリプション、管理者ロールを管理する]: http://go.microsoft.com/fwlink/?LinkId=324796
  [Azure 管理ポータル]: https://manage.windowsazure.com
  [Microsoft Azure への組織としてのサインアップ]: http://azure.microsoft.com/ja-jp/documentation/articles/sign-up-organization/
  [2]: http://manage.windowsazure.com
  [」を参照してください。]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg456328.aspx
  [Azure コマンドレット リファレンス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554330.aspx
  [MSDN 上の Azure フォーラム]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [Stackoverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [TechNet スクリプト センター]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [スクリプトのサポート]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [Windows PowerShell を使用して Azure AD を管理する]: http://go.microsoft.com/fwlink/p/?LinkId=320628
