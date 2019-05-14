---
title: ユーザーをサインインさせる Web アプリ (運用環境への移行) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリをビルドする方法について説明します (運用環境への移行)
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
ms.openlocfilehash: d41ad2518f885bbaa02dda3b01f0c02e9fc1d217
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080079"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>ユーザーをサインインさせる Web アプリ - 運用環境への移行

Web API を呼び出すトークンの取得方法が分かったので、次にそれを運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次の手順

### <a name="calling-web-apis-scenario"></a>Web API を呼び出すシナリオ

Web アプリはユーザーをサインインさせると、サインインしているユーザーの代わりに Web API を呼び出すことができます。 Web アプリから Web API を呼び出すことは、次のシナリオの目標です。

> [!div class="nextstepaction"]
> [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---web-app-tutorial"></a>詳細情報 - Web アプリのチュートリアル

ASP.NET Core チュートリアル: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) を使用してユーザーをサインインさせる他の方法について説明します。 これは、サインインを追加する方法など、Web アプリの運用環境対応コードを使用したプログレッシブ チュートリアルです。

<!--- Removed the diagram as it's already shown in the above link to GitHub

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
