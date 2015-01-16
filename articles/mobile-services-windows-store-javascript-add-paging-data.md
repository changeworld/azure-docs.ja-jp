<properties pageTitle="データへのページングの追加 (JavaScript) - Azure モバイル サービス" metaKeywords="" description="ページングを使用して、Mobile Services から Windows ストア JavaScript アプリに返されるデータの量を管理する方法について説明します。" metaCanonical="http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="mobile-services" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />


# ページングを使用したモバイル サービス クエリの改善

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


このトピックでは、ページングを使用して、Azure Mobile Services から Windows ストア アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

>[WACOM.NOTE]モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。 

[WACOM.INCLUDE [mobile-services-javascript-paging](../includes/mobile-services-javascript-paging.md)]

## <a name="next-steps"> </a>次のステップ

これで、モバイル サービスのデータを操作するための基本について説明する一連のチュートリアルは終了です。次のモバイル サービスのトピックの詳細を確認することをお勧めします。

* [認証の使用]
  <br/>Windows アカウントを使用してアプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>アプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->

[次のステップ]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-windows-store-javascript-get-started-push/

[管理ポータル]: https://manage.windowsazure.com/
