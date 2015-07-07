<properties
	pageTitle="Azure App Service の Web アプリケーションの診断ログの有効化"
	description="診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログ記録された情報にアクセスする方法を説明します。"
	services="app-service\web"
	documentationCenter=".net"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2015"
	ms.author="cephalin"/>

# Azure App Service の Web アプリケーションの診断ログの有効化

## 概要

Azure では、組み込みの診断機能により、[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) でホストされる Web アプリのデバッグを容易に行うことができます。この記事では、診断ログを有効にしてインストルメンテーションをアプリケーションに追加する方法と、Azure によってログに記録された情報にアクセスする方法について説明します。

> [AZURE.NOTE]この記事では、[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)、Azure PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) で診断ログを使用する方法を示します。Visual Studio で診断ログを使用する方法の詳細については、「[Visual Studio での Azure のトラブルシューティング](../troubleshoot-web-sites-in-visual-studio.md)」を参照してください。

## <a name="whatisdiag"></a>Web サーバーの診断とアプリケーション診断

[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) は、Web サーバーと Web アプリケーション両方からのログ情報の診断機能を備えています。これらは論理的に **Web サーバー診断**と**アプリケーション診断**に分けられます。

### Web サーバー診断

次の種類のログを有効または無効にできます。

- **詳細なエラー ログ** - 障害 (状態コード 400 以上) を示す HTTP 状態コードの詳細なエラー情報。このログには、サーバーがエラー コードを返した理由を特定するために役立つ情報が記録されている場合があります。
- **失敗した要求トレース** - 要求の処理に使用された IIS コンポーネントのトレースや各コンポーネントにかかった時間など、失敗した要求の詳細情報。このログが便利なのは、サイトのパフォーマンスを向上させたり、特定の HTTP エラーが返される理由を特定したりする場合です。
- **Web サーバーのログ記録** - [W3C 拡張ログ ファイル形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用した、HTTP トランザクションに関する情報。このレポートが便利なのは、全体的なサイト メトリック、たとえば、サイトで処理された要求の数や、特定の IP アドレスからの要求の数を特定するときです。

### アプリケーション診断

アプリケーション診断では、Web アプリケーションによって生成された情報を取り込むことができます。ASP.NET アプリケーションは、[System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) クラスを使用して、情報をアプリケーション診断ログに記録できます。次に例を示します。

	System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

アプリケーション診断では、コードの特定の部分が使用されるときの情報を取り込むことにより、実行中のアプリケーションのトラブルシューティングを行うことができます。この診断が便利なのは、特定のパスがコードによって取られる理由を特定するときです。そのパスの多くは、エラーやその他の望ましくない動作につながるものです。

Visual Studio でのアプリケーション診断の操作については、「[Visual Studio での Azure Web アプリのトラブルシューティング](../troubleshoot-web-sites-in-visual-studio.md)」を参照してください。

> [AZURE.NOTE]web.config ファイルの変更とは異なり、アプリケーション診断の有効化や診断ログ レベルの変更によって、アプリケーションが実行されているアプリケーション ドメインがリサイクルされることはありません。

Azure の Web アプリケーションでは、Web アプリケーションにコンテンツを発行したときに展開情報もログに記録されます。これは自動的に行われ、展開ログの構成設定はありません。展開ログでは、展開が失敗した理由を特定できます。たとえば、カスタムの展開スクリプトを使用している場合は、展開ログを使用して、スクリプトでエラーが発生する理由を特定できることがあります。

## <a name="enablediag"></a>診断を有効にする方法

[Azure 管理ポータル](https://portal.azure.com)で診断を有効にするには、Web アプリのブレードに移動し、**[すべての設定] > [診断ログ]** をクリックします。

<!-- todo:cleanup dogfood addresses in screenshot -->
![ログ パーツ](./media/web-sites-enable-diagnostic-log/logspart.png)

**アプリケーション ログ**を有効にした場合、**ログ レベル**と、**ファイル システム**、**テーブル ストレージ**、または **BLOB ストレージ**へのログを有効にするかどうかも選択する必要があります。ログ書き込み先の 3 つの場所のいずれでも、ログ記録されたイベントについて同じ基本的な情報が得られますが、**テーブル ストレージ**と **BLOB ストレージ**には、インスタンス ID、スレッド ID、より詳細なタイムスタンプ (目盛り形式) など、追加の情報がログ記録されます。**ファイル システム**には、このような情報はログ記録されません。

**サイト診断**を有効にすると、**[Web サーバーのログ記録]** で **[ストレージ]** または **[ファイル システム]** を選択する必要があります。**[ストレージ]** を選択すると、ストレージ アカウントを選択でき、ログ書き込み先の BLOB コンテナーを指定できます。**サイト診断**用のその他のすべてのログはファイル システムにのみ書き込まれます。

> [AZURE.NOTE]**テーブル ストレージ**または **BLOB ストレージ**に格納されている情報には、これらのストレージ システムを直接操作できるストレージ クライアントまたはアプリケーションからアクセスできます。たとえば、Visual Studio 2013 のストレージ エクスプローラーを使用すると、テーブル ストレージまたは BLOB ストレージを操作できます。HDInsight を使用すると、BLOB ストレージに格納されているデータにアクセスできます。[Azure SDK](/downloads/#) のいずれかを使用して、Azure Storage にアクセスするアプリケーションを記述することもできます。

次に示しているのは、**アプリケーション診断**を有効にすると使用できる設定です。

* **ログ レベル** - 取得された情報を、**情報**、**警告**、**エラー**情報にフィルター処理できます。これを**詳細**に設定すると、アプリケーションにより生成されるすべての情報がログに記録されます。**ログ レベル**は、**ファイル システム**、**テーブル ストレージ**、および **BLOB ストレージ**に対して、それぞれ異なるレベルを設定できます。
* **ファイル システム** - アプリケーション診断情報が Web アプリのファイル システムに保存されます。これらのファイルは、FTP によってアクセスするか、Azure PowerShell または Azure コマンド ライン ツールを使用して Zip アーカイブとしてダウンロードできます。
* **テーブル ストレージ** - 指定された Azure Storage アカウントおよびテーブル名にアプリケーション診断情報が保存されます。
* **BLOB ストレージ** - 指定された Azure Storage アカウントおよび BLOB コンテナーにアプリケーション診断情報が保存されます。
* **保有期間** - 既定では、**BLOB ストレージ**からログが自動的に削除されることはありません。ログを自動的に削除するには、**[保有期間の設定]** を選択して、ログを保有する日数を入力します。

> [AZURE.NOTE]ファイル システム、テーブル ストレージ、BLOB ストレージへのログ記録は、任意に組み合わせて同時に有効にすることができます。また、それぞれ個別にログ レベルを設定できます。たとえば、BLOB ストレージへのエラーと警告の長期間のログ記録、ファイル システムへの詳細レベルのログ記録を同時に有効にすることができます。

> [AZURE.NOTE]診断を有効にするには、Azure PowerShell から **Set-AzureWebsite** コマンドレットを使用する方法もあります。Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

##<a name="download"></a> ログをダウンロードする方法

Web アプリケーション ファイル システムに保存された診断情報には、FTP を使用して直接アクセスできます。さらに、Azure PowerShell または Azure コマンド ライン インターフェイスを使用して Zip アーカイブとしてダウンロードすることもできます。

ログが保存されるディレクトリ構造は次のとおりです。

* **アプリケーション ログ**: /LogFiles/Application/。このフォルダーには、アプリケーション ログによって生成された情報を含む 1 つ以上のテキスト ファイルが格納されます。

* **失敗した要求トレース**: /LogFiles/W3SVC#########/。このフォルダーには、1 つの XSL ファイルと 1 つ以上の XML ファイルが格納されます。この XSL ファイルは、XML ファイルが Internet Explorer で表示されるときに、コンテンツの書式設定とフィルター処理を行う役割を果たすため、必ず XML ファイルと同じディレクトリにダウンロードしてください。

* **詳細なエラー ログ**: /LogFiles/DetailedErrors/。このフォルダーには、発生した HTTP エラーに関する詳細な情報を記録した 1 つ以上の .htm ファイルが格納されます。

* **Web サーバー ログ**: /LogFiles/http/RawLogs。このフォルダーには、[W3C 拡張ログ ファイル形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)を使用して形式が設定された 1 つ以上のテキスト ファイルが格納されます。

* **デプロイ ログ**: /LogFiles/Git。このフォルダーには、Git 展開のログだけでなく、Azure の Web アプリケーションが使用する内部展開プロセスによって生成されたログも格納されます。

### FTP

FTP を使用して診断情報にアクセスするには、Azure 管理ポータルで Web アプリの**ダッシュボード**にアクセスします。**[概要]** セクションで、**[FTP 診断ログ]** リンクを使用し、FTP を使用してログ ファイルにアクセスします。**[展開/FTP ユーザー]** エントリには、FTP サイトへのアクセスに使用するユーザー名が一覧表示されます。

> [AZURE.NOTE]**[デプロイ/FTP ユーザー]** エントリが設定されていない場合や、このユーザーのパスワードを忘れた場合は、**ダッシュボード**の **[概要]** セクションで **[デプロイ資格情報のリセット]** リンクを使用することで、新しいユーザーとパスワードを作成できます。

### Azure PowerShell を使用してダウンロードする

ログ ファイルをダウンロードするには、Azure PowerShell の新しいインスタンスを開始し、次のコマンドを使用します。

	Save-AzureWebSiteLog -Name webappname

これにより、**-Name** パラメーターにより指定された Web アプリのログが、現在のディレクトリにある **logs.zip** というファイルに保存されます。

> [AZURE.NOTE]Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

### Azure コマンド ライン インターフェイスを使用してダウンロードする

Azure コマンド ライン インターフェイスを使用してログ ファイルをダウンロードするには、新しいコマンド プロンプト、PowerShell、Bash、ターミナル セッションを開き、次のコマンドを入力します。

	azure site log download webappname

これにより、"webappname" という名前の Web アプリのログが、現在のディレクトリにある **diagnostics.zip** というファイルに保存されます。

> [AZURE.NOTE]Azure コマンド ライン インターフェイス (Azure CLI) をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure CLI の使用方法](../xplat-cli.md)」に関するページをご覧ください。

## Application Insights でログを表示する方法

Visual Studio の Application Insights には、ログをフィルターおよび検索したり、要求やその他のイベントにログを関連付けたりするためのツールが用意されています。

1. Application Insights SDK を Visual Studio のプロジェクトに追加します。
 * ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。Application Insights リソースの作成などの手順が示されます。[詳細情報](../application-insights/app-insights-start-monitoring-app-health-usage.md)
2. トレース リスナーのパッケージをプロジェクトに追加します。
 * プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。`Microsoft.ApplicationInsights.TraceListener` を選択します。[詳細情報](../application-insights/app-insights-asp-net-trace-logs.md)
3. プロジェクトをアップロードして実行し、ログ データを生成します。
4. [Azure プレビュー ポータル](http://portal.azure.com/)で、新しい Application Insights リソースを参照し、**[検索]** を開きます。ログ データが、要求、使用状況、およびその他の製品利用統計情報と共に表示されます。一部の製品利用統計情報については、表示されるまで数分かかる場合があります。[更新] をクリックします。[詳細情報](../application-insights/app-insights-diagnostic-search.md)

[Application Insights でのパフォーマンス追跡についての詳細情報](../insights-perf-analytics.md)

##<a name="streamlogs"></a> ログをストリーミングする方法

アプリケーションの開発中に、ログ情報をほぼリアルタイムで参照すると役立つことがよくあります。これは、Azure PowerShell または Azure コマンド ライン インターフェイスを使用して開発環境にログ情報をストリーミングすることで実現できます。

> [AZURE.NOTE]一部の種類のログ バッファーはログ ファイルに書き込まれるため、ストリーミング中に無効な順序エラーが発生する可能性があります。たとえば、ユーザーがページにアクセスしたときに発生するアプリケーション ログ エントリは、ページ要求の該当する HTTP ログ エントリより前のストリームに表示されることがあります。

> [AZURE.NOTE]ログのストリーミングでは、**D:\home\LogFiles** フォルダーに格納されているテキスト ファイルに書き込まれた情報もストリーミングされます。

### Azure PowerShell を使用してストリーミングする

ログ情報をストリーミングするには、Azure PowerShell を新たに起動して次のコマンドを使用します。

	Get-AzureWebSiteLog -Name webappname -Tail

**-Name** パラメーターにより指定された Web アプリに接続され、ログ イベントが Web アプリで発生したら、PowerShell ウィンドウへの情報のストリーミングが開始されます。/LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、**-Message** パラメーターを使用します。次に例を示します。

	Get-AzureWebSiteLog -Name webappname -Tail -Message Error

特定のログの種類 (HTTP など) をフィルター処理するには、**-Path** パラメーターを使用します。次に例を示します。

	Get-AzureWebSiteLog -Name webappname -Tail -Path http

使用可能なパスの一覧を表示するには、-ListPath パラメーターを使用します。

> [AZURE.NOTE]Azure PowerShell をインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure PowerShell の使用方法](/develop/nodejs/how-to-guides/powershell-cmdlets/)」を参照してください。

### Azure コマンド ライン ツールを使用してストリーミングする

ログ情報をストリーミングするには、新しいコマンド プロンプト、PowerShell、Bash、またはターミナル セッションを開き、次のコマンドを入力します。

	azure site log tail webappname

"webappname" という名前の Web アプリに接続され、ログ イベントが Web アプリで発生したら、ウィンドウへの情報のストリーミングが開始されます。/LogFiles ディレクトリ (d:/home/logfiles) に格納されており、末尾が .txt、.log、.htm のいずれかになっているファイルに書き込まれた情報は、ローカル コンソールにストリーミングされます。

特定のイベント (エラーなど) をフィルター処理するには、**-Filter** パラメーターを使用します。次に例を示します。

	azure site log tail webappname --filter Error

特定のログの種類 (HTTP など) をフィルター処理するには、**--Path** パラメーターを使用します。次に例を示します。

	azure site log tail webappname --path http

> [AZURE.NOTE]Azure コマンド ライン インターフェイスをインストールしていない場合や、Azure サブスクリプションを使用するように構成していない場合は、「[Azure コマンド ライン インターフェイスの使用方法](../xplat-cli.md)」に関するページをご覧ください。

##<a name="understandlogs"></a> 診断ログを読む方法

### アプリケーション診断ログ

アプリケーション診断では、ファイル システム、テーブル ストレージ、BLOB ストレージのうち、どれにログを保存するかに応じて、.NET アプリケーション向けの一定の形式で情報が保存されます。格納される一連の基本的なデータは、3 種類のすべてのストレージ間で同じで、イベントが発生した日時、イベントを生成したプロセスの ID、イベントの種類 (情報、警告、エラー)、イベントのメッセージです。

__ファイル システム__

ファイル システムにログが記録される行またはストリーミングによって受信する行は、それぞれ以下の形式になります。

	{Date}  PID[{process id}] {event type/level} {message}

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
<td style="border:1px solid black;vertical-align:top">Web アプリケーション名</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">レベル</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベント レベル (例: エラー、警告、情報)</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">このイベントのイベント ID<br>指定されていない場合、既定値は 0</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した Web アプリケーションのインスタンス</td>
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
<td style="border:1px solid black;vertical-align:top">メッセージ</td>
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
<td style="border:1px solid black;vertical-align:top">Web アプリケーション名</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">InstanceId</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した Web アプリケーションのインスタンス</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">EventTickCount</td>
<td style="border:1px solid black;vertical-align:top;background-color:#8ddaf6">イベントが発生した目盛り形式 (高精度) の日時</td>
</tr>
<tr>
<td style="border:1px solid black;vertical-align:top">EventId</td>
<td style="border:1px solid black;vertical-align:top">このイベントのイベント ID<br>指定されていない場合、既定値は 0</td>
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
<td style="border:1px solid black;vertical-align:top">メッセージ</td>
<td style="border:1px solid black;vertical-align:top">イベントの詳細メッセージ</td>
</tr>
</table>

BLOB に格納されるデータは次のようになります。

	date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
	2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [AZURE.NOTE]この例に示しているように、ログの最初の行は列ヘッダーになります。

### 失敗した要求トレース

失敗した要求トレースは __fr######.xml__ という名前の XML ファイルに保存されます。ログに記録された情報を見やすくするには、__freb.xsl__ という名前の XSL スタイルシートを XML ファイルと同じディレクトリに配置します。Internet Explorer でいずれかの XML ファイルを開くと、トレース情報が XSL スタイルシートを使用して書式設定されて表示されます。たとえば、次のように表示されます。

![失敗した要求をブラウザーで表示したところ](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### 詳細なエラー ログ

詳細なエラー ログは、発生した HTTP エラーに関する詳細な情報を提供する HTML ドキュメントです。単なる HTML ドキュメントであるため、Web ブラウザーを使用して表示できます。

### Web サーバーのログ

Web サーバー ログは [W3C 拡張ログ形式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx) (.aspx) で書式設定されます。この情報は、テキスト エディターを使用して表示したり、[Log Parser](http://go.microsoft.com/fwlink/?LinkId=246619) などのユーティリティで解析したりできます。

> [AZURE.NOTE]Azure Web アプリによって生成されるログでは、__s-computername__、__s-ip__、または __cs-version__ のフィールドはサポートされていません。

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##<a name="nextsteps"></a> 次のステップ

- [Web Apps を監視する方法](/ja-jp/manage/services/web-sites/how-to-monitor-websites/)
- [チュートリアル - Web Apps のトラブルシューティング](/ja-jp/develop/net/best-practices/troubleshooting-web-sites/)
- [Visual Studio での Azure の Web Apps のトラブルシューティング](/ja-jp/develop/net/tutorials/troubleshoot-web-sites-in-visual-studio/)
- [HDInsight での Web アプリ ログの分析](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[Azure ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。
 

<!---HONumber=62-->