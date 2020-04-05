---
title: Web API を呼び出す Web API を構築する - Microsoft ID プラットフォーム | Azure
description: ダウンストリーム Web API を呼び出す Web API を構築する方法 (概要) について説明します。
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 467ff2f789cc83bc5651d831838da0b5c922c839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701741"
---
# <a name="scenario-a-web-api-that-calls-web-apis"></a>シナリオ:Web API を呼び出す Web API

Web API を呼び出す Web API を構築するために知っておくべきことについて説明します。

## <a name="prerequisites"></a>前提条件

保護された Web API が Web API を呼び出すこのシナリオは、「Web API の保護」シナリオの上に構築されます。 この基本的なシナリオの詳細については、「[シナリオ: 保護された Web API](scenario-protected-web-api-overview.md)」を参照してください。

## <a name="overview"></a>概要

- Web、デスクトップ、モバイル、またはシングルページ アプリケーション クライアント (付属図に示されていない) は、保護された Web API を呼び出して、"Authorization" HTTP ヘッダーに JSON Web トークン (JWT) ベアラー トークンを提供します。
- 保護された Web API はトークンを検証し、Microsoft Authentication Library (MSAL) の `AcquireTokenOnBehalfOf` メソッドを使用して Azure Active Directory (Azure AD) から別のトークンを要求することで、保護された Web API がユーザーに代わって 2 つ目の Web API (ダウンストリーム Web API) を呼び出すことができるようにします。
- また、保護された Web API は、後で `AcquireTokenSilent` を呼び出して、同じユーザーに代わって他のダウンストリーム API のトークンを要求することもできます。 必要な場合は `AcquireTokenSilent` でトークンを更新します。

![Web API を呼び出す Web API の図](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細

API のアクセス許可に関連するアプリの登録部分は、従来と変わりません。 このアプリ構成には、OAuth 2.0 On-Behalf-Of フローを使用して、JWT ベアラー トークンをダウンストリーム API 用のトークンと交換することが含まれます。 このトークンはトークン キャッシュに追加され、Web API のコントローラーで使用できるようになります。また、ダウンストリーム API を呼び出すために、トークンをサイレントに取得できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリの登録](scenario-web-api-call-api-app-registration.md)
