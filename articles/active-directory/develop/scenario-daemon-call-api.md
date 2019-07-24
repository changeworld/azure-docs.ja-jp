---
title: Web API を呼び出すデーモン アプリ (Web API の呼び出し) - Microsoft ID プラットフォーム
description: Web API を呼び出すデーモン アプリを構築する方法について説明します (Web API の呼び出し)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: aff375f996126d9e8b64361fc0e5673c25d30c19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080287"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Web API を呼び出すデーモン アプリ - アプリからの Web API 呼び出し

デーモン アプリは、.NET デーモン アプリケーションから Web API を呼び出したり、事前に承認された複数の Web API を呼び出したりすことができます。

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>.NET デーモン アプリケーションからの Web API の呼び出し

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->

## <a name="calling-several-apis"></a>複数の API の呼び出し

デーモン アプリの場合、呼び出す Web API は事前に承認されている必要があります。 デーモン アプリでの増分同意はありません (ユーザー対話はありません)。 テナント管理者は、アプリケーションとすべての API アクセス許可に事前に同意する必要があります。 複数の API を呼び出す場合、`AcquireTokenForClient` を呼び出すたびに各リソースのトークンを取得する必要があります。 MSAL は、不要なサービスの呼び出しを回避するために、アプリケーションのトークン キャッシュを使用します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デーモン アプリ - 運用環境への移行](./scenario-daemon-production.md)