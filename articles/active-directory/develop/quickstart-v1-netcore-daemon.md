---
title: コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph API を呼び出す (v1.0) | Microsoft Docs
description: OAuth 2.0 を利用して Azure AD の保護 API を呼び出すために Azure AD と統合する .NET デーモン アプリケーションの構築方法を説明します。
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d79d448f10c0d404516371be19d561bb21c8c0c7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149605"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>クイック スタート:コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph API を呼び出す (v1.0) 

[Microsoft ID プラットフォーム](v2-overview.md)は、Azure Active Directory (Azure AD) 開発者プラットフォームの進化版です。 これにより、すべての Microsoft ID にサインインして、Microsoft API (Microsoft Graph) や開発者が構築した API を呼び出すためのトークンを取得するアプリケーションを開発者が構築できます。

[Microsoft Authentication Library (MSAL)](msal-overview.md) によって、セキュリティで保護された Web API にアクセスする目的で、開発者は Microsoft ID プラットフォーム エンドポイントからトークンを取得できます。 Active Directory Authentication Library (ADAL) は開発者向け Azure AD (v1.0) エンドポイントと統合されます。このエンドポイントでは、MSAL が Microsoft ID プラットフォーム (v2.0) エンドポイントと統合されます。

## <a name="next-steps"></a>次の手順

新しい .NET デーモン アプリケーションでは、Microsoft ID プラットフォーム (v2.0) と MSAL を使用してトークンを取得し、セキュリティで保護された Web API にアクセスすることをお勧めします:[クイック スタート:トークンを取得し、コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出します](quickstart-v2-netcore-daemon.md)。
