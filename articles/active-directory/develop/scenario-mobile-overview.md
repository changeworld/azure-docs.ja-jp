---
title: Web API を呼び出すモバイル アプリ - 概要 | Microsoft ID プラットフォーム
description: Web API を呼び出すモバイル アプリを構築する方法 (概要) について説明します
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb0acd1534bab11eb57a7aa0e695f192b5999ed2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080317"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>シナリオ: Web API を呼び出すモバイル アプリケーション

Web API を呼び出すモバイル アプリを構築するために必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用の開始

最初のモバイル アプリケーションを作成する場合は、クイック スタートをお試しください。

> [!div class="nextstepaction"]
> [クイック スタート:Android アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-android.md)
>
> [クイック スタート:iOS アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-ios.md)
>
> [クイック スタート:Xamarin iOS および Android アプリからトークンを取得し、Microsoft Graph API を呼び出す](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>概要

モバイル アプリを構築するときは、パーソナライズされた、シームレスなエンド ユーザー エクスペリエンスが必要不可欠になります。  Microsoft ID プラットフォームを使用すれば、モバイル開発者は、iOS および Android のユーザーに対してまさにこれを行うことができます。 お使いのアプリケーションで Azure AD、個人の Microsoft アカウント、Azure AD B2C ユーザーをサインインさせて、そのユーザーの代わりに Web API を呼び出すためのトークンを取得できます。 これらのフローを実装するため、業界標準の [OAuth2.0 承認コード フロー](v2-oauth2-auth-code-flow.md)を実装する Microsoft 認証ライブラリ (MSAL) を使用します。

![デーモン アプリ](./media/scenarios/mobile-app.svg)

モバイル アプリに関する考慮事項:

- ***ユーザー エクスペリエンスが重要***:ユーザーが、サインイン要求の前に該当のアプリの値を確認して、必要なアクセス許可のみを要求できるようにします。
- ***すべてのユーザー構成をサポート***:多くのモバイル ビジネス ユーザーに、条件付きアクセスおよびデバイス コンプライアンスのポリシーが適用されています。 必ずこれらの重要なシナリオをサポートしてください。
- ***シングル サインオン (SSO) の実装***:MSAL と Microsoft ID プラットフォームにより、デバイスのブラウザーまたは Microsoft Authenticator (および Android の Intune ポータル サイト) を介したシングル サインオンを容易に実現できます。

## <a name="specifics"></a>詳細

Microsoft ID プラットフォームにモバイル アプリを構築する場合、エンド ツー エンド エクスペリエンスには考慮事項がいくつかあります。

- プラットフォームによっては、最初のサインインで対話なしのサインインはできない可能性があります。 iOS では、たとえば、Microsoft Authenticator (および Android の Intune ポータル サイト) を介して初めて SSO を行うときに、アプリでユーザーの対話を表示する必要があります。
- iOS および Android の MSAL では、ユーザーをサインインさせるために、(お使いのアプリの上に表示される場合がある) 外部ブラウザーを使用することがあります。 これは、代わりにアプリ内の WebViews を使用するようにカスタマイズできます。
- モバイル アプリケーションでシークレットを使用しないでください。すべてのユーザーがアクセスできるようになります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-mobile-app-registration.md)
