---
title: Web API を呼び出す Web アプリ (運用環境への移行) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを構築する方法について説明します (運用環境への移行)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b326cdb7303ff392c6129e0f0fa460ffe088305
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080109"
---
# <a name="web-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出す Web アプリ - 運用環境への移行

Web API を呼び出すトークンの取得方法が分かったところで、次に運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次の手順

ASP.NET Core Web アプリの詳細なプログレッシブ チュートリアルをお試しになり、詳細をご確認ください。以下の内容が記載されています。

- 複数の対象ユーザー、各国のクラウドまたはソーシャル ID を持つユーザーをサインインさせる方法
- Microsoft Graph の呼び出し
- 複数の Microsoft API の呼び出し
- 増分同意の取り扱い
- お客様独自の Web API の呼び出し

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
