<properties linkid="develop-net-common-tasks-diagnostics-logging-and-instrumentation" urlDisplayName="診断ログの有効化" pageTitle="診断ログの有効化: Azure の Web サイト" metaKeywords="Azure 診断 Web サイト, Azure 管理ポータル診断, Azure 診断, Web サイト診断, Web サイト デバッグ" description="診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。" metaCanonical="" services="web-sites" documentationCenter=".NET" title="Azure の Web サイトの診断ログを有効にする" authors=""  solutions="" writer="larryfr" manager="" editor=""  />





#Azure の Web サイトの診断ログを有効にする

Azure では、ビルトインの診断機能により、Azure の Web サイトでホストされるアプリケーションのデバッグが支援されます。この記事では、診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。

> [WACOM.NOTE] この記事では、Azure の管理ポータル、Azure PowerShell、および Azure クロスプラットフォーム コマンド ライン インターフェイスで診断ログを使用する方法を示します。Visual Studio で診断ログを使用する方法の詳細については、「[Visual Studio での Azure の Web サイトのトラブルシューティング](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

##目次##

- [Web サイト診断とは](#whatisdiag)
- [方法: 診断を有効にする](#enablediag)
- [方法: ログをダウンロードする](#download)
- [方法: ログをストリーミングする](#streamlogs)
- [方法: 診断ログを読む](#understandlogs)
- [次のステップ](#nextsteps)

<a name="whatisdiag"></a><h2>Web サイト診断とは</h2>

Azure の Web サイトには、Web サーバーと Web アプリケーション両方からのログ情報の診断機能が備わっています。これらは論理的に**サイト診断**と**アプリケーション診断**に分けられます。

###サイト診断

サイト診断では、次の機能を有効または無効にできます。

- **詳細なエラー ログ**: 障害 (ステータス コード 400 以上) を示す HTTP ステータス コードの詳細なエラー情報をログに記録します。このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。
- **失敗した要求トレース**: 要求の処理に使用されたコンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報をログに記録します。このログが便利なのは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーが返される理由を特定したりする場合です。
- **Web サーバーのログ記録**: [W3C 拡張ログ ファイル フォーマット](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用して、Web サイト上のすべての HTTP トランザクションをログに記録します。このレポートが便利なのは、全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときです。

###アプリケーション診断

アプリケーション診断では、Web アプリケーションによって生成された情報を取り込むことができます。ASP.NET アプリケーションは、[System.Diagnostics.Trace](http://msdn.microsoft.com/ja-jp/library/36hhw2t6.aspx) クラスを使用して、情報をアプリケーション診断ログに記録できます。次に例を示します。

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

アプリケーション診断では、コードの特定の部分が使用されるときの情報を取り込むことにより、実行中のアプリケーションのトラブルシューティングを行うことができます。この診断が便利なのは、特定のパスがコードによって取られる理由を特定するときです。そのパスの多くは、エラーやその他の望ましくない動作につながるものです。

Visual Studio でアプリケーション診断を使用する方法の詳細については、「[Visual Studio での Azure の Web サイトのトラブルシューティング](http://www.windowsazure.com/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)」を参照してください。

> [WACOM.NOTE] web.config ファイルを変更する場合と異なり、アプリケーション診断を有効にしたり、診断ログ レベルを変更したりしても、アプリケーションが実行されているアプリケーション ドメインがリサイクルされることはありません。

Azure の Web サイトでは、Web サイトにアプリケーションを発行したときに展開情報もログに記録されます。これは自動的に行われ、展開ログの構成設定はありません。展開ログでは、展開が失敗した理由を特定できます。たとえば、カスタムの展開スクリプトを使用している場合は、展開ログを使用して、スクリプトでエラーが発生する理由を特定できることがあります。

<a name="enablediag"></a><h2>方法: 診断を有効にする</h2>

診断は、[Azure の管理ポータル](https://manage.microsoft.com)の Azure の Web サイトにある **[構成]** ページにアクセスすることで有効にできます。**[構成]** ページで、**アプリケーション診断**と**サイト診断**を使用してログを有効にします。

**アプリケーション診断**を有効にした場合、**ログ レベル**と、**ファイル システム**、**テーブル ストレージ**、または **BLOB ストレージ**へのログを有効にするかどうかも選択する必要があります。ログ書き込み先の 3 つの場所のいずれでも、ログ記録されたイベントについて同じ基本的な情報が得られますが、**テーブル ストレージ**と **BLOB ストレージ**には、インスタンス ID、スレッド ID、より詳細なタイムスタンプ (目盛り形式) など、追加の情報がログ記録されます。**ファイル システム**には、このような情報はログ記録されません。

**サイト診断**を有効にすると、**[Web サーバーのログ記録]** で **[ストレージ]** または **[ファイル システム]** を選択する必要があります。**[ストレージ]** を選択すると、ストレージ アカウントを選択でき、ログ書き込み先の BLOB コンテナーを指定できます。**サイト診断**用のその他のすべてのログはファイル システムにのみ書き込まれます。

> [WACOM.NOTE] **テーブル ストレージ**または **BLOB ストレージ**に格納されている情報には、これらのストレージ システムを直接操作できるストレージ クライアントまたはアプリケーションからアクセスできます。たとえば、Visual Studio 2013 のストレージ エクスプローラーを使用すると、テーブル ストレージまたは BLOB ストレージを操作できます。HDInsight を使用すると、BLOB ストレージに格納されているデータにアクセスできます。[Azure SDK](http://www.windowsazure.com/ja-jp/downloads/#) のいずれかを使用して、Azure のストレージにアクセスするアプリケーションを書くこともできます。

次に示しているのは、**アプリケーション診断**を有効にすると使用できる設定です。

* **ログ レベル** - 取得された情報を、**情報**、**警告**、**エラー**情報にフィルター処理できます。これを**詳細**に設定すると、アプリケーションにより生成されるすべての情報がログに記録されます。**ログ レベル**は、**ファイル システム**、**テーブル ストレージ**、および **BLOB ストレージ**に対して、それぞれ異なるレベルを設定できます。
* **ファイル システム** - アプリケーション診断情報が Web サイトのファイル システムに保存されます。これらのファイルは、FTP によってアクセスするか、Azure PowerShell または Azure コマンド ライン ツールを使用して Zip アーカイブとしてダウンロードできます。
* **テーブル ストレージ** - 指定された Azure のストレージ アカウントおよびテーブル名にアプリケーション診断情報が保存されます。
* **BLOB ストレージ** - 指定された Azure のストレージ アカウントおよび BLOB コンテナーにアプリケーション診断情報が保存されます。
* **保有期間** - 既定では、**BLOB ストレージ**からログが自動的に削除されることはありません。ログを自動的に削除するには、**[保有期間の設定]** を選択して、ログを保有する日数を入力します。

> [WACOM.NOTE] ファイル システム、テーブル ストレージ、BLOB ストレージへのログ記録は、任意に組み合わせて同時に有効にすることができます。また、それぞれ個別にログ レベルを設定できます。たとえば、BLOB ストレージへのエラーと警告の長期間のログ記録、ファイル システムへの詳細レベルのログ記録を同時に有効にすることができます。

> [WACOM.NOTE] 診断を有効にするには、Azure PowerShell から **Set-AzureWebsite** コマンドレットを使用する方法もあります。Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure PowerShell の使用方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

<a name="download"></a><h2>方法: ログをダウンロードする</h2>

Web サイト ファイル システムに保存された診断情報には、FTP を使用して直接アクセスできます。さらに、Azure PowerShell または Azure コマンド ライン ツールを使用して Zip アーカイブとしてダウンロードすることもできます。

ログが保存されるディレクトリ構造は次のとおりです。

* **アプリケーション ログ** - /LogFiles/Application/。このフォルダーには、アプリケーション ログによって生成された情報を含む 1 つ以上のテキスト ファイルが格納されます。

* **失敗した要求トレース** - /LogFiles/W3SVC#########/。このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。この XSL ファイルは、XML ファイルが Internet Explorer で表示されるときに、コンテンツの書式設定とフィルター処理を行う役割を果たすため、必ず XML ファイルと同じディレクトリにダウンロードしてください。

* **詳細なエラー ログ** - /LogFiles/DetailedErrors/。このフォルダーには、発生した HTTP エラーに関する詳細な情報を記録した 1 つ以上の .htm ファイルが格納されます。

* **Web サーバーのログ** - /LogFiles/http/RawLogs。このフォルダーには、[W3C 拡張ログ ファイル形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用して形式が設定された 1 つ以上のテキスト ファイルが格納されます。

* **展開ログ** - /LogFiles/Git。このフォルダーには、Git 展開のログだけでなく、Azure の Web サイトが使用する内部展開プロセスによって生成されたログも格納されます。

###FTP

FTP を使用して診断情報にアクセスするには、Azure の管理ポータルで Web サイトの**ダッシュボード**にアクセスします。**[概要]** セクションで、**[FTP 診断ログ]** リンクを使用し、FTP を使用してログ ファイルにアクセスします。**[展開/FTP ユーザー]** エントリには、FTP サイトへのアクセスに使用するユーザー名が一覧表示されます。

> [WACOM.NOTE] **[展開/FTP ユーザー]** エントリが設定されていない場合や、このユーザーのパスワードを忘れた場合は、**ダッシュボード**の **[概要]** セクションで **[展開資格情報のリセット]** リンクを使用することで、新しいユーザーとパスワードを作成できます。

###Azure PowerShell を使用してダウンロードする

ログ ファイルをダウンロードするには、Azure PowerShell の新しいインスタンスを開始し、次のコマンドを使用します。

	Save-AzureWebSiteLog -Name websitename

これにより、**-Name** パラメーターにより指定された Web サイトのログが、現在のディレクトリにある **logs.zip** というファイルに保存されます。

> [WACOM.NOTE] Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合、「[Azure PowerShell の使用方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

###Azure コマンド ライン ツールを使用してダウンロードする

Azure コマンド ライン ツールを使用してログ ファイルをダウンロードするには、新しいコマンド プロンプト、PowerShell、Bash、またはターミナル セッションを開き、次のコマンドを入力します。

	azure site log download websitename

これにより、'websitename' という名前の Web サイトのログが、現在のディレクトリにある **diagnostics.zip** というファイルに保存されます。

> [WACOM.NOTE] Azure コマンド ライン ツールをインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合、「[Mac および Linux 用 Azure コマンド ライン ツールの使用方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/command-line-tools/)」を参照してください。

<a name="streamlogs"></a><h2>方法: ログをストリーミングする</h2>

アプリケーションの開発中に、ログ情報をほぼリアルタイムで参照すると役立つことがよくあります。これは、Azure PowerShell または Azure コマンド ライン ツールを使用して開発環境にログ情報をストリーミングすることで実現できます。

> [WACOM.NOTE] 一部の種類のログ バッファーはログ ファイルに書き込まれるため、ストリーミング中に無効な順序エラーが発生する可能性があります。たとえば、ユーザーがページにアクセスしたときに発生するアプリケーション ログ エントリは、ページ要求の該当する HTTP ログ エントリより前のストリームに表示されることがあります。

> [WACOM.NOTE]  ログのストリーミングでは、**D:\\home\\LogFiles\\** に格納されているテキスト ファイルに書き込まれた情報もすべてストリーミングされます。

###Azure PowerShell を使用してストリーミングする

ログ情報をストリーミングするには、Azure PowerShell を新たに起動して次のコマンドを使用します。

	Get-AzureWebSiteLog -Name websitename -Tail

**-Name** パラメーターにより指定された Web サイトに接続され、ログ イベントが Web サイトで発生したら、PowerShell ウィンドウへの情報のストリーミングが開始されます。/LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、**-Message** パラメーターを使用します。次に例を示します。

	Get-AzureWebSiteLog -Name websitename -Tail -Message Error

特定のログの種類 (HTTP など) をフィルター処理するには、**-Path** パラメーターを使用します。次に例を示します。

	Get-AzureWebSiteLog -Name websitename -Tail -Path http

使用可能なパスの一覧を表示するには、-ListPath パラメーターを使用します。

> [WACOM.NOTE] Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合、「[Azure PowerShell の使用方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

###Azure コマンド ライン ツールを使用してストリーミングする

ログ情報をストリーミングするには、新しいコマンド プロンプト、PowerShell、Bash、またはターミナル セッションを開き、次のコマンドを入力します。

	azure site log tail websitename

"'websitename'" という名前の Web サイトに接続され、ログ イベントが Web サイトで発生したら、ウィンドウへの情報のストリーミングが開始されます。/LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、**--Filter** パラメーターを使用します。次に例を示します。

	azure site log tail websitename --filter Error

特定のログの種類 (HTTP など) をフィルター処理するには、**--Path** パラメーターを使用します。次に例を示します。

	azure site log tail websitename --path http

> [WACOM.NOTE] Azure コマンド ライン ツールをインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合、「[Mac および Linux 用 Azure コマンド ライン ツールの使用方法](http://www.windowsazure.com/ja-jp/develop/nodejs/how-to-guides/command-line-tools/)」を参照してください。

<a name="understandlogs"></a><h2>方法: 診断ログを読む</h2>

###アプリケーション診断ログ

アプリケーション診断では、ファイル システム、テーブル ストレージ、BLOB ストレージのうち、どれにログを保存するかに応じて、.NET アプリケーション向けの一定の形式で情報が保存されます。格納される一連の基本的なデータは、3 種類のすべてのストレージ間で同じで、イベントが発生した日時、イベントを生成したプロセスの ID、イベントの種類 (情報、警告、エラー)、イベントのメッセージです。

__ファイル システム__

ファイル システムにログが記録される行またはストリーミングによって受信する行は、それぞれ以下の形式になります。

	{日付}  PID[{プロセス ID}] {イベントの種類/レベル} {メッセージ}

たとえば、エラー イベントは次のようになります。

	2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

ファイル システムにログ記録する場合は、使用できる 3 つのログ記録方法のうちで最も基本的な情報が提供され、時間、プロセス ID、イベント レベル、メッセージのみを確認できます。

__テーブル ストレージ__

テーブル ストレージにログ記録する場合は、追加のプロパティを使用して、テーブルに格納されているデータだけでなく、イベントに関するより詳細な情報も簡単に検索できます。テーブルに格納される各エンティティ (行) に次のプロパティ (列) が使用されます。

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">プロパティ名</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">値/形式</th>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">PartitionKey</td>
<td style="border:1px solid black;vertical-align:top">yyyyMMddHH の形式によるイベントの日時</td>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">RowKey</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">このエンティティを一意に識別する GUID 値</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Timestamp</td>
<td style="border:1px solid black;vertical-align:top">イベントが発生した日時</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した目盛り形式 (高精度) の日時</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Web サイト名</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベント レベル (例: エラー、警告、情報)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top"></td>このイベントの ID<br>何も指定しない場合は既定で 0
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した Web サイトのインスタンス</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">プロセス ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントを生成したスレッドの ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">イベントの詳細メッセージ</td>
</tr>
</table>

__BLOB ストレージ__

BLOB ストレージにログを記録するときには、値をコンマで区切った (CSV) 形式で格納されます。テーブル ストレージと同様、追加のフィールドがログに記録されて、イベントについてより詳細な情報が提供されます。CSV 内の各行に次のプロパティが使用されます。

<table style="width:100%;border-collapse:collapse">
<thead>
<tr>
<th style="width:45%;border:1px solid black;background-color:#0099dd">プロパティ名</th>
<th style="border:1px solid black;vertical-align:top;background-color:#0099dd">値/形式</th>
</tr>
</thead>
<tr>
<td style="border:1px solid black;vertical-align:top">Date</td>
<td style="border:1px solid black;vertical-align:top">イベントが発生した日時</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Level</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベント レベル (例: エラー、警告、情報)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">ApplicationName</td>
<td style="border:1px solid black;vertical-align:top">Web サイト名</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した Web サイトのインスタンス</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した目盛り形式 (高精度) の日時</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top"></td>このイベントの ID<br>何も指定しない場合は既定で 0
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Pid</td>
<td style="border:1px solid black;vertical-align:top">プロセス ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">Tid</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントを生成したスレッドの ID</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">Message</td>
<td style="border:1px solid black;vertical-align:top">イベントの詳細メッセージ</td>
</tr>
</table>

BLOB に格納されるデータは次のようになります。

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebsite,6ee38a,635266966128818593,0,3096,9,An error occurred

> [WACOM.NOTE] この例に示しているように、ログの最初の行は列ヘッダーになります。

###失敗した要求トレース

失敗した要求トレースは __fr######.xml__ という名前の XML ファイルに保存されます。ログに記録された情報を見やすくするには、__freb.xsl__ という名前の XSL スタイルシートを XML ファイルと同じディレクトリに配置します。Internet Explorer でいずれかの XML ファイルを開くと、トレース情報が XSL スタイルシートを使用して書式設定されて表示されます。たとえば、次のように表示されます。

![失敗した要求をブラウザーで表示したところ](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

###詳細なエラー ログ

詳細なエラー ログは、発生した HTTP エラーに関する詳細な情報を提供する HTML ドキュメントです。単なる HTML ドキュメントであるため、Web ブラウザーを使用して表示できます。

###Web サーバーのログ

Web サーバー ログは [W3C 拡張ログ形式](http://msdn.microsoft.com/ja-jp/library/windows/desktop/aa814385(v=vs.85) (.aspx) で書式設定されます。この情報は、テキスト エディターを使用して表示したり、[Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619) などのユーティリティで解析したりできます。

> [WACOM.NOTE] Azure の Web サイトによって生成されるログでは、__s-computername__、__s-ip__、__cs-version__ のフィールドはサポートされていません。

<a name="nextsteps"></a><h2>次のステップ</h2>

- [Web サイトの監視方法](/ja-jp/manage/services/web-sites/how-to-monitor-websites/)
- [チュートリアル: Web サイトのトラブルシューティング](/ja-jp/develop/net/best-practices/troubleshooting-web-sites/)
- [Visual Studio での Azure の Web サイトのトラブルシューティング](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [HDInsight での Web サイト ログの分析](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)


