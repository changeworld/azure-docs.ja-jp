---
title: Web API を呼び出す Web アプリ (概要) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリ を構築する方法 (概要) について説明します
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
ms.openlocfilehash: ce45f11a697b72ebdd0fe01166a70e7b47aa8e9f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080291"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>シナリオ: Web API を呼び出す Web アプリ

Microsoft ID プラットフォームでユーザーをサインインさせ、サインインしたユーザーに代わって Web API を呼び出す Web アプリを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

このシナリオでは、以下のシナリオが確認済みであることを前提としています。

> [!div class="nextstepaction"]
> [ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>概要

お使いの Web アプリに認証を追加することで、ユーザーをサインインさせ、サインインしたユーザーに代わって Web API を呼び出すことができます。

![Web API を呼び出す Web アプリ](./media/scenario-webapp/web-app.svg)

Web API を呼び出す Web アプリ:

- 機密クライアント アプリケーションです。
- そのため、Azure AD にシークレット (アプリケーション パスワードまたは証明書) が登録されています。 このシークレットは、トークンを取得するために Azure AD への呼び出し中に渡されます

## <a name="specifics"></a>詳細

> [!NOTE]
> MSAL ライブラリは Web アプリの保護に関するものであるため、Web アプリにサインインを追加しても MSAL ライブラリは使用されません。 ライブラリの保護は、ミドルウェアと呼ばれるライブラリによって実現します。 これは、これまでに説明したシナリオ「[ユーザーを Web アプリにサインインさせる](scenario-web-app-sign-user-overview.md)」のオブジェクトです
>
> Web アプリから Web API を呼び出す場合、これらの Web API のアクセス トークンを取得する必要があります。 MSAL ライブラリを使用してこれらのトークンを取得できます。

このため、このシナリオにおける開発者のエンド ツー エンド エクスペリエンスには、固有の側面があります。

- [アプリケーションの登録](scenario-web-app-call-api-app-registration.md)時には、Azure AD と共有する必要がある 1 つまたは複数 (お使いのアプリを複数の場所にデプロイする場合) の応答 URI、シークレットまたは証明書を提供する必要があります。
- [アプリケーションの構成](scenario-web-app-call-api-app-configuration.md)で、アプリケーションの登録時に Azure AD と共有されるようにクライアントの資格情報を提供する必要があります

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-web-app-call-api-app-registration.md)
