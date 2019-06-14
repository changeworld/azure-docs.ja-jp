---
title: Web API を呼び出す Web アプリ (アプリの登録) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを構築する方法について説明します (アプリの登録)
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
ms.openlocfilehash: 5becdc287f7cad28aa6c7c07ebc107586e9a2b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65080143"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Web API を呼び出す Web アプリ - アプリの登録

Web API を呼び出す Web アプリは、ユーザーをサインインさせる Web アプリと同じ登録を持ちます。 そのため、「[ユーザーをサインインさせる Web アプリ - アプリの登録](scenario-web-app-sign-user-app-registration.md)」の手順に従う必要があります

ただし、Web アプリは現在 Web API を呼び出すため、機密クライアント アプリケーションになります。 そのため、追加の登録が若干必要となります。Microsoft ID プラットフォームとシークレット (クライアントの資格情報) を共有する必要があります。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API のアクセス許可

Web アプリケーションは、サインインしたユーザーの代わりに API を呼び出せます。 委任されたアクセス許可を要求する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-web-app-call-api-app-configuration.md)
