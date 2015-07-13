<properties 
	pageTitle="ストリーミング ログとコンソール" 
	description="ストリーミング ログとコンソールの概要"
	authors="adamabdelhamed" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#ストリーミング ログとコンソール

### ストリーミング ログ ###

[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)には、統合されたストリーミング ログ ビューアーが用意されており、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps のトレース イベントをリアルタイムで表示できます。

この設定には、次のいくつかの簡単な手順が必要です。

- コードにトレースを記述する
- Azure プレビュー ポータル内からアプリケーション診断を有効にする
- Web アプリ ブレードの [ストリーミング ログ] タイルをクリックする

### 方法: コードにトレースを記述する ###

コードにトレースを記述するのは簡単です。C# では、次のコードを記述するだけです。

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Trace クラスは System.Diagnostics 名前空間にあります。

node.js アプリケーションでは、次のコードを記述すると同じ結果を得られます。

`````````````````````````
console.log("My trace statement").
`````````````````````````

### ストリーミング ログを有効にして表示する ###

診断は Web アプリごとに有効になります。

1. [Azure プレビュー ポータル](https://portal.azure.com)内から、**[参照]**、**[Web Apps]** をクリックして、すべての Web アプリの一覧を取得します。  

2. 構成する Web アプリの名前をクリックします。

3. **[すべての設定]**、**[診断ログ]**の順にクリックし、**[アプリケーション ログ記録 (ファイル システム)]** を **[オン]** に切り替えます。

4. **[レベル]** オプションが表示されて、取り込むトレースの重要度レベルを変更できるようになります。**[詳細]** はこの機能に詳しくなっておこうとする場合に設定してください。この設定では、トレース ステートメントのすべてがログに記録されるためです。

5. ブレードの上部にある **[保存]** をクリックします。これで、ログを表示できる状態になりました。

6. ポータル内からストリーミング ログを表示するには、Web アプリ ブレードの **[ストリーミング ログ]** タイルをクリックします。アプリによってトレース ステートメントがアクティブに書き込まれている場合は、結果ウィンドウにそれらのステートメントがほぼリアルタイムで表示されます。

![][StreamingLogsScreenshot]

## コンソールにアクセスする ##

Azure プレビュー ポータルには、Web アプリにアクセスするためのコンソールが用意されています。コンソールでは、Web アプリのファイル システムを操作したり、powershell/cmd スクリプトを実行したりできます。コンソールのコマンドを実行するときは、実行中の Web アプリのコードに設定された同じアクセス許可が適用されます。保護されたディレクトリにアクセスしたり、より高いレベルのアクセス許可が必要なスクリプトを実行したりすることはできません。

1. 前のセクションで説明しているように Web アプリのブレードを参照します。

2. **[コンソール]** タイルをクリックすると、コンソールが開きます。

![][ConsoleScreenshot]

コンソールに詳しくなるには、次のような基本的なコマンドを試してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=62-->