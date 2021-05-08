---
title: Web API を呼び出す Web API を運用環境に移行する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web API を運用環境に移行する方法を学びます。
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
ms.openlocfilehash: 0ab5baef925b7c8589dd7852b6ff8058d67ba745
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104675874"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web API:運用環境に移行する

Web API を呼び出すトークンを取得したら、アプリケーションを運用環境に移行する場合に考慮すべき点がいくつかあります。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次のステップ

独自の Web API から Web API を呼び出す方法の基本を確認したので、次のチュートリアルをご検討ください。このチュートリアルでは、Web API を呼び出す保護された Web API を構築するために使用されるコードについて説明しています。

| サンプル | プラットフォーム | 説明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) の第 1 章 | ASP.NET Core Web API, Desktop (WPF) | ASP.NET Core Web API が Microsoft Graph を呼び出します。これは、Microsoft ID プラットフォームを使用して WPF アプリケーションから呼び出します。 |
