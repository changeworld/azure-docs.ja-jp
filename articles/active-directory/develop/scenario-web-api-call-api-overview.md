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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b357def86b77d4bbb294e2253dacfbd129998ec
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965128"
---
# <a name="scenario-web-api-that-calls-web-apis"></a>シナリオ: Web API を呼び出す Web API

Web API を呼び出す Web API の構築に必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

このシナリオは、Web API を呼び出す保護された Web API で、「Web API の保護」シナリオの上に構築されます。 この基本的なシナリオの詳細については、まず[保護された Web API - シナリオ](scenario-protected-web-api-overview.md)に関するページを参照してください。

## <a name="overview"></a>概要

- 次の図に示されていないクライアント (Web、デスクトップ、モバイル、またはシングルページ アプリケーション) は、保護された Web API を呼び出し、「Authorization」 HTPP ヘッダーに JWT ベアラー トークンを提供します。
- 保護された Web API では、トークンを検証し、MSALの `AcquireTokenOnBehalfOf` メソッドを使用して (Azure AD から) 別のトークンを要求し、それ自体がユーザーに代わって 2 つ目の Web API (ダウンストリーム Web API と呼ばれる) を呼び出せるようにします。
- 保護された Web API は、このトークンを使用してダウン ストリーム API を呼び出します。 また、後で `AcquireTokenSilent` を呼び出して、他のダウンストリーム API のトークンを要求することもできます (ただし、この場合も同じユーザーに代わって)。 必要な場合は `AcquireTokenSilent` でトークンを更新します。

![Web API を呼び出す Web API](media/scenarios/web-api.svg)

## <a name="specifics"></a>詳細

API のアクセス許可に関連するアプリの登録の部分は、従来と変わりません。 このアプリケーション構成では、OAuth 2.0 on-behalf-of フ ローを使用して、JWT ベアラー トークンをダウンストリーム API 用のトークンと交換します。 このトークンはトークン キャッシュに追加され、Web API のコントローラーで使用できるようになります。また、ダウンストリーム API を呼び出すために、サイレントにトークンを取得できます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-web-api-call-api-app-registration.md)
