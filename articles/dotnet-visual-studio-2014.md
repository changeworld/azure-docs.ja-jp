<properties 
	pageTitle="Azure SDK 2.4 for Visual Studio 14 CTP2 のインストール" 
	description="Azure SDK 2.4 と Visual Studio 14 CTP2 のインストール" 
	services="visual-studio-online" 
	documentationCenter=".net" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="visual-studio-online" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# Azure SDK 2.4 for Visual Studio "14" CTP のインストール

Azure SDK 2.4 for .NET と Visual Studio "14" CTP をインストールするには、次の手順を実行します。この手順では、Visual Studio "14" CTP による Azure 開発用の SDK、基本的なツール、および拡張機能ツールをインストールします。これは、他のバージョンの Visual Studio での使用を前提にしていません。

**メモ**:Azure SDK 2.4 には Visual Studio "14" CTP1 との互換性はありません。

Azure SDK 2.4 for .NET をインストールするには、次の手順に従います。

1. 最新の [Visual Studio "14" CTP](http://go.microsoft.com/fwlink/p/?LinkId=400776) をインストールします。

2. 次のリストのリンクをこの順序で使用して、Azure SDK の各コンポーネントをインストールします。次の各コンポーネントの x86 バージョンまたは x64 バージョンを選択します。

       <ul>
        <li>Azure Authoring Tools: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400892">MicrosoftAzureAuthoringTools-x86.msi</a> または <a href="http://go.microsoft.com/fwlink/p/?LinkId=400893">MicrosoftAzureAuthoringTools-x64.msi</a>。</li>
       <li>Azure コンピューティング エミュレーター: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400894">MicrosoftAzureEmulator-x86.exe</a> または <a href="http://go.microsoft.com/fwlink/p/?LinkId=400895">MicrosoftAzureEmulator-x64.exe</a>。</li>
       <li>Azure クライアント ライブラリ: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400896">MicrosoftAzureLibsForNet-x86.msi</a> または <a href="http://go.microsoft.com/fwlink/p/?LinkId=400897">MicrosoftAzureLibsForNet-x64.msi</a>。</li>
       <li>ストレージ エミュレーター: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400904">MicrosoftAzureStorageEmulator.msi</a>。                            ローカル SQL データベースに関する警告を受け取った場合、x86 に対しては<a href="http://go.microsoft.com/fwlink/p/?LinkId=400778">この場所</a>から、x64 に対しては<a href="http://go.microsoft.com/fwlink/p/?LinkId=400779">この場所</a>から SQL Server LocalDB 11.0 をインストールします。</li><li>Azure Tools for Visual Studio: <a href="http://go.microsoft.com/fwlink/p/?LinkId=400903">WindowsAzureTools.vs140.exe</a>。</li></ul>

## 既知の問題

1. Visual Studio 2013 がインストールされているコンピューターに Visual Studio "14" CTP2 をインストールすると、Visual Studio "14" CTP2 でモバイル サービスを開始できません。この問題を回避するには、  モバイル サービス プロジェクトで以下のアセンブリへの参照を追加します。

	* packages/Microsoft.Data.OData.5.6.0/lib/net40/Microsoft.Data.OData.dll
	* packages/Microsoft.Data.Edm.5.6.0/lib/net40/Microsoft.Data.Edm.dll

2. Azure の Web サイトおよびモバイル サービス向けのリモート デバッグは、Visual Studio "14" CTP2 では動作しません。

## リリース ノート

Azure SDK 2.4 の[リリース ノート](http://go.microsoft.com/fwlink/?LinkId=507517)を参照してください。

<!--HONumber=46--> 
