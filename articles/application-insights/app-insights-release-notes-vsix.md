<properties
	pageTitle="開発者分析用 Visual Studio 拡張機能のリリース ノート"
	description="開発者分析用 Visual Studio ツールの更新内容。"
	services="application-insights"
    documentationCenter=""
	authors="aruna"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="acearun"/>

# リリース ノート - Developer Analytics Tools
##### Visual Studio での Application Insights と HockeyApp の分析
## バージョン 5.2
Visual Studio に HockeyApp シナリオを導入することになりました。最初に有効にした統合は、Visual Studio でのユニバーサル Windows アプリと Windows フォーム アプリのベータ版配布です。

ベータ版配布を使用すると、旧バージョンのアプリを HockeyApp にアップロードし、一部の顧客やテスターに配布することができます。ベータ版配布と HockeyApp のクラッシュ コレクションとユーザー フィードバック機能を組み合わせることで、広範なリリース前にアプリに関する重要な情報を収集できます。この情報を使用すると、アプリの問題 (低評価、よくないフィードバックなど) が大きくなる前に対処することができます。

Visual Studio からベータ版配布用にビルドをアップロードする方法は簡単です。ここではその方法について説明します。
### ユニバーサル Windows アプリ
UWP プロジェクト ノードのコンテキスト メニューには、ビルドを HockeyApp にアップロードするオプションが追加されました。

![ユニバーサル アプリのプロジェクト コンテキスト メニュー](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

項目を選択すると、HockeyApp アップロード ダイアログが表示されます。ビルドをアップロードするには、HockeyApp アカウントが必要です。新規ユーザーの場合でも、アカウントを簡単に作成できます。

接続すると、ダイアログにアップロード フォームが表示されます。

![ユニバーサル アプリのアップロード ダイアログ](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

アップロードするコンテンツ (appxbundle または appx) を選択し、ウィザードでリリース オプションを選択します。必要に応じて、次のページでリリース ノートを追加できます。[Finish] を選択してアップロードを開始します。

アップロードが完了すると、HockeyApp のトーストに確認メッセージと HockeyApp ポータルのアプリへのリンクが表示されます。

![アップロード完了のトースト](./media/app-insights-release-notes-vsix/UploadComplete.png)

以上で完了です。わずか数回のクリックで、ベータ版配布のビルドをアップロードできます。

HockeyApp ポータルでは、さまざまな方法でアプリケーションを管理できます (ユーザーの招待、クラッシュ レポートとフィードバックの表示、変更履歴など)。

![HockeyApp ポータル](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

アプリ管理の詳細については、[HockeyApp のナレッジ ベース](http://support.hockeyapp.net/kb/app-management-2)を参照してください。

### Windows フォーム アプリ
Windows フォーム プロジェクト ノードのコンテキスト メニューには、ビルドを HockeyApp にアップロードするオプションが含まれています。

![Windows フォーム アプリのプロジェクト コンテキスト メニュー](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

このオプションを選択すると、ユニバーサル アプリの場合と同様に HockeyApp のアップロード ダイアログが表示されます。

![Windows フォーム アプリのアップロード ダイアログ](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

このウィザードには、アプリのバージョンを指定する追加のフィールドがあります。ユニバーサル アプリの場合、情報はマニフェストから生成されます。残念ながら Windows フォームに同等の機能はないので、手動で指定する必要があります。

その他のフローはユニバーサル アプリと同様です。ビルドを選択し、リリース オプションを選択し、リリース ノートを追加し、アップロードし、HockeyApp ポータルで管理します。

このように操作は簡単です。ぜひ試してご意見をお聞かせください。
## バージョン 4.3
### ローカル デバッグ セッションからのテレメトリの検索
このリリースでは、Visual Studio デバッグ セッションで生成された Application Insights テレメトリを検索する機能が導入されました。以前は、Application Insights でアプリを登録していた場合のみ検索可能でした。このリリースでは、ローカルのテレメトリを検索するために、アプリに Application Insights SDK のみインストールする必要があります。

#### Application Insights SDK を使用する ASP.NET アプリケーションがある場合

- アプリケーションをデバッグします。
- 次のいずれかの方法で、Application Insights の [検索] を開く
	- [表示] メニュー -> [その他のウィンドウ] -> Application Insights の [検索]
	- Application Insights のツール バー ボタンをクリックします。
	- ソリューション エクスプ ローラーで、[ApplicationInsights.config] -> [デバッグ セッションのテレメトリの検索]の順に展開します。
- Application Insights にサインアップしていない場合、[検索] ウィンドウは [デバッグ セッションのテレメトリ] モードで開きます。
- ローカルのテレメトリを表示するには、[検索] アイコンをクリックします。

![アップロード完了](./media/app-insights-release-notes-vsix/LocalSearch.png)



##バージョン 4.2
このリリースでは、イベントのコンテキストでのデータ検索を容易にする機能、複数のデータ イベントからコードに移動する機能、およびログ データを Application Insights に送信するための非常に簡単なエクスペリエンスが追加されました。この拡張機能は月単位で更新されます。フィードバックや機能についてリクエストがある場合は、aidevtools@microsoft.com に送信してください。
###- 0 クリックのログ記録エクスペリエンス
NLog、Log4Net や System.Diagnostics のトレースを既に使用している場合は、AI へのすべてのトレースの移動について心配する必要はありません。通常の構成エクスペリエンスと Application Insights のログ記録アダプターが統合されました。既にこれらのログ記録フレームワークを構成してある場合は、次の手順でトレースを移動できます。
####既に Application Insights を追加してある場合
- プロジェクト ノードを右クリックし、[Application Insights]、[Application Insights の構成] の順に選択します。構成ウィンドウで、正しいアダプターを追加するオプションが設定されていることを確認します。
- または、ソリューションをビルドするときに、画面右上に表示されるポップアップに気付いて、[構成] をクリックします。![ログイン トースト](./media/app-insights-release-notes-vsix/LoggingToast.png)

ログ アダプターをインストールした後、アプリケーションを実行することができ、[診断ツール] タブに次のようにデータが表示されます。![トレース](./media/app-insights-release-notes-vsix/Traces.png)
###- テレメトリ イベント プロパティが生成されるコードへの移動、またはこれらのコードの検索が可能に
新しいリリースでは、ユーザーはイベント詳細の任意の値をクリックすることで、現在開いているソリューション内で一致する文字列を検索できます。結果は、Visual Studio の [検索結果] リストに次のように表示されます。![一致の検索](./media/app-insights-release-notes-vsix/FindMatch.png)
###- サインインしていないユーザーに表示される [検索] ウィンドウの新しい画面
[検索] ウィンドウの外観が機能強化され、実稼働環境でのデータ検索手順をユーザーにガイドするようになりました。![[検索] ウィンドウ](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- イベントに関連するすべてのテレメトリ イベントが表示可能に
イベント詳細の横に新しいタブが追加されました。このタブには、ユーザーが表示しているテレメトリ イベントに関連するすべてのデータを表示する定義済みクエリが含まれています。例: 要求には操作 ID と呼ばれるフィールドがあり、この要求に関連付けられているすべてのイベントは同じ操作 ID を持ちます。そのために、たとえば、要求の処理中に例外が発生した場合は、要求と同じ操作 ID を受け取るため、例外を見つけやすくなります。要求を見ているユーザーが、[この操作のすべてのテレメトリ] をクリックすると、新しい検索結果を含む新しいタブが開くようになりました。![関連項目](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - 検索の前/後の履歴を追加
ユーザーは、検索結果の間で前後に移動できるようになりました。![戻る](./media/app-insights-release-notes-vsix/GoBAck.png)

##Version 4.1
このリリースには、多くの新しい機能と、既存の機能の強化が含まれています。このリリースを入手するには、お使いのコンピューターに Update 1 をインストールしておく必要があります。

### 例外からソース コード内のメソッドへのジャンプ
Application Insights の [検索] ウィンドウで運用アプリの例外を表示しているユーザーが、コード内で例外が発生しているメソッドにジャンプできるようになりました。適切なプロジェクトを読み込んでおくだけで、後は自動的に行われます ([検索] ウィンドウの詳細については、以下の 4.0 リリース ノートを参照してください)

#### それはどのように機能しますか?

ソリューションが開かれていない場合は、ソリューションを開かなくても AI Search を使用できます。その場合、スタック トレース領域には情報メッセージが表示され、スタック トレース内の多くの項目は淡色表示されます。


ファイル情報が使用可能な場合は、いくつかの項目がリンクになりますが、ソリューション情報項目も引き続き表示されます。

ハイパーリンクをクリックすると、コード内の選択したメソッドがある場所に移動します。バージョン番号が異なる場合がありますが、正しいバージョンのコードにジャンプする機能は今後のリリースで提供されます。

![Clicking on Exception](./media/app-insights-release-notes-vsix/jumptocode.png)

###ソリューション エクスプローラーの検索エクスペリエンスへの新しいエントリ ポイント

![Entry Point in Solution Explorer](./media/app-insights-release-notes-vsix/searchentry.png)


###発行の完了時にトーストをポップアップ
プロジェクトがオンラインに発行されると、ポップアップが表示されるので、運用環境の Application Insights データを見ることができます。

![Popup](./media/app-insights-release-notes-vsix/publishtoast.png)

## Version 4.0

###Visual Studio 内で Application Insights データを検索
Application Insights ポータルでの検索と同じように、イベントの種類、プロパティ値、テキストに対するフィルター処理と検索、個々のイベントの確認を行うことができます。

![Search window](./media/app-insights-release-notes-vsix/search.png)

###診断ツール ウィンドウでローカル ボックスから受け取ったデータを表示

テレメトリは、他のデバッグ データと共に、Visual Studio 診断ハブにも表示されます。これは ASP.NET 4.5 のみをサポートしています。ASP.NET 5 は、今後のリリースでサポートされる予定です。

![Diagnostics hub window](./media/app-insights-release-notes-vsix/diagtools.png)

###Azure にサインインすることなくプロジェクトに SDK を追加

新しいプロジェクトのダイアログでも、プロジェクトのコンテキスト メニューでも、Application Insights パッケージをプロジェクトに追加するために Azure にサインインする必要がなくなりました。サインインすると、以前と同じように、SDK がインストールされ、ポータルにテレメトリを送信するように構成されます。サインインしないと、SDK がプロジェクトに追加され、診断ハブ用にテレメトリが生成されます。これは、後で必要に応じて構成できます。

![[新しいプロジェクト] ダイアログ](./media/app-insights-release-notes-vsix/newproject.png)

###デバイスのサポート

*Connect();* 2015 において、Microsoft は、デバイスのモバイル DevOps エクスペリエンスが HockeyApp であることを[発表](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)しました。HockeyApp は、テスト担当者へのベータ版ビルドの配布、アプリのすべてのクラッシュの収集と分析、およびお客様からのフィードバックの直接収集を支援します。HockeyApp は、iOS、Android、Windows や、Xamarin、Cordova、Unity のようなクロスプラットフォーム ソリューションなど、モバイル アプリケーションをビルドするすべてのプラットフォームに対応しています。

Application Insights 拡張機能の今後のリリースでは、HockeyApp と Visual Studio のエクスペリエンスをさらに統合するための新しい機能を導入する予定です。現時点では、NuGet 参照を追加するだけで HockeyApp を開始できます。詳細については、[ドキュメント](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)を参照してください。

<!---HONumber=AcomDC_0330_2016-->