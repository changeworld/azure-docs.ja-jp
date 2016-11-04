---
title: Xamarin.iOS アプリに対して Azure App Service Mobile Apps を使用する | Microsoft Docs
description: このチュートリアルに従って、Xamarin.iOS 開発用の Azure Mobile Apps を使用します。
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: dwrede
editor: ''

ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: adrianha

---
# Xamarin.iOS アプリを作成する
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## Overview
このチュートリアルでは、Azure Mobile Apps バックエンドを使用して Xamarin.iOS モバイル アプリにクラウドベースのバックエンド サービスを追加する方法を示します。新しいモバイル アプリ バックエンドと、アプリのデータを Azure に保存する簡単な *Todo list* Xaamrin.iOS アプリの両方を作成します。

このチュートリアルは、Azure App Service での Mobile Apps 機能の使用に関する他のすべての Xamarin.iOS チュートリアルを実行する前に完了しておく必要があります。

## 前提条件
このチュートリアルを完了するには、以下が必要です。

* アクティブな Azure アカウント。アカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料モバイル アプリを入手できます。このアプリは評価終了後も使用できます。詳細については、[Azure の無料評価版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Visual Studio with Xamarin。手順については、「[Visual Studio と Xamarin の設定とインストール](https://msdn.microsoft.com/library/mt613162.aspx)」を参照してください。
* Xcode v7.0 以降と Xamarin Studio Community がインストールされた Mac。「[Setup and install for Visual Studio and Xamarin (Visual Studio と Xamarin を対象にしたセットアップとインストール)](https://msdn.microsoft.com/library/mt613162.aspx)」と「[Setup, install, and verifications for Mac users (Mac ユーザーを対象にしたセットアップ、インストール、および確認)](https://msdn.microsoft.com/library/mt488770.aspx)」 (MSDN) を参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、「[Azure App Service アプリケーションの作成](https://tryappservice.azure.com/?appServiceName=mobile)」にアクセスしてください。有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## 新しい Azure Mobile App バックエンドの作成
新しいモバイル アプリ バックエンドを作成するには、次の手順に従います。

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## サーバー プロジェクトの構成
これで、モバイル クライアント アプリケーションで使用できる Azure モバイル アプリ バックエンドのプロビジョニングが完了しました。次は、簡単な "todo list" バックエンドのサーバー プロジェクトをダウンロードして、それを Azure に発行します。

以下の手順に従って、Node.js または .NET バックエンドを使用するようにサーバー プロジェクトを構成します。

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## Xamarin.iOS アプリのダウンロードと実行
1. [Azure ポータル]をブラウザー ウィンドウで開きます。
2. モバイル アプリの [設定] ブレードで、**[開始]**、**[Xamarin.iOS]** の順にクリックします。手順 3 で、まだ選択されていない場合は **[新しいアプリを作成する]** をクリックします。次に、**[ダウンロード]** ボタンをクリックします。
   
      これにより、モバイル アプリに接続されているクライアント アプリケーションが含まれているプロジェクトがダウンロードされます。圧縮されたプロジェクト ファイルをローカル コンピューターに保存し、保存場所を書き留めておいてください。
3. ダウンロードしたプロジェクトを抽出し、Xamarin Studio または Visual Studio で開きます。
   
    ![][9]
   
    ![][8]
4. F5 キーを押してプロジェクトをビルドし、iPhone エミュレーターでアプリを起動します。
5. アプリで、意味のあるテキスト (「*Xamarin の学習*」など) を入力し、**+** ボタンをクリックします。
   
    ![][10]
   
    これで、Azure でホストされている新しいモバイル アプリ バックエンドに POST 要求が送信されます。要求のデータは TodoItem テーブルに挿入されます。テーブルに格納された項目がモバイル アプリ バックエンドによって返され、データが一覧に表示されます。

> [!NOTE]
> モバイル アプリ バックエンドにアクセスして QSTodoService.cs C# ファイルにあるデータを照会および挿入するコードを確認できます。
> 
> 

## 次のステップ
* [アプリへの認証の追加](app-service-mobile-xamarin-ios-get-started-users.md) <br/>ID プロバイダーを使用してアプリのユーザーを認証する方法について説明します。
* [アプリケーションにプッシュ通知を追加する](app-service-mobile-xamarin-ios-get-started-push.md) <br/>アプリにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure ポータル]: https://portal.azure.com/

<!---HONumber=AcomDC_1005_2016-->