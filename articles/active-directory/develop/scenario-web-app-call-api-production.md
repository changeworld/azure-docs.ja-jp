---
title: Web API を呼び出す Web アプリを運用環境に移行する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web アプリを運用環境に移行する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cf32274a49cb1b790e9d872efe36f2e1cb188d1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675942"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web アプリ: 運用環境への移行

Web API を呼び出すためにトークンを取得する方法について説明しました。ここでは、アプリケーションを運用環境に移行する際に考慮すべき点をいくつか示します。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

ASP.NET Core Web アプリの詳細なプログレッシブ チュートリアルを試して、さらに詳しく学習します。 このチュートリアルでは以下を行います。

- 複数の対象ユーザーや各国のクラウドにユーザーをサインインさせたり、ソーシャル ID を使用してユーザーをサインインさせたりする方法を示します。
- Microsoft Graph を呼び出します。
- 複数の Microsoft API を呼び出します。
- 増分同意を処理します。
- ユーザー自身の Web API を呼び出します。

[ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
