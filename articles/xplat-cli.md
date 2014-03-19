<properties linkid="script-xplat-intro" urlDisplayName="Windows Azure クロスプラットフォーム コマンド ライン インターフェイス" pageTitle="Windows Azure クロスプラットフォーム コマンド ライン インターフェイス" title="Windows Azure クロスプラットフォーム コマンド ライン インターフェイス" metaKeywords="windows azure cross-platform command-line interface, windows azure command-line, azure command-line, azure cli" description="Windows Azure サービスを管理するための Windows Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成" metaCanonical="http://www.windowsazure.com/en-us/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" writer="larryfr" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="" services="" />

#Windows Azure クロス プラットフォーム コマンド ライン インターフェイスのインストールと構成

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/manage/install-and-configure-windows-powershell/" title="PowerShell">PowerShell</a><a href="/en-us/manage/install-and-configure-cli/" title="クロス プラットフォーム CLI" class="current">クロス プラットフォーム CLI</a></div>

Windows Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) は、Windows Azure のプラットフォームを使用するための、オープン ソースのクロス プラットフォーム コマンドのセットです。xplat-cli で使用できる機能の多くは、Web サイトの管理、仮想マシン、モバイル サービス、SQL データベース、Windows Azure のプラットフォームの他のサービスなど、Windows Azure の管理ポータルで使用できるものと同じです。

xplat-cli は JavaScript で記述されており、Node.js が必要です。Windows Azure SDK for Node.js を使用して実装されており、Apache 2.0 ライセンスに基づいてリリースされています。プロジェクト リポジトリは、[https://github.com/WindowsAzure/azure-sdk-tools-xplat](https://github.com/WindowsAzure/azure-sdk-tools-xplat) にあります。

このドキュメントでは、Windows Azure クロス プラットフォーム コマンド ライン インターフェイスのインストール方法、およびこれを使用して Windows Azure のプラットフォームで基本的なタスクを実行する方法を説明します。

##このドキュメントの内容

* [Windows Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする方法](#install)
* [Windows Azure サブスクリプションに接続する方法](#configure)
* [Windows Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する方法](#use)
* [Windows Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する方法](#script)
* [その他のリソース](#additional-resources)

<h2><a id="install"></a>Windows Azure クロス プラットフォーム コマンド ライン インターフェイスをインストールする方法</h2>

xplat-cli のインストール方法は 2 つあります。Windows および OS X のインストーラー パッケージを使用する方法と、**npm** コマンドを使用する方法 (Node.js をインストール済みの場合) です。

Xamarin ツールには、クロス プラットフォーム アプリケーションを開発するための Visual Studio 統合と Xamarin Studio、スタンドアロン IDE がサポートされています。Linux システムの場合、Node.js をインストールして、以下の手順に従って **npm** を使用して xplat-cli をインストールするか、ソースから構築します。ソースは、[http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409](http://go.microsoft.com/fwlink/?linkid=253472&clcid=0x409) にあります。ソースからの構築の詳細については、アーカイブにある INSTALL ファイルを参照してください。

xplat-cli をインストールしたら、コマンド ライン インターフェイス (Bash、Terminal、Command プロンプト) から **azure** コマンドを使用して、xplat-cli コマンドにアクセスできます。

###インストーラーの使用

次のインストーラー パッケージを使用できます。

* [Windows インストーラー][windows-installer]

* [OS X インストーラー][mac-installer]

<div class="dev-callout">
<b>メモ</b>
<p>インストーラー パッケージには Node.js も含まれています。システムで他のバージョンの Node.js が利用できない場合は、このバージョンの Node.js が xplat-cli によって使用されます。これらのパッケージによってインストールされる Node.js のバージョンが、システムにインストール済みの Node.js のバージョンと競合しないようにしてください。</p>
</div>

###npm の使用

Node.js がインストールされているシステムの場合は、次のコマンドを使用して xplat-cli をインストールします。

	npm install azure-cli

<div class="dev-callout">
<b>メモ</b>
<p><b>npm</b> コマンドを実行するには、<b>sudo</b> の使用が必要になる場合があります。</p>
</div>

これにより、xplat-cli および必要な依存関係がインストールされます。インストールの最後に、次のような情報が表示されます。

	azure-cli@0.7.0 ..\node_modules\azure-cli
	|-- easy-table@0.0.1
	|-- eyes@0.1.8
	|-- xmlbuilder@0.4.2
	|-- colors@0.6.1
	|-- node-uuid@1.2.0
	|-- async@0.2.7
	|-- underscore@1.4.4
	|-- tunnel@0.0.2
	|-- omelette@0.1.0
	|-- github@0.1.6
	|-- commander@1.0.4 (keypress@0.1.0)
	|-- xml2js@0.1.14 (sax@0.5.4)
	|-- streamline@0.4.5
	|-- winston@0.6.2 (cycle@1.0.2, stack-trace@0.0.7, async@0.1.22, pkginfo@0.2.3, request@2.9.203)
	|-- kuduscript@0.1.2 (commander@1.1.1, streamline@0.4.11)
	|-- azure@0.7.13 (dateformat@1.0.2-1.2.3, envconf@0.0.4, mpns@2.0.1, mime@1.2.10, validator@1.4.0, xml2js@0.2.8, wns@0.5.3, request@2.25.0)

<div class="dev-callout">
<b>メモ</b>
<p>Node.js は <a href="http://nodejs.org/">http://nodejs.org/</a> からインストールできます。</p>
</div>

<h2><a id="Configure"></a>Windows Azure サブスクリプションに接続する方法</h2>

xplat-cli の一部のコマンドは Windows Azure サブスクリプションがなくても機能しますが、多くのコマンドにはサブスクリプションが必要です。Windows Azure サブスクリプションを使用するように plat-cli を構成するには、発行設定ファイルをダウンロードしてインストールする必要があります。このファイルには、Windows Azure サブスクリプションへの管理要求を認証するために使用される管理証明書とサブスクリプション ID が含まれています。

アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Windows Azure の無料評価版サイト][free-trial]を参照してください。

アカウントの発行設定をダウンロードするには、次のコマンドを使用します。

	azure account download

既定のブラウザーが開き、Windows Azure の管理ポータルにサインインするよう促されます。サインインすると、.publishsettings ファイルがダウンロードされます。ファイルを保存した場所をメモしておきます。

<div class="dev-callout">
<b>メモ</b>
<p>アカウントが複数の Windows Azure Active Directory (AD) テナントに関連付けられている場合は、発行の設定ファイルのダウンロード対象となる AD を選択するよう促されることがあります。</p>
<p>ダウンロード ページを使用するか、Windows Azure の管理ポータルにアクセスして選択を行った後、選択した AD は、ポータルおよびダウンロード ページによって使用される既定になります。既定を確定した後、"<b>click here to return to the selection page (選択ページに戻るにはここをクリックしてください)</b>" というテキストが、ダウンロード ページの上部に表示されます。選択されたページに戻るには、用意されているリンクを使用します。</p>
</div>

その後、次のコマンドを実行して ".publishsettings" ファイルをインポートします。ここで、"{path to .publishsettings file}" には、".publishsettings" ファイルのパスを指定してください。

	azure account import {path to .publishsettings file}

<div class="dev-callout">
<b>メモ</b>
<p>発行設定をインポートすると、Windows Azure サブスクリプションにアクセスするための情報が、<code>user</code> ディレクトリの <code>.azure</code> ディレクトリに格納されます。<code>user</code> ディレクトリはシステムによって保護されますが、追加の作業を行って <code>user</code> ディレクトリを暗号化することをお勧めします。そのためには、次の操作を行います。</p>

<ul>
<li>Windows の場合、ディレクトリ プロパティを変更するか、または BitLocker を使用します。</li>
<li>Mac の場合、ディレクトリに対して FileVault を有効にします。</li>
<li>Ubuntu の場合、Encrypted Home ディレクトリ機能を使用します。その他の Linux ディストリビューションにも同等の機能が用意されています。</li>
</ul>

</div>

発行設定をインポートしたら、".publishsettings" ファイルを削除します。このファイルはコマンド ライン ツールでは不要であり、第三者によってサブスクリプションヘのアクセスに使用されるセキュリティ上のリスクがあるためです。

###複数のサブスクリプション

複数の Windows Azure サブスクリプションがある場合、".publishsettings" ファイルには全サブスクリプションの情報が含まれます。"azure account import" コマンドを使用してファイルをインポートすると、操作の実行時に xplat-cli によって使用される既定のサブスクリプションとして 1 つのサブスクリプションが選択されます。"azure account list" コマンドを使用すると、すべてのサブスクリプションおよび既定のサブスクリプションを表示できます。このコマンドでは、次のような情報が返されます。

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

上のリストでは、**Current** 列に現在の既定のサブスクリプションとして Azure-sub-1 が表示されています。既定のサブスクリプションを変更するには、"azure account set" コマンドを使用して、既定にするサブスクリプションを指定します。たとえば、

	azure account set Azure-sub-2

この結果、既定のサブスクリプショは Azure-sub-2 に変更されます。

<div class="dev-callout">
<b>メモ</b>
<p>既定のサブスクリプションの変更はすぐに有効になり、グローバル設定です。新しい xplat-cli コマンドの実行には、同じコマンドライン インスタンスからでも別のインスタンスからでも、この新しい既定のサブスクリプションが使用されます。</p>
</div>

xplat-cli で、既定のサブスクリプションを変更せずに既定ではないサブスクリプションを使用する場合は、"--subscription" オプションを使用して、その操作に使用するサブスクリプションの名前を指定します。

<h2><a id="use"></a>Windows Azure クロス プラットフォーム コマンド ライン インターフェイスを使用する方法</h2>

xplat-cli には "azure" コマンドを使用してアクセスします。使用できるコマンドのリストを表示するには、"azure" コマンドをパラメーターなしで使用します。次のようなヘルプ情報が表示されます。

	info:             _    _____   _ ___ ___
	info:            /_\  |_  / | | | _ \ __|
	info:      _ ___/ _ \__/ /| |_| |   / _|___ _ _
	info:    (___  /_/ \_\/___|\___/|_|_\___| _____)
	info:       (_______ _ _)         _ ______ _)_ _
	info:              (______________ _ )   (___ _ _)
	info:
	info:    Windows Azure: Microsoft's Cloud Platform
	info:
	info:    Tool version 0.7.0
	help:
	help:    Display help for a given command
	help:      help [options] [command]
	help:
	help:    Opens the portal in a browser
	help:      portal [options]
	help:
	help:    Commands:
	help:      account        Commands to manage your account information and publish settings
	help:      config         Commands to manage your local settings
	help:      hdinsight      Commands to manage your HDInsight accounts
	help:      mobile         Commands to manage your Mobile Services
	help:      network        Commands to manage your Networks
	help:      sb             Commands to manage your Service Bus configuration
	help:      service        Commands to manage your Cloud Services
	help:      site           Commands to manage your Web Sites
	help:      sql            Commands to manage your SQL Server accounts
	help:      storage        Commands to manage your Storage objects
	help:      vm             Commands to manage your Virtual Machines
	help:
	help:    Options:
	help:      -h, --help     output usage information
	help:      -v, --version  output the application version

ここに表示されているトップ レベルのコマンドには、Windows Azure の特定の領域で機能するコマンドが含まれています。たとえば、"azure account" コマンドには、以前使用した "download" や "import" 設定など、Windows Azure サブスクリプションに関連するコマンドが含まれています。

ほとんどのコマンドは "azure <command> <operation> [parameters]" という形式で、サービスやアカウント構成などのオブジェクトに対する操作を行います。その他のコマンドにはサブコマンドがあり、"azure <command> <subcommand> <operation> [parameters]" という形式で指定します。次のコマンド例は、アカウント構成に対する操作です。

* インポート済みのサブスクリプションを表示する。

		azure account list

* 複数のサブスクリプションをインポート済みで、1 つのサブスクリプションを既定に設定する。

		azure account set <subscription>

"--help" または "-h" パラメーターを使用すると特定のコマンドに対するヘルプを表示できます。または、"azure help [command] [options]" の形式で指定しても、同じ情報が返されます。たとえば、次のコマンドでは、すべて同じ情報が返されます。

	azure account set --help

	azure account set -h

	azure help account set

コマンドに必要なパラメーターがわからない場合は "--help"、"-h"、または "azure help [command]" を使用してヘルプを参照してください。

###サービスの操作

xplat-cli を使用すると、Windows Azure サービスを簡単に管理できます。この例では、xplat-cli を使用して Windows Azure の Web サイトを管理する方法を説明します。

1. 新しい Windows Azure の Web サイトを作成するには、次のコマンドを使用します。**mywebsite** は、一意の名前に置き換えてください。

		azure site create mywebsite

	Web サイトが作成される場所を指定するように求められます。地理的に近いリージョンを選択します。次のコマンドを実行すると、Web サイトが http://mywebsite.azurewebsites.net で使用できるようになります (**mywebsite** は、作成時に指定した名前に置き換えます)。

	<div class="dev-callout">
	<b>メモ</b>
	<p>プロジェクト ソース管理に Git を使用する場合、<code>--git</code> パラメーターを指定して、この Web サイト用の Git リポジトリを Windows Azure に作成します。Git リポジトリがないディレクトリからこのコマンドを実行した場合、Git リポジトリの再初期化も行われます。また、<b>azure</b> という名前の Git リモートも作成されます。これは、<code>git push azure master</code> コマンドを使用して Windows Azure の Web サイトに展開をプッシュするために使用できます。</p>
	</div>

2. サブスクリプションのすべての Web サイトを表示するには、次のコマンドを使用します。

		azure site list

	このリストには、前の手順で作成したサイトが含まれます。

2. Web サイトを停止するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site stop mywebsite

	コマンドが完了したら、ブラウザーを更新するとサイトが停止したことを確認できます。

3. Web サイトを開始するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site start mywebsite

	コマンドが完了したら、ブラウザーを更新するとサイトが開始したことを確認できます。

4. Web サイトを削除するには次のコマンドを使用します。**mywebsite** は、サイト名に置き換えてください。

		azure site delete mywebsite

	コマンドが完了したら、"azure site list" コマンドを使用して、Web サイトが削除されていることを確認します。

<h2><a id="script"></a>Windows Azure クロス プラットフォーム コマンド ライン インターフェイスでスクリプトを使用する方法</h2>

xplat-cli を使用して手動でコマンドを発行できますが、お使いのシステムで使用できるコマンド ライン インタープリターやその他のコマンド ライン ユーティリティの機能を利用して、複雑な自動化ワークフローを作成することもできます。たとえば、次のコマンドはすべての Windows Azure の Web サイトを停止します。

	azure site list | grep 'Running' | awk '{system("azure site stop "$2)}'

この例では、"grep" コマンドに Web サイトのリストをパイプして、文字列 "Running" について各行を調べています。一致した行は "awk" コマンドにパイプされます。"azure site stop" が呼び出され、渡された 2 番目の列 (実行中のサイト名) が、停止するサイト名に使用されます。

これは、コマンドの連結方法を示した例ですが、コマンド ライン インタープリターのスクリプト機能を使用して、さらに複雑なスクリプトを作成することもできます。コマンドライン インタープリターの種類ごとに、スクリプト機能や構文が異なります。Linux および OS X を含め、UNIX ベースのシステム用には Bash が最も広く使用されているコマンド ライン インタープリターでしょう。

Bash でのスクリプトの作成方法については、「[Advanced Bash-Scripting Guide][advanced-bash]」を参照してください。

OS X または Linux ベース システムのスクリプトの作成の全般的な情報については、「[Shell script][script]」を参照してください。

バッチ ファイルを使用した Windows ベース システムのスクリプトについては、[「Command-line reference A-Z」][batch]を参照してください。

### 結果について

スクリプトの作成時、コマンド出力をキャプチャしたり、出力を別のコマンドに渡したり、出力に特別な操作 (特定の値の確認など) を実行したりすることがあります。xplat-cli は出力を STDOUT および STDERR に生成します。各行にはプレフィックスとして、情報ステータス メッセージの場合は文字列 "info:" が、サービスに関して返されるデータの場合は "data:" がそれぞれ付加されますが、"--verbose" または "-v" パラメーターを使用すると、詳細情報を取得できます。これらのパラメーターで返される追加の情報には、文字列 "verbose:" がプレフィックスとして付加されます。

たとえば、次の出力は "azure site list" コマンドで返された情報です。

	info:    Executing command site list
	+ Enumerating sites
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

"--verbose" または "-v" パラメーターを指定すると、次のような情報が返されます。

	info:    Executing command site list
	verbose: Subscription ####################################
	verbose: Enumerating sites
	verbose: [
	verbose:     {
	verbose:         ComputeMode: 'Shared',
	verbose:         HostNameSslStates: {
	verbose:             HostNameSslState: [
	verbose:                 {
	verbose:                     IpBasedSslResult: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     SslState: 'Disabled',
	verbose:                     ToUpdateIpBasedSsl: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     VirtualIP: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Thumbprint: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     ToUpdate: {
	verbose:                         $: { i:nil: 'true' }
	verbose:                     },
	verbose:                     Name: 'myawesomesite.azurewebsites.net'
	verbose:                 },
	...
	verbose:     }
	verbose: ]
	data:    Name           Status   Mode  Host names
	data:    -------------  -------  ----  -------------------------------
	data:    myawesomesite  Running  Free  myawesomesite.azurewebsites.net
	info:    site list command OK

"verbose:" の情報は、JSON 形式のデータです。jsawk や jq など、JSON をネイティブで認識するユーティリティを使用している場合は、"--json" パラメーターを使用して、[jsawk](https://github.com/micha/jsawk) または [jq](http://stedolan.github.io/jq/) のように、情報を JSON 形式で取得できます。たとえば、

	azure site list --json | jsawk -n 'out(this.Name)' | xargs -L 1 azure site delete -q 

このコマンドは Web サイトのリストを JSON として取得してから jsawk を使用してサイト名を取得し、最後に xargs を使用して、各サイトの削除コマンドを実行します。このサイト名はパラメーターとして渡されています。

<div class="dev-callout">
<b>メモ</b>
<p><code>--json</code> パラメーターは、ステータスの生成またはデータ情報をブロックします (プレフィックス <code>info:</code> および <code>data:</code> がある文字列)。たとえば、<code>--json</code> パラメーターを <code>azure site create</code> コマンドで使用した場合、返される出力はありません。このコマンドでは <code>info:</code> 以外のデータを返さないためです。</p>
</div>

###エラーの操作

xplat-cli では、エラー情報を STDERR にログ出力しますが、エラーに関する追加情報が、スクリプトを実行したディレクトリにある **azure.err** ファイルに記録されます。このファイルに情報が記録された場合、次のような文字列が STDOUT に書き込まれます。

	info:    Error information has been recorded to azure.err

### 終了ステータス

必須パラメーターが不足している場合、一部の xplat-cli コマンドは 0 以外の終了ステータスを返しません。代わりに、ユーザーの入力が要求されます。たとえば、Web サイトを新規作成する "azure site create" コマンドを使用したとき、名前または "--location" パラメーターがない場合は、これらの値を指定するように求められます。

終了ステータスに依存するスクリプトを作成する場合は、使用する xplat-cli コマンドがユーザー入力を求めないことを確認してください。

<h2><a id="additional-resources"></a>その他のリソース</h2>

* ソース コードのダウンロード、問題の報告、プロジェクトへの協力など、xplat-cli の詳細については、「[Windows Azure クロス プラットフォーム コマンド ライン インターフェイスの GitHub リポジトリ](https://github.com/WindowsAzure/azure-sdk-tools-xplat)」を参照してください。

* xplat-cli または Windows Azure の使用で問題が発生した場合は、[Windows Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home)にアクセスしてください。

* Windows Azure の詳細については、[http://www.windowsazure.com/](http://www.windowsazure.com) を参照してください。




[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464&clcid=0x409


[Windows Azure の Web サイト]: ../media/freetrial.png
[プレビュー機能を選択する]: ../media/antares-iaas-preview-02.png
[サブスクリプションを選択する]: ../media/antares-iaas-preview-03.png
[無料評価版]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A7171371E
[advanced-bash]: http://tldp.org/LDP/abs/html/
[script (スクリプト)]: http://en.wikipedia.org/wiki/Shell_script
[batch (バッチ)]: http://technet.microsoft.com/en-us/library/bb490890.aspx


