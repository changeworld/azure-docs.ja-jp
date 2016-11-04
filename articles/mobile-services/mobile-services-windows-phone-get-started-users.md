---
title: 認証の使用 (Windows Phone) | Microsoft Docs
description: Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows Phone アプリのユーザーを認証する方法について説明します。
services: mobile-services
documentationcenter: windows
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Mobile Services アプリへの認証の追加
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

## 概要
このトピックでは、アプリケーションから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、Mobile Services でサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

次のビデオで、Nick Harris によるこのチュートリアルのデモをご覧いただけます。

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Phone-Authenticate-and-Authorize-users-with-Server-Scripts-in-Windows-Azure-Mobile-Services/player]
> 
> 

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。また、最初にチュートリアル「[既存のアプリケーションへの Mobile Services の追加]」を完了しておく必要があります。

> [!NOTE]
> このチュートリアルでは、さまざまな ID プロバイダーを使用した、モバイル サービスによって管理される認証フローについて説明します。この方法は構成が容易で、複数のプロバイダーをサポートしています。クライアントによって管理される認証を使用することにより、アプリは ID プロバイダーが保持する追加のユーザー データにアクセスできます。サーバー スクリプトで **user.getIdentities()** 関数を呼び出せば、モバイル サービス内にある同じユーザー データを取得できます。詳細については、[この投稿](http://go.microsoft.com/fwlink/p/?LinkId=506605)を参照してください。
> 
> 

## <a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

&nbsp;&nbsp;3.Visual Studio で、チュートリアル「[既存のアプリケーションへの Mobile Services の追加](mobile-services-windows-phone-get-started-data.md)」を実行したときに作成したプロジェクトを開きます。

&nbsp;&nbsp;4.F5 キーを押して、このクイック スタート ベースのアプリケーションを実行します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、*TodoItem* テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
[!INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>クライアントに認証トークンを保存する
[!INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>次のステップ
次の[モバイル サービス ユーザーのサービス側の認証](mobile-services-javascript-backend-service-side-authorization.md)チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[既存のアプリケーションへの Mobile Services の追加]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

<!---HONumber=AcomDC_0727_2016-->