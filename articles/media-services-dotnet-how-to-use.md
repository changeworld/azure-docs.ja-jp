<properties 
	pageTitle=".NET を使用してメディア サービス開発を行うためにコンピューターをセットアップする方法" 
	description="Media Services SDK for .NET を使用したメディア サービスの前提条件について説明します。Visual Studio アプリケーションを作成する方法についても説明します。" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="juliako"/>

#.NET を使用したメディア サービス開発 

このトピックでは、.NET を使用してメディア サービス アプリケーションを開発する方法について説明します。 

**Azure Media Services .NET SDK** ライブラリを利用すると、.NET を使用してメディア サービスに対するプログラミングを行うことができます。.NET でより簡単に開発できるようにするための **Azure Media Services .NET SDK Extensions** ライブラリも提供されています。このライブラリには、.NET コードの簡素化に役立つ一連の拡張メソッドとヘルパー関数が含まれています。どちらのライブラリも、**NuGet** と **GitHub** を通じて利用できます。
 

##前提条件

-   新規または既存の Azure サブスクリプションで作成したメディア サービス アカウント。[メディア サービス アカウントの作成方法](media-services-create-account.md)のトピックをご覧ください。
-   オペレーティング システム: Windows 7、Windows 2008 R2、または Windows 8。
-   .NET Framework 4.5。
-   Visual Studio 2013、Visual Studio 2012 または Visual Studio 2010 SP1 (Professional、Premium、Ultimate、または Express)。 
  

##Visual Studio プロジェクトの作成と構成 

このセクションでは、メディア サービス開発用に Visual Studio でプロジェクトを作成し、セットアップする方法を説明します。ここでは C# Windows コンソール アプリケーション プロジェクトを使用していますが、メディア サービス アプリケーション用に作成できる他の種類のプロジェクト (Windows フォーム アプリケーション、ASP.NET Web アプリケーションなど) についても、同じセットアップ手順を利用できます。

このセクションでは、**NuGet** を使用して、Media Services .NET SDK と依存するその他のライブラリを追加する方法を説明します。 

また、GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) と [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) から最新の Media Services .NET SDK bits を取得してソリューションをビルドし、クライアント プロジェクトに参照を追加できます。必要なすべての依存関係が、自動的にダウンロードされ抽出されることにご注意ください。   

1. Visual Studio 2013、Visual Studio 2012 か Visual Studio 2010 SP1 で、C# の新しいコンソール アプリケーションを作成します。**[名前]**、**[場所]**、**[ソリューション名]** を入力し、[OK] をクリックします。 

2. ソリューションをビルドします。

2. **NuGet** を使用して、**Azure Media Services .NET SDK Extensions** をインストールして追加します。このパッケージをインストールすると、**Media Services .NET SDK** が一緒にインストールされるほか、必要な依存関係がすべて追加されます。
	1. 最新バージョンの NuGet がインストールされていることをご確認ください。詳しい情報とインストール手順については、[NuGet](http://nuget.codeplex.com/) をご覧ください。
	
	2. ソリューション エクスプローラーでプロジェクトの名前を右クリックし、[NuGet パッケージの管理...] を選択します。
	
		The Manage NuGet Packages dialog box appears.

	3. オンライン ギャラリーで、Azure Media Services Extensions を検索し、[Azure Media Services .NET SDK Extensions] を選択し、[インストール] をクリックします。
 
		プロジェクトが変更され、Media Services .NET SDK Extensions、Media Services .NET SDK、その他の依存アセンブリへの参照が追加されます。

	4. よりクリーンな開発環境を実現するために、NuGet パッケージの復元を有効にすることを検討してください。詳細については、[NuGet Package Restore (NuGet パッケージの復元)](http://docs.nuget.org/consume/package-restore) をご覧ください。

3. **System.Configuration** アセンブリへの参照を追加します。このアセンブリには、構成ファイル (App.config など) にアクセスするための System.Configuration.**ConfigurationManager** クラスが含まれています。 

	[参照の管理] ダイアログを使用して参照を追加するには、次の操作を行います。 

	1. ソリューション エクスプローラーで、プロジェクト名を右クリックします。次に、[追加と参照] を選択します。

		[参照の管理] ダイアログが表示されます。

	2. .NET framework のアセンブリで、System.Configuration アセンブリを探して選択します。
	3. [OK] をクリックします。


4. App.config ファイルを開き (既定で追加されていない場合はファイルをプロジェクトに追加してください)、ファイルに  *appSettings* セクションを追加します。Azure メディア サービスのアカウント名とアカウント キーの値を設定します。次の例をご覧ください。 
	
	**アカウント名**と**アカウント キー**情報を取得するには、**Azure の管理ポータル**を開いてメディア サービスのアカウントを選択し、**[キーの管理]** をクリックします。


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Program.cs ファイルの先頭にある既存のステートメントを次のコードで上書きします。

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;

これで、メディア サービス アプリケーションの開発準備が整いました。    

<!--HONumber=45--> 
