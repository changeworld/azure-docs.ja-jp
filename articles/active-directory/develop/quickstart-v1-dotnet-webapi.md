---
title: 認証と認可に Azure AD を使用する .NET Web API を構築する | Microsoft Docs
description: 認証と承認のために Azure AD と連携する .NET MVC Web API を構築する方法
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b080ce3869bcdbbde9724a33433e5f0659b37e4
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703832"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>クイック スタート:認証と認可のために Azure AD と連携する .NET Web API を構築する

[Microsoft ID プラットフォーム](v2-overview.md)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、すべての Microsoft ID にサインインして、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者が構築できます。

[Microsoft Authentication Library (MSAL)](msal-overview.md) によって、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントからトークンを取得できます。 Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。

新しい Web API では、Microsoft ID プラットフォーム (v2.0) と MSAL を使用してトークンを取得し、セキュリティで保護された Web API にアクセスすることをお勧めします:[クイック スタート: ASP.NET Web アプリへの "Microsoft でサインイン" の追加](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
