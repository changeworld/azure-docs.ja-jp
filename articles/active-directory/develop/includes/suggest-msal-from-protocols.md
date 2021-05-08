---
title: MSAL を優先する | Azure
description: MSAL を使用するのが最善であることを示すファイルを含めてください。
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 11/17/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 7fd409b69ac89e38d6a27e3871117617f208777a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96511197"
---
この記事では、Azure AD からのトークンを要求するために、アプリケーションでプロトコルに対して直接プログラミングする方法について説明します。  可能な場合は、[トークンを取得してセキュリティで保護された Web API を呼び出す](..\authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)代わりに、サポートされている Microsoft 認証ライブラリ (MSAL) を使用することをお勧めします。  また、[MSAL を使用するサンプル アプリ](..\sample-v2-code.md)も参照してください。
