---
title: トークンを取得して Microsoft Graph を呼び出す (.NET Core Console) (v1.0) | Azure
description: OAuth 2.0 を使用して Azure AD と連携し、Azure AD によって保護された API を呼び出す .NET デーモン アプリケーションを構築します
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.openlocfilehash: bdcf0632110df23cbe91040f41535f51fb06ba44
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703798"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>クイック スタート:コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph を呼び出す (v1.0)

[Microsoft ID プラットフォーム](v2-overview.md)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、すべての Microsoft ID にサインインして、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者が構築できます。

[Microsoft Authentication Library (MSAL)](msal-overview.md) によって、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントからトークンを取得できます。 Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。

## <a name="next-steps"></a>次のステップ

新しい .NET デーモン アプリケーションでは、Microsoft ID プラットフォーム (v2.0) と MSAL を使用してトークンを取得し、セキュリティで保護された Web API にアクセスすることをお勧めします:[クイック スタート: トークンを取得し、コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出します](quickstart-v2-netcore-daemon.md)。
