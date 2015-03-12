<properties 
	pageTitle="メディア サービス用にコンピューターをセットアップする方法 - Azure" 
	description="Media Services SDK for .NET を使用した Media Services の前提条件について説明します。Visual Studio アプリケーションを作成する方法についても説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1><a name="setup-dev"> </a><span class="short header">メディア サービス開発のためのコンピューターのセットアップ</span></h1> 

このセクションでは、Media Services SDK for .NET を使用したメディア サービス開発の大まかな前提条件について説明します。また、メディア サービス SDK 開発のための Visual Studio アプリケーションの作成方法を開発者向けに紹介しています。 

### 前提条件

-   新規または既存の Azure サブスクリプションで作成した Media Services アカウント。[Media Services アカウントの作成方法](../media-services-create-account/)に関するページを参照してください。
-   オペレーティング システム:Windows 7、Windows 2008 R2、または Windows 8。
-   .NET Framework 4
-   Visual Studio 2013、Visual Studio 2012 または Visual Studio 2010 SP1 (Professional、Premium、Ultimate、または Express) 
-   [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) パッケージを使用して Azure SDK for .NET をインストールします。次のセクションでは、**Nuget** を使用して Azure SDK をインストールする方法を示します。
  
<h2><a name="setup-account"></a><span class="short header">Media Services に使用する Azure アカウントの設定</span></h2>

Media Services アカウントを設定するには、Azure の管理ポータル (推奨) を使用します。[Media Services アカウントの作成方法][]に関するページを参照してください。管理ポータルでアカウントを作成すると、メディア サービス開発用にコンピューターをセットアップできるようになります。 

### Visual Studio でのアプリケーションの作成

このセクションでは、メディア サービス開発用に Visual Studio でプロジェクトを作成し、セットアップする方法を説明します。  ここでは C# Windows コンソール アプリケーション プロジェクトを使用していますが、Media Services アプリケーション用に作成できる他の種類のプロジェクト (Windows フォーム アプリケーション、ASP.NET Web アプリケーションなど) についても、同じセットアップ手順を利用できます。

   1. Visual Studio 2013、Visual Studio 2012 または Visual Studio 2010 SP1 で、C# の新しい**コンソール アプリケーション**を  作成します。**[名前]**、**[場所]**、**[ソリューション名]** を入力し、**[OK]** をクリックします。
   2. ターゲット フレームワークが .NET Framework 4 に設定されていることを確認します。確認するには、Visual Studio プロジェクトをマウスのボタンで右クリックし、[プロパティ] を選択します。[アプリケーション] タブで、ターゲット フレームワークを .NET Framework 4 に設定します。   
   3. **System.Configuration** アセンブリへの参照を追加します。**System.Configuration** への参照を追加するには、**ソリューション エクスプローラー**で **[参照]** ノードを右クリックし、**[参照の追加]** を選択します。**[参照の管理]** ダイアログで、**[System.Configuration]** を選択し、**[OK]** をクリックします。
   4. [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices) パッケージを使用して **Azure SDK for .NET** への参照を追加します(他のすべての従属アセンブリも同様にインストールされます)。  

	Nuget を使用して参照を追加するには、次の操作を行います。Visual Studio の**メイン メニュー**で、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。コンソール ウィンドウで「**Install-Package windowsazure.mediaservices**」と入力し、**Enter** キーを押します。
   4. Program.cs ファイルの先頭にある既存のステートメントを次のコードで上書きします。

   		using System;
		using System.Linq;
		using System.Configuration;
		using System.IO;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Collections.Generic;
		using Microsoft.WindowsAzure.MediaServices.Client;

これで、メディア サービス アプリケーションの開発準備が整いました。    
   
<h2><a name="setup-account"></a><span class="short header">メディア サービスへの接続</span></h2> 

メディア サービス プログラミングで行うほぼすべての作業は、サーバー コンテキスト オブジェクトへの参照を必要とします。メディア サービスのあらゆるプログラミング オブジェクトは、サーバー コンテキストを通じてプログラムからアクセスすることができます。

サーバー コンテキストへの参照を取得するには、次のコード例のように、コンテキスト型の新しいインスタンスを作成します。コンストラクターには、(アカウント設定プロセスで入手した) メディア サービスのアカウント名とアカウント キーを渡します。 

    // Create and cache the Media Services credentials in a static class variable.
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);

	// Use the cached credentials to create CloudMediaContext.
	_context = new CloudMediaContext(_cachedCredentials);


多くの場合、サーバー コンテキストへの参照を保持するように **CloudMediaContext** 型のモジュール レベル変数を定義する際に役立ちます。詳細については、「[Media Services SDK による Media Services への接続](http://msdn.microsoft.com/library/azure/jj129571.aspx)」を参照してください。

このトピックのコード例では以降、**_context** という変数を使用してサーバー コンテキストを参照します。 

<h2>次のステップ</h2>
これで、コンピューターをセットアップし、Media Services のプログラミングのための Visual Studio ソリューションを作成できました。次は、[暗号化されたアセットを作成してストレージにアップロードする方法][]に関するトピックに進みます。
[Media Services アカウントの作成方法]: ../media-services-create-account/
[暗号化されたアセットを作成してストレージにアップロードする方法]: ../media-services-create-encrypted-asset-upload-storage/


<!--HONumber=42-->
