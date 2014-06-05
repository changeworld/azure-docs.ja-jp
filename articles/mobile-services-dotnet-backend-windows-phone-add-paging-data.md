<properties pageTitle="データへのページングの追加 (Windows Phone) | モバイル デベロッパー センター" metaKeywords="" description="ページングを使用して、モバイル サービスから Windows Phone アプリケーションに返されるデータの量を管理する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="ページングを使用したモバイル サービス クエリの改善" authors="glenga" solutions="" manager="" editor="" />

# ページングを使用したモバイル サービス クエリの改善

> [AZURE.SELECTOR-LIST (プラットフォーム | バックエンド)]
- [(Windows ストア C# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md)
- [(Windows ストア C# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md)
- [(Windows ストア JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md)
- [(Windows ストア JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md)
- [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data)
- [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data)
- [(iOS | JavaScript)](mobile-services-ios-add-paging-data)
- [(Android | JavaScript)](mobile-services-android-add-paging-data)
- [(HTML | .NET)](mobile-services-html-add-paging-data)
- [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data)
- [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)


このトピックでは、ページングを使用して、Azure のモバイル サービスから Windows Phone アプリケーションに返されるデータの量を管理する方法について説明します。このチュートリアルでは、クライアントで **Take** および **Skip** クエリ メソッドを使用して、データの特定の "ページ" を要求します。

>[WACOM.NOTE]モバイル デバイス クライアントでデータがオーバーフローしないように、モバイル サービスでは、自動ページ制限を実装しています。既定では、1 つの応答で最大 50 項目に設定されます。ページ サイズを指定することで、1 つの応答で 1,000 項目まで明示的に要求できます。

このチュートリアルは、前の[データの使用]に関するチュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、少なくとも、データ操作シリーズの最初のチュートリアル (「[データの使用]」) を完了している必要があります。

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

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
[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[認証の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[プッシュ通知の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/


[管理ポータル]: https://manage.windowsazure.com/

