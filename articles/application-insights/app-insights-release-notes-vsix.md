<properties 
	pageTitle="Application Insights の Visual Studio 拡張機能のリリース ノート" 
	description="Application Insights の Visual Studio ツールの最新の更新内容。" 
	services="application-insights" 
    documentationCenter=""
	authors="dimazaid" 
	manager="douge"/>
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="dimazaid"/>
 
# Application Insights Tools for Visual Studio v 4.2 のリリース ノート
##バージョン 4.2
このリリースでは、イベントのコンテキストでのデータ検索を容易にする機能、複数のデータ イベントからコードに移動する機能、およびログ データを Application Insights に送信するための非常に簡単なエクスペリエンスが追加されました。この拡張機能は月単位で更新されます。フィードバックや機能についてリクエストがある場合は、aidevtools@microsoft.com に送信してください。
###- 0 クリックのログ記録エクスペリエンス
NLog、Log4Net や System.Diagnostics のトレースを既に使用している場合は、AI へのすべてのトレースの移動について心配する必要はありません。通常の構成エクスペリエンスと Application Insights のログ記録アダプターが統合されました。既にこれらのログ記録フレームワークを構成してある場合は、次の手順でトレースを移動できます。
####既に Application Insights を追加してある場合
- プロジェクト ノードを右クリックし、[Application Insights]、[Application Insights の構成] の順に選択します。構成ウィンドウで、正しいアダプターを追加するオプションが設定されていることを確認します。 
- または、ソリューションをビルドするときに、画面右上に表示されるポップアップに気付いて、[構成] をクリックします。![ログイン トースト](./media/app-insights-release-notes-vsix/LoggingToast.png)

ログ アダプターをインストールした後、アプリケーションを実行することができ、[診断ツール] タブに次のようにデータが表示されます。![トレース](./media/app-insights-release-notes-vsix/Traces.png)
###- テレメトリ イベント プロパティが生成されるコードへの移動、またはこれらのコードの検索が可能に
新しいリリースでは、ユーザーはイベント詳細の任意の値をクリックすることで、現在開いているソリューション内で一致する文字列を検索できます。結果は、Visual Studio の [検索結果] リストに次のように表示されます。 ![一致の検索](./media/app-insights-release-notes-vsix/FindMatch.png)
###- サインインしていないユーザーに表示される [検索] ウィンドウの新しい画面
[検索] ウィンドウの外観が機能強化され、実稼働環境でのデータ検索手順をユーザーにガイドするようになりました。 ![[検索] ウィンドウ](./media/app-insights-release-notes-vsix/SearchWindow.png)
###- イベントに関連するすべてのテレメトリ イベントが表示可能に
イベント詳細の横に新しいタブが追加されました。このタブには、ユーザーが表示しているテレメトリ イベントに関連するすべてのデータを表示する定義済みクエリが含まれています。例: 要求には操作 ID と呼ばれるフィールドがあり、この要求に関連付けられているすべてのイベントは同じ操作 ID を持ちます。そのために、たとえば、要求の処理中に例外が発生した場合は、要求と同じ操作 ID を受け取るため、例外を見つけやすくなります。要求を見ているユーザーが、[この操作のすべてのテレメトリ] をクリックすると、新しい検索結果を含む新しいタブが開くようになりました。 ![関連項目](./media/app-insights-release-notes-vsix/RelatedItems.png)
### - 検索の前/後の履歴を追加
ユーザーは、検索結果の間で前後に移動できるようになりました。 ![戻る](./media/app-insights-release-notes-vsix/GoBAck.png)

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

 

<!---HONumber=AcomDC_0211_2016-->