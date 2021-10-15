---
title: パブリック クライアントを MSAL に移行するための一般的な手順
description: ADAL から MSAL への移行に関して、すべてのパブリック クライアント アプリに対して実行する必要がある一般的な手順について説明するインクルード ファイル。
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 09/08/2021
ms.author: sahmalik
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 419b038f89df0d7fb5185fe2ef8ae43c76db7f7f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837801"
---
コードを更新するための以下の手順は、すべての機密クライアント シナリオに適用されます。

1. ソース コードに MSAL.NET 名前空間を追加します: `using Microsoft.Identity.Client;`。
2. `AuthenticationContext` をインスタンス化する代わりに、`PublicClientApplicationBuilder.Create` を使用して `IPublicClientApplication` をインスタンス化します。
3. MSAL.NET は、`resourceId` 文字列ではなくスコープを使用します。 ADAL.NET を使用するアプリケーションは事前認証されているので、常に次のスコープを使用できます: `new string[] { $"{resourceId}/.default" }`。
4. `AuthenticationContext.AcquireTokenAsync` への呼び出しを `IPublicClientApplication.AcquireTokenXXX` への呼び出しで置き換えます。*XXX* は、シナリオによって異なります。