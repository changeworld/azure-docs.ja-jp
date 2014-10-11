<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="setup-dev"> </a><span class="short header">メディア サービス開発のためのコンピューターのセットアップ</span>

このセクションでは、Media Services SDK for .NET を使用したメディア サービス開発の大まかな前提条件について説明します。また、メディア サービス SDK 開発のための Visual Studio アプリケーションの作成方法を開発者向けに紹介しています。

### 前提条件

-   新規または既存の Azure サブスクリプションで作成した Media Services アカウント。「[メディア サービス アカウントの作成方法][]」を参照してください。
-   オペレーティング システム: Windows 7、Windows 2008 R2、または Windows 8。
-   .NET Framework 4.5 または .NET Framework 4。
-   Visual Studio 2012 または Visual Studio 2010 SP1 (Professional、Premium、Ultimate、または Express)。
-   [windowsazure.mediaservices Nuget][] パッケージを使用して Azure SDK for .NET をインストールします。次のセクションでは、**Nuget** を使用して Azure SDK をインストールする方法を示します。

## <a name="setup-account"></a><span class="short header">Media Services に使用する Azure アカウントの設定</span>

Media Services アカウントを設定するには、Azure の管理ポータル (推奨) を使用します。「[メディア サービス アカウントの作成方法][]」を参照してください。管理ポータルでアカウントを作成すると、メディア サービス開発用にコンピューターをセットアップできるようになります。

### Visual Studio でのアプリケーションの作成

このセクションでは、メディア サービス開発用に Visual Studio でプロジェクトを作成し、セットアップする方法を説明します。ここでは C# Windows コンソール アプリケーション プロジェクトを使用していますが、Media Services アプリケーション用に作成できる他の種類のプロジェクト (Windows フォーム アプリケーション、ASP.NET Web アプリケーションなど) についても、同じセットアップ手順を利用できます。

1.  Visual Studio 2012 または Visual Studio 2010 SP1 で、C# の新しい**コンソール アプリケーション**を作成します。**[名前]**、**[場所]**、**[ソリューション名]** を入力し、**[OK]** をクリックします。
2.  **System.Configuration** アセンブリへの参照を追加します。**System.Configuration** への参照を追加するには、**ソリューション エクスプローラー**で **[参照]** ノードを右クリックし、**[参照の追加]** を選択します。**[参照の管理]** ダイアログで、**[System.Configuration]** を選択し、**[OK]** をクリックします。
3.  [windowsazure.mediaservices Nuget][] パッケージを使用して、**Azure SDK for .NET** (Microsoft.WindowsAzure.StorageClient.dll)、**Azure Media Services SDK for .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll)、および **WCF Data Services 5.0 for OData V3** (Microsoft.Data.OData.dll) ライブラリへの参照を追加します。

    Nuget を使用して参照を追加するには、次の操作を行います。Visual Studio の**メイン メニュー**で、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。コンソール ウィンドウで「**Install-Package windowsazure.mediaservices**」と入力し、**Enter** キーを押します。

4.  Program.cs ファイルの先頭にある既存のステートメントを次のコードで上書きします。

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

これで、メディア サービス アプリケーションの開発準備が整いました。

## <a name="setup-account"></a><span class="short header">メディア サービスへの接続</span>

メディア サービス プログラミングで行うほぼすべての作業は、サーバー コンテキスト オブジェクトへの参照を必要とします。メディア サービスのあらゆるプログラミング オブジェクトは、サーバー コンテキストを通じてプログラムからアクセスすることができます。

サーバー コンテキストへの参照を取得するには、次のコード例のように、コンテキスト型の新しいインスタンスを取得します。コンストラクターには、(アカウント設定プロセスで入手した) メディア サービスのアカウント名とアカウント キーを渡します。

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);
    } 

**CloudMediaContext** 型の変数をモジュール レベルで定義しておくと、**GetContext** などのメソッドから返されたサーバー コンテキストへの参照を格納する際に便利です。このトピックのコード例では以降、\*\*\_context\*\* という変数を使用してサーバー コンテキストを参照します。

## 次のステップ

これで、コンピューターをセットアップし、Media Services のプログラミングのための Visual Studio ソリューションを作成できました。次は、[暗号化されたアセットを作成してストレージにアップロードする方法](http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409) に関するトピックに進みます。

[メディア サービス アカウントの作成方法]: <http://go.microsoft.com/fwlink/?linkid=256662>
[方法: 暗号化されたアセットを作成してストレージにアップロードする]: <http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409>

  [メディア サービス アカウントの作成方法]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
