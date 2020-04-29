---
title: シングルページ アプリを運用環境に移行する - Microsoft ID プラットフォーム | Azure
description: シングルページ アプリケーションを構築する方法について説明します (運用環境への移行)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 04a900c98311f4e7dcccbfca93ea41e212308759
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882099"
---
# <a name="single-page-application-move-to-production"></a>シングルページ アプリケーション：運用環境に移行する

Web API を呼び出すトークンの取得方法が分かったところで、次に、運用環境に移行する方法を学びます。

## <a name="improve-your-app"></a>アプリの改善

[ロギングを有効にして](msal-logging.md)、アプリの制作準備を整えます。

## <a name="test-your-integration"></a>統合のテスト

[Microsoft ID プラットフォームの統合チェックリスト](identity-platform-integration-checklist.md)に従って、統合をテストします。

## <a name="next-steps"></a>次のステップ

MSAL.js を使用してユーザーをサインインし、Microsoft Graph API を呼び出すアクセストークンを取得する方法のコードを説明するクイックスタートサンプルの詳細を次に示します。

> [!div class="nextstepaction"]
> [JavaScript SPA チュートリアル](./tutorial-v2-javascript-spa.md)

MSAL.js を使用して独自のバックエンド Web API のトークンを取得する方法を示すサンプルを次に示します：

> [!div class="nextstepaction"]
> [ASP.NET バックエンドを使用する SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2)

MSAL を使用して Azure Active Directory B2C (Azure AD B2C) に登録されているアプリのユーザーにサインインする方法を示すサンプルです。

> [!div class="nextstepaction"]
> [Azure AD B2C を使用する SPA](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)
