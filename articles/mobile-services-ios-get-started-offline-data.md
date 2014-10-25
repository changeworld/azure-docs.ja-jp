<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data sync in Mobile Services" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# モバイル サービスでのオフライン データの同期の使用

<div class="dev-center-tutorial-selector sublanding">
<a href="/ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows ストア C#">Windows ストア C#</a>
<a href="/ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS" class="current">iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

このチュートリアルでは、Mobile Services のオフライン同期機能について説明します。開発者はこの機能を使用することで、エンド ユーザーがネットワークにアクセスできなくても使用可能なアプリケーションを作成できます。

オフライン同期には、いくつかの潜在的な用途があります。

-   サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
-   断続的なネットワーク接続に対してアプリケーションに弾力性を持たせる。
-   エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
-   複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。

このチュートリアルでは、「[モバイル サービスの使用][モバイル サービスの使用]」または「[データの使用][データの使用]」のいずれかのチュートリアルで使用したアプリケーションを更新し、Azure Mobile Services のオフライン機能をサポートできるようにする方法を説明します。その後、切断されたオフラインの状況でデータを追加し、それらの項目をオンライン データベースに同期してから、Azure の管理ポータルにログインして、アプリケーションを実行したときにデータに加えた変更を表示します。

> [WACOM.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、[Azure の無料評価版サイト][Azure の無料評価版サイト]を参照してください。

## サンプル アプリケーションの取得

**完全なチュートリアルは近日提供予定です。それまでの間は、[iOS のオフライン ToDo リスト][iOS のオフライン ToDo リスト]のサンプルおよび[コンパニオン ブログの記事][コンパニオン ブログの記事]を参照してください。**



  [Windows ストア C\#]: /ja-jp/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows ストア C#"
  [Windows Phone]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /ja-jp/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /ja-jp/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /ja-jp/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started/
  [データの使用]: /ja-jp/documentation/articles/mobile-services-ios-get-started-data/
  [Azure の無料評価版サイト]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28
  [iOS のオフライン ToDo リスト]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
  [コンパニオン ブログの記事]: http://aka.ms/iosoffline
