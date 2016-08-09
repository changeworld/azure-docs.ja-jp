<properties
	pageTitle="開発者分析用 Visual Studio 拡張機能のリリース ノート"
	description="開発者分析用 Visual Studio ツールの更新内容。"
	services="application-insights"
    documentationCenter=""
	authors="acearun"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2016"
	ms.author="acearun"/>

# Developer Analytics Tools のリリース ノート
新機能: Visual Studio での Application Insights と HockeyApp の分析
## Version 7.0
### Visual Studio Application Insights の傾向
Visual Studio Application Insights は、時間経過に伴うアプリの動作を分析するための Visual Studio の新しいツールです。このツールを起動するには、**[Application Insights]** ツール バー ボタンまたは Application Insights の [検索] ウィンドウから **[テレメトリの傾向を調べる]** を選択します。または、**[表示]** メニューの **[その他のウィンドウ]** をクリックし、**[Application Insights の傾向]** をクリックします。5 つの一般的なクエリから 1 つ選択して開始します。テレメトリの種類、時間範囲、およびその他のプロパティに基づき、さまざまなデータセットを分析できます。データ内の異常を見つけるには、**[ビューの種類]** ボックスの一覧からいずれかの異常オプションを選択します。ウィンドウの下部にあるフィルター オプションを使用すると、テレメトリの特定の部分に対象を絞り込みやすくなります。

![Application Insights Trends](./media/app-insights-release-notes-vsix/Trends.PNG)

### CodeLens における例外
CodeLens で例外のテレメトリを表示できるようになりました。プロジェクトが Application Insights サービスに接続されている場合、開発中の各メソッドで過去 24 時間内に発生した例外の数が表示されます。CodeLens から [検索] または [傾向] に移動すれば、さらに詳しく例外を調べることができます。

![CodeLens における例外](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### ASP.NET Core のサポート
Application Insights により、Visual Studio で ASP.NET Core RC2 プロジェクトがサポートされるようになりました。以下のスクリーンショットに示したように、新しい ASP.NET Core RC2 プロジェクトに Application Insights を追加するには、**[新しいプロジェクト]** ダイアログを使用します。または、既存のプロジェクトに追加することもできます。その場合は、ソリューション エクスプローラーで既存のプロジェクトを右クリックし、**[Application Insights Telemetry の追加]** をクリックします。

![ASP.NET Core support](./media/app-insights-release-notes-vsix/NetCoreSupport.PNG)

ASP.NET 5 RC1 と ASP.NET Core RC2 プロジェクトには、診断ツール ウィンドウでの新しいサポートもあります。お使いの PC でローカルにデバッグ中に、ASP.NET アプリケーションから要求や例外などの Application Insights のイベントが表示されます。個々のイベントから **[検索]** をクリックすると、ドリルダウンして、さらに詳しい情報を得ることができます。

![診断ツールのサポート](./media/app-insights-release-notes-vsix/DiagnosticTools.PNG)

### ユニバーサル Windows アプリ用 HockeyApp
HockeyApp には、ベータ版の配布やユーザー フィードバックに加え、ユニバーサル Windows アプリのクラッシュ レポートにシンボルを対応付ける機能があります。この HockeyApp SDK が従来よりも簡単に追加できるようになりました。対象のユニバーサル Windows プロジェクトを右クリックし、**[Hockey App - Enable Crash Analytics (HockeyApp - クラッシュ分析を有効にする)]** の順にクリックするだけです。これだけで SDK がインストールされ、クラッシュ収集がセットアップされて、HockeyApp のリソースがクラウドにプロビジョニングされます。アプリを HockeyApp サービスにアップロードする必要はありません。

その他の新機能:

* Application Insights の検索機能が高速化され、より直感的に利用できるようになりました。時間範囲フィルターと詳細フィルターを選択すると、それらが自動的に適用されます。
* また、要求のテレメトリから直接コードに移動するオプションが Application Insights の検索に追加されました。
* HockeyApp サインイン エクスペリエンスが向上しました。
* 診断ツールで、例外に関する運用テレメトリ情報が表示されます。

## バージョン 5.2
Visual Studio に HockeyApp シナリオを導入することになりました。まず、ユニバーサル Windows アプリと Windows フォーム アプリのベータ版を Visual Studio 内から配布できるようになります。

ベータ版配布を使用すると、初期バージョンのアプリを HockeyApp にアップロードし、一部の顧客やテスターに配布することができます。ベータ版配布に HockeyApp のクラッシュ コレクションとユーザー フィードバック機能を組み合わせることで、アプリに関する重要な情報を本格的なリリースの前に収集することができます。その情報を基にアプリの問題を解決しておけば、後で低い評価が下されたり否定的なフィードバックが寄せられたりするなどの問題を回避するか、最小限に抑えることができます。

Visual Studio からベータ版配布用にビルドをアップロードする方法は簡単です。ここではその方法について説明します。
### ユニバーサル Windows アプリ
ユニバーサル Windows アプリ プロジェクト ノードのコンテキスト メニューには、ビルドを HockeyApp にアップロードするオプションが追加されました。

![Project context menu for Universal Windows apps](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

項目を選択すると、HockeyApp アップロード ダイアログが表示されます。ビルドをアップロードするには、HockeyApp アカウントが必要です。まだ使ったことがない方もご安心ください。アカウントは簡単なプロセスで作成できます。

接続すると、ダイアログにアップロード フォームが表示されます。

![Upload dialog for Universal Windows apps](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

アップロードするコンテンツ (.appxbundle ファイルまたは .appx ファイル) を選択し、ウィザードでリリース オプションを選択します。必要に応じて、次のページでリリース ノートを追加できます。**[Finish (終了)]** を選択してアップロードを開始します。

アップロードが完了すると、HockeyApp の通知画面に確認メッセージと HockeyApp ポータルのアプリへのリンクが表示されます。

![Upload complete notification](./media/app-insights-release-notes-vsix/UploadComplete.png)

これで終了です。 わずか数回のクリックで、ベータ版配布用ビルドをアップロードしました。

HockeyApp ポータルから、さまざまな方法でアプリケーションを管理することができます。たとえばユーザーを招待したり、クラッシュ レポートやフィードバックを閲覧したり、詳細情報を変更したりすることもできます。

![HockeyApp ポータル](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

アプリの管理について詳しくは、[HockeyApp のサポート技術情報](http://support.hockeyapp.net/kb/app-management-2)をご覧ください。

### Windows フォーム アプリ
Windows フォーム プロジェクト ノードのコンテキスト メニューには、ビルドを HockeyApp にアップロードするオプションが追加されました。

![Windows フォーム アプリのプロジェクト コンテキスト メニュー](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

このオプションを選択すると、HockeyApp のアップロード ダイアログが、ユニバーサル Windows アプリの場合と同様に表示されます。

![Upload dialog for Windows Forms apps](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

このウィザードに、アプリのバージョンを指定するための新しいフィールドが追加されていることに注目してください。ユニバーサル Windows アプリの場合、この情報はマニフェストから取得されます。あいにく Windows フォーム アプリに関しては、これと同等の機能がありません。手動で指定する必要があります。

その他のフローはユニバーサル Windows アプリと同様です。ビルドを選択し、リリース オプションを選択して、リリース ノートを追加し、アップロードして、HockeyApp ポータルで管理します。

このように操作は簡単です。ぜひ試してご意見をお聞かせください。
## バージョン 4.3
### ローカル デバッグ セッションからのテレメトリの検索
このリリースでは、Visual Studio デバッグ セッションで生成された Application Insights Telemetry を検索できるようになりました。従来、検索を利用できるのは、Application Insights にアプリを登録した場合に限られていました。今後は、Application Insights SDK がインストールされていれば、アプリでローカルのテレメトリを検索することができます。

ASP.NET アプリケーションと Application Insights SDK がある場合は、次の手順で検索機能を利用できます。

1. アプリケーションをデバッグします。
2. 次のいずれかの方法で [Application Insights の検索] を開きます。
	- **[表示]** メニューの **[その他のウィンドウ]** をクリックし、**[Application Insights の検索]** をクリックします。
	- **[Application Insights]** ツール バー ボタンをクリックします。
	- ソリューション エクスプ ローラーで、**[ApplicationInsights.config]** を展開し、**[デバッグ セッションのテレメトリの検索]** をクリックします。
3. Application Insights にサインアップしていない場合、[検索] ウィンドウはデバッグ セッションのテレメトリ モードで開きます。
4. ローカルのテレメトリを表示するには、**[検索]** アイコンをクリックします。

![アップロード完了](./media/app-insights-release-notes-vsix/LocalSearch.png)

## バージョン 4.2
このリリースでは、イベントのコンテキストでのデータ検索を容易にする機能、複数のデータ イベントからコードに移動する機能、およびログ データを Application Insights に送信するための非常に簡単なエクスペリエンスが追加されました。この拡張機能は毎月更新されます。ご意見や機能に関するご要望がありましたら、aidevtools@microsoft.com までお寄せください。
### クリックレスのログ記録
既に NLog、log4net、System.Diagnostics.Tracing を使用している場合、必要なトレースを自分ですべて Application Insights に移動する必要はありません。このリリースでは、標準的な構成機能に Application Insights ログ アダプターを統合しました。前述のいずれかのログ記録フレームワークが既に構成されている場合の取得方法を次のセクションで説明します。**Application Insights が既に追加されている場合: **
1. プロジェクト ノードを右クリックし、**[Application Insights]** をクリックして、**[Application Insights の構成]** をクリックします。構成ウィンドウで、正しいアダプターを追加するオプションが設定されていることを確認します。
2. または、ソリューションをビルドするときに、画面右上に表示されるポップアップ ウィンドウの **[構成]** をクリックします。

![Logging notification](./media/app-insights-release-notes-vsix/LoggingToast.png)

ログ アダプターをインストールしたら、アプリケーションを実行して、[診断ツール] タブに次のようにデータが表示されることを確認します。

![トレース](./media/app-insights-release-notes-vsix/Traces.png)

### テレメトリ イベント プロパティが生成されるコードへの移動、またはこれらのコードの検索
新しいリリースでは、ユーザーはイベント詳細の任意の値をクリックすることで、現在開いているソリューション内で一致する文字列を検索できます。結果は、Visual Studio の [検索結果] リストに次のように表示されます。

![Find match](./media/app-insights-release-notes-vsix/FindMatch.png)

### サインインしていないときに表示される新しい検索ウィンドウ
Application Insights の検索ウィンドウの外観が改善され、アプリが稼働状態であるときにデータが検索しやすくなりました。

![Search window](./media/app-insights-release-notes-vsix/SearchWindow.png)

### イベントに関連するすべてのテレメトリ イベントが表示可能に
イベントの詳細を表示するタブの横に、新しいタブを追加しました。このタブには、ユーザーが閲覧しているテレメトリ イベントに関連するすべてのデータを問い合わせるクエリがあらかじめ定義されています。たとえば要求には、**操作 ID** というフィールドが存在します。その要求に関連付けられているすべてのイベントには、同じ**操作 ID** の値が割り当てられます。その操作によって要求が処理されている間に例外が発生した場合、その例外には要求と同じ操作 ID が割り当てられるため、容易に見つけることができます。特定の要求を表示した状態で **[この操作のすべてのテレメトリ]** をクリックすると、新しいタブが開いて新しい検索結果が表示されます。

![関連項目](./media/app-insights-release-notes-vsix/RelatedItems.png)

### 前後の検索結果の表示
前後の検索結果を表示できるようになりました。

![Go back](./media/app-insights-release-notes-vsix/GoBAck.png)

## Version 4.1
このリリースでは、さまざまな新機能が追加され、既存の機能も多数更新されています。このリリースをインストールするには、Update 1 がインストールされている必要があります。

### 例外からソース コード内のメソッドへのジャンプ
運用アプリから発生した例外を [Application Insights の検索] ウィンドウで表示した場合に、その例外の発生元となったコード内のメソッドに移動できるようになりました。必要な操作は、正しいプロジェクトを読み込むだけです。後はすべて Application Insights によって自動的に行われます。 [Application Insights の検索] ウィンドウの詳細については、この後の Version 4.0 のリリース ノートを参照してください。

それはどのように機能しますか? Application Insights の検索は、ソリューションを開いていないときでも使用できます。スタック トレース領域に情報メッセージが表示されます。ただしスタック トレースでは、項目の多くが利用できません。

ファイル情報が使用可能な場合は、いくつかの項目がリンクになりますが、ソリューション情報項目も引き続き表示されます。

ハイパーリンクをクリックすると、コード内の選択したメソッドの場所が表示されます。バージョン番号に違いがある可能性はありますが、今後のリリースでは、正しいバージョンのコードにジャンプする機能が提供される予定です。

![Click exception details](./media/app-insights-release-notes-vsix/jumptocode.png)

### ソリューション エクスプローラーの検索エクスペリエンスへの新しいエントリ ポイント
ソリューション エクスプローラーから検索機能にアクセスできるようになりました。

![Search in Solution Explorer](./media/app-insights-release-notes-vsix/searchentry.png)

### 発行が完了すると通知が表示されます。
プロジェクトがオンラインに発行されると、ポップアップ ダイアログが表示され、運用環境の Application Insights データを見ることができます。

![Publish complete notification](./media/app-insights-release-notes-vsix/publishtoast.png)

## Version 4.0

### Visual Studio 内から Application Insights データを検索
Application Insights ポータルでの検索機能と同じように、Visual Studio 内からイベントの種類やプロパティ値、テキストをフィルターで抽出して検索し、そのうえで個々のイベントを調査することができます。

![Search window](./media/app-insights-release-notes-vsix/search.png)

### ローカル コンピューターからのデータを診断ツールで表示

Visual Studio の [診断ツール] ページで、ローカルにあるテレメトリや他のデバッグ データを表示できます。ASP.NET 4.5 のみサポートされます。

![Diagnostic Tools page](./media/app-insights-release-notes-vsix/diagtools.png)

### Azure にサインインすることなくプロジェクトに SDK を追加

**[新しいプロジェクト]** ダイアログまたはプロジェクトのコンテキスト メニューで Application Insights パッケージをプロジェクトに追加する際、Azure にサインインする必要がなくなりました。サインインした場合は、以前と同じように、SDK がインストールされ、ポータルにテレメトリを送信するように構成されます。サインインしない場合は、SDK がプロジェクトに追加され、診断ハブ用にテレメトリが生成されます。これは、後で必要に応じて構成できます。

![[新しいプロジェクト] ダイアログ](./media/app-insights-release-notes-vsix/newproject.png)

### デバイスのサポート

*Connect();* 2015 において、Microsoft は、モバイル デバイス向けのアプリ開発を支援する機能として HockeyApp を[発表](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)しました。HockeyApp は、テスト担当者へのベータ版ビルドの配布、アプリのすべてのクラッシュの収集と分析、およびお客様からのフィードバックの直接収集を支援します。HockeyApp は、iOS、Android、Windows や、Xamarin、Cordova、Unity のようなクロスプラットフォーム ソリューションなど、あらゆるプラットフォームのアプリに対応しています。

Application Insights 拡張機能の今後のリリースでは、HockeyApp と Visual Studio の連携をさらに進化させる予定です。現時点では、NuGet 参照を追加するだけで HockeyApp を開始できます。詳細については、[ドキュメント](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)をご覧ください。

<!---HONumber=AcomDC_0727_2016-->