---
title: Web API を呼び出す Web アプリを登録する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web アプリを登録する方法を説明します
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
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962935"
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
