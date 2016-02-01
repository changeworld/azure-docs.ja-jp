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
 
# Application Insights Tools for Visual Studio v 4.1 のリリース ノート

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

Connect(); 2015 において、Microsoft は、デバイスのモバイル DevOps エクスペリエンスが HockeyApp であることを[発表](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/)しました。HockeyApp は、テスト担当者へのベータ版ビルドの配布、アプリのすべてのクラッシュの収集と分析、およびお客様からのフィードバックの直接収集を支援します。HockeyApp は、iOS、Android、Windows や、Xamarin、Cordova、Unity のようなクロスプラットフォーム ソリューションなど、モバイル アプリケーションをビルドするすべてのプラットフォームに対応しています。

Application Insights 拡張機能の今後のリリースでは、HockeyApp と Visual Studio のエクスペリエンスをさらに統合するための新しい機能を導入する予定です。現時点では、NuGet 参照を追加するだけで HockeyApp を開始できます。詳細については、[ドキュメント](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)を参照してください。

 

<!---HONumber=AcomDC_0121_2016-->