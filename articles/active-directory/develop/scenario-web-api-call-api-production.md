---
title: Web API を呼び出す Web API (運用環境への移行) - Microsoft ID プラットフォーム
description: ダウンストリーム Web API を呼び出す Web API を構築する方法について説明します (運用環境への移行)。
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
ms.openlocfilehash: 7de892a0851e556c5046fe4b3691f39b42a9d237
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080085"
---
# <a name="web-api-that-calls-web-apis---move-to-production"></a>Web API を呼び出す Web API - 運用環境への移行

Web API を呼び出すトークンを取得したら、アプリを運用環境に移行することができます。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="learn-more"></a>詳細情報

独自の Web API から Web API を呼び出す方法の基本が分かったので、次のチュートリアルに興味があるかもしれません。このチュートリアルでは、Web API を呼び出す保護された Web API を構築するために使用されるコードについて説明しています。

| サンプル | プラットフォーム | 説明 |
|--------|----------|-------------|
| [active-directory-aspnetcore-webapi-tutorial-v2](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/tree/master/2.%20Web%20API%20now%20calls%20Microsoft%20Graph) | ASP.NET Core 2.2 Web API, Desktop (WPF) | ASP.NET Core 2.2 Web API が Microsoft Graph を呼び出し、それ自体は Microsoft ID プラットフォーム (v2.0) を使用して WPF アプリケーションから呼び出される |
