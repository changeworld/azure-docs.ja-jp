---
title: "Azure Mobile Apps を使用した iOS での認証の追加"
description: "Azure Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。"
services: app-service\mobile
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4df1f27e5cefe9813fdc79b96d42e5c5d033901c


---
# <a name="add-authentication-to-your-ios-app"></a>iOS アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

このチュートリアルでは、サポートされている ID プロバイダーを使用して、 [iOS のクイック スタート] プロジェクトに認証を追加します。 最初に、このチュートリアルの基になっている [iOS のクイック スタート] チュートリアルを完了しておく必要があります。

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>アプリケーションを認証に登録し、App Service を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode で、**[Run]** をクリックしてアプリケーションを開始します。 認証されないユーザーとしてアプリがバックエンドにアクセスしようとしても、*TodoItem* テーブルで認証が要求されるために例外が発生します。

## <a name="a-nameadd-authenticationaadd-authentication-to-app"></a><a name="add-authentication"></a>アプリケーションに認証を追加する
[!INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]

<!-- URLs. -->

[iOS のクイック スタート]: app-service-mobile-ios-get-started.md

[Azure ポータル]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


