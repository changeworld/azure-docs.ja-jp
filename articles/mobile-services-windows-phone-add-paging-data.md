<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# ページングを使用したモバイル サービス クエリの改善

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/ja-jp/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/ja-jp/documentation/articles/mobile-services-android-add-paging-data" title="Android" class="current">Android</a><a href="/ja-jp/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title=".NET バックエンド">.NET バックエンド</a> |  <a href="/ja-jp/documentation/articles/mobile-services-windows-phone-add-paging-data"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>

このトピックでは、ページングを使用して、Azure のモバイル サービスから Windows Phone アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

> [WACOM.NOTE] モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。

このチュートリアルは、前の[データの使用][データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用][データの使用]」) を完了している必要があります。

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a> 次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

-   [認証の使用][認証の使用]
    Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

-   [プッシュ通知の使用][プッシュ通知の使用]
    アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

 
 


  [データの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-data/
  [認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-users/
  [プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-push/
