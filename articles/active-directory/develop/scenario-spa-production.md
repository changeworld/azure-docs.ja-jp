---
title: シングルページ アプリを運用環境に移行する
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーションを構築する方法について説明します (運用環境への移行)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4f4821dc48650c14b6d9736a7238dc44c500c259
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357555"
---
# <a name="single-page-application-move-to-production"></a>シングルページ アプリケーション：運用環境に移行する

Web API を呼び出すためにトークンを取得する方法について説明しました。ここでは、アプリケーションを運用環境に移行する際に考慮すべき点をいくつか示します。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="deploy-your-app"></a>アプリのデプロイ

Azure Storage サービスと Azure App Service でそれぞれ SPA プロジェクトおよび Web API プロジェクトをデプロイする方法については、[デプロイのサンプル](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter3)を確認してください。

## <a name="code-samples"></a>コード サンプル

これらのコード サンプルは、シングルページ アプリのいくつかの重要な操作を示しています。
- [ASP.NET バックエンドを使用する SPA](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi): **MSAL.js** を使用して独自のバックエンド Web API (ASP.NET Core) 用のトークンを取得する方法。

- [Node.js Web API (Azure AD)](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2): **passport-azure-ad** を使用してバックエンド Web API (Node.js) 用のアクセス トークンを検証する方法。

- [Azure AD B2C を使用する SPA](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa): **MSAL.js** を使用して、**Azure Active Directory B2C** (Azure AD B2C) に登録されているアプリにユーザーをサインインさせる方法。

- [Node.js Web API (Azure AD B2C)](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi): **passport-azure-ad** を使用して、**Azure Active Directory B2C** (Azure AD B2C) に登録されているアプリ用のアクセス トークンを検証する方法。

## <a name="next-steps"></a>次のステップ

- [JavaScript SPA チュートリアル](./tutorial-v2-javascript-auth-code.md): ユーザーをサインインさせ、**MSAL.js** を使用して **Microsoft Graph API** を呼び出すためのアクセス トークンを取得する方法について詳細を確認します。