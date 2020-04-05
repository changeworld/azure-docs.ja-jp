---
title: Web API を呼び出す Web アプリを運用環境に移行する - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを運用環境に移行する方法について説明します。
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
ms.openlocfilehash: b1307df8f6dfb0457719b13c9e5cd0bf28660caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758949"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web アプリ: 運用環境に移行する

Web API を呼び出すトークンの取得方法が分かったところで、次に、運用環境に移行する方法を学びます。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

ASP.NET Core Web アプリの詳細なプログレッシブ チュートリアルを試して、さらに詳しく学習します。 このチュートリアルでは以下を行います。

- 複数の対象ユーザーや各国のクラウドにユーザーをサインインさせたり、ソーシャル ID を使用してユーザーをサインインさせたりする方法を示します。
- Microsoft Graph を呼び出します。
- 複数の Microsoft API を呼び出します。
- 増分同意を処理します。
- ユーザー自身の Web API を呼び出します。

> [!div class="nextstepaction"]
> [ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
