<properties 
	pageTitle="ストリーミング ログとコンソール" 
	description="ストリーミング ログとコンソールの概要" 
	authors="btardif" 
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
	ms.date="11/12/2015" 
	ms.author="byvinyal"/>

#ストリーミング ログとコンソール

### ストリーミング ログ ###

Microsoft Azure ポータルには、統合されたストリーミング ログ ビューアーが用意されており、Azure App Service Web Apps のトレース イベントをリアルタイムで表示できます。

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

Trace クラスは System.Diagnostics 名前空間にあります。

node.js アプリケーションでは、次のコードを記述すると同じ結果を得られます。

`````````````````````````
console.log("My trace statement").
`````````````````````````

### 方法: ストリーミング ログを有効にして表示する ###

![][BrowseSitesScreenshot] 診断は Web アプリごとに有効になります。[ポータル](https://portal.azure.com)で左側のメニュー バーにある **[参照] (1)** ボタンをクリックし、**[Web Apps] (2)** をクリックすると、既存のすべての Web アプリの**一覧 (3)** が表示されます。

構成する Web アプリの名前をクリックして移動します。
  
![][DiagnosticsLogs] **[設定] (1)**、**[診断ログ] (2)** の順にクリックし、**[アプリケーション ログ (ファイル システム)] (3)** を **[オン]** にします。**[レベル]** オプションでは、取り込むトレースの重要度レベルを変更できます。**[詳細]** はこの機能に詳しくなっておこうとする場合に設定してください。この設定では、トレース ステートメントのすべてがログに記録されるためです。

ブレードの上部にある **[保存]** をクリックします。これで、ログを表示できる状態になりました。

**注:** **重大度レベル**を高くすると、ログを記録するために消費されるリソースが増大し、取得できるトレースが増えます。トラフィックの多いサイトや運用サイトでこの機能を使用する場合は、適切なレベルに設定されていることを確認してください。

![][StreamingLogsScreenshot] ポータル内からストリーミング ログを表示するには、**[ツール] (1)**、**[ログ ストリーム] (2)** の順にクリックします。アプリによってトレース ステートメントがアクティブに書き込まれている場合は、結果ウィンドウにそれらのステートメントがほぼリアルタイムで表示されます。

## コンソール ##

Azure ポータルには、Web アプリ環境にアクセスするためのコンソールが用意されています。コンソールでは、Web アプリのファイル システムを操作したり、powershell/cmd スクリプトを実行したりできます。コンソールのコマンドを実行するときは、実行中の Web アプリのコードに設定された同じアクセス許可が適用されます。保護されたディレクトリにアクセスしたり、より高いレベルのアクセス許可が必要なスクリプトを実行したりすることはできません。

![][ConsoleScreenshot] コンソールにアクセスするには、前のセクションで説明しているように Web アプリを参照します。**[ツール]**、**[コンソール]** の順にクリックすると、コンソールが開きます。

コンソールに詳しくなるには、次のような基本的なコマンドを試してください。



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Nov15_HO4-->