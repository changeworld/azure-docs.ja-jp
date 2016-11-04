---
title: ユニバーサル Windows 8.1 アプリへの認証の追加 | Microsoft Docs
description: Mobile Services を使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて Windows ストア アプリのユーザーを認証する方法について説明します。
services: mobile-services
documentationcenter: windows
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# ユニバーサル Windows 8.1 アプリへの認証の追加
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> このトピックの Mobile Apps バージョンについては、「[Add authentication to your Windows App (Windows アプリに認証を追加する)](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md)」を参照してください。
> 
> 

このトピックでは、ユニバーサル Windows 8.1 アプリから Azure モバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

> [!NOTE]
> このチュートリアルでは、Windows ストア アプリおよび Windows Phone Store 8.1 アプリでユーザーを認証する方法について説明します。Windows Phone 8.0 アプリまたは Windows Phone Silverlight 8.1 アプリの場合、このバージョンの [Mobile Services アプリへの認証の追加](mobile-services-windows-phone-get-started-users.md)を参照してください。
> 
> 

## <a name="register"></a> アプリケーションを認証に登録し、Mobile Services を構成する
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

## <a name="permissions"></a> アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

> [!NOTE]
> Visual Studio ツールを使用してアプリをモバイル サービスに接続する場合、ツールはクライアント プラットフォームごとに 1 つずつ、**MobileServiceClient** 定義を合計 2 セット生成します。この機会に、`#if...#endif` でラップされた **MobileServiceClient** 定義を、両方のバージョンのアプリで使用される、ラップされていない 1 つの定義に統合することによって、生成されたコードを単純化することができます。[Azure クラシック ポータル]からクイック スタート アプリをダウンロードした場合、この操作は必要ありません。
> 
> 

## <a name="add-authentication"></a> アプリケーションに認証を追加する
[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

これで、信頼できる ID プロバイダーによって認証されたユーザーは、*TodoItem* テーブルにアクセスできます。ユーザー固有データのセキュリティを強化するには、承認を実装する必要もあります。これを行うには、特定のユーザーのユーザー ID を取得します。この ID を使用することで、特定のリソースに対してユーザーが有する必要のあるアクセス権のレベルを特定できます。

## <a name="tokens"></a>クライアント側で認証トークンを保存する
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始するたびに、クライアントは ID プロバイダーとモバイル サービスの両方にアクセスする必要があります。この方法は非効率であるだけなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。よって、Mobile Services から返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。

> [!NOTE]
> クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、Mobile Services が発行したトークンをキャッシュできます。このチュートリアルでは、サービスによって管理される認証を使用します。
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>次のステップ
次の[モバイル サービス ユーザーのサービス側の認証](mobile-services-javascript-backend-service-side-authorization.md)チュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

## 関連項目
* [強化されたユーザー機能](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/> サーバー スクリプトの中で **user.getIdentities()** 関数を呼び出すことによって、モバイル サービス内で ID プロバイダーが管理する追加のユーザー データを取得できます、
* [Mobile Servicesでの .NET の使用方法に関する概念リファレンス]<br/>Mobile Services で .NET クライアントを使用する方法について説明します。

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[モバイル サービスの使用]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md

[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Mobile Servicesでの .NET の使用方法に関する概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md


<!---HONumber=AcomDC_0727_2016-->