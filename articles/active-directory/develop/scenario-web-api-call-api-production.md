---
title: Web API の呼び出す Web API を運用環境へ移行する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web API を運用環境に移行する方法を学びます。
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
ms.openlocfilehash: 1426ad250e18d0132e116162a374120dda2e1200
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044142"
---
# <a name="a-web-api-that-calls-web-apis-move-to-production"></a>Web API を呼び出す Web API:運用環境に移行する

Web API を呼び出すトークンを取得したら、アプリを運用環境に移行することができます。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>詳細情報

独自の Web API から Web API を呼び出す方法の基本を確認したので、次のチュートリアルをご検討ください。このチュートリアルでは、Web API を呼び出す保護された Web API を構築するために使用されるコードについて説明しています。

| サンプル | プラットフォーム | [説明] |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API が Microsoft Graph を呼び出します。これは、Microsoft ID プラットフォーム (v2.0) を使用して WPF アプリケーションから呼び出します。 |
