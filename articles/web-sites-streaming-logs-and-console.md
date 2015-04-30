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
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#ストリーミング ログとコンソール

### ストリーミング ログ ###

Microsoft Azure ポータルには、統合されたストリーミング ログ ビューアーが用意されており、Azure App Service Web アプリのトレース イベントをリアルタイムで表示できます。  

この設定には、次のいくつかの簡単な手順が必要です。

- コードにトレースを記述する
- Azure ポータル内からアプリケーション診断を有効にする
- Web アプリ ブレードの [ストリーミング ログ] パーツをクリックする

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

システム診断名前空間の Trace クラス。

In a node.js app you can write this code to achieve the same result:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 方法: ストリーミング ログを有効にして表示する ###

診断は Web アプリごとに有効になります。[ポータル](https://portal.azure.com)で左側のメニュー バーにある **[参照]** ボタンをクリックし、**[Web アプリ]** をクリックすると、既存のすべての Web サイトの一覧が表示されます。  

![][BrowseSitesScreenshot]

構成する Web アプリの名前をクリックします。次に、**[診断ログ]** パーツをクリックし、**[アプリケーション ログ記録 (ファイル システム)]** を **[オン]** に切り替えます。**[レベル]** オプションが表示されて、取り込むトレースの重要度レベルを変更できるようになります。**[詳細]** はこの機能に詳しくなっておこうとする場合に設定してください。この設定では、トレース ステートメントのすべてがログに記録されるためです。

ブレードの上部にある **[保存]** をクリックします。これで、ログを表示できる状態になりました。

ポータル内からストリーミング ログを表示するには、Web アプリ ブレードの **[ストリーミング ログ]** パーツをクリックします。アプリによってトレース ステートメントがアクティブに書き込まれている場合は、結果ウィンドウにそれらのステートメントがほぼリアルタイムで表示されます。

![][StreamingLogsScreenshot]

## コンソール ##

Azure ポータルには、Web アプリ環境にアクセスするためのコンソールが用意されています。コンソールでは、Web アプリのファイル システムを操作したり、powershell/cmd スクリプトを実行したりできます。コンソールのコマンドを実行するときは、実行中の Web アプリのコードに設定された同じアクセス許可が適用されます。保護されたディレクトリにアクセスしたり、より高いレベルのアクセス許可が必要なスクリプトを実行したりすることはできません。  

コンソールにアクセスするには、前のセクションで説明しているように Web アプリを参照します。**[コンソール]** パーツをクリックすると、コンソールが開きます。

![][ConsoleScreenshot]

コンソールに詳しくなるには、次のような基本的なコマンドを試してください。



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!--HONumber=52-->