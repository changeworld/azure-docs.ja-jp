---
title: MSAL.js を Azure AD B2C とともに使用する
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を使用すると、アプリケーションは Azure AD B2C と連携し、セキュリティで保護された Web API を呼び出すためのトークンを取得できます。 これらの Web API には、Microsoft Graph、その他の Microsoft API、他の開発者の Web API、または自分の Web API が可能です。
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 1aa07a4135452ae4303ecfee410b840cc75efffb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729378"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>JavaScript 用 Microsoft Authentication Library を使用して Azure AD B2C と連携する

[JavaScript 用 Microsoft Authentication Library (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) を使用すると、JavaScript 開発者は [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure AD B2C) を使用して、ソーシャルおよびローカル ID でユーザーを認証できます。

ID 管理サービスとして Azure AD B2C を使用すると、顧客がアプリケーションを使用するときにサインアップ、サインイン、自分のプロファイルの管理を行う方法をカスタマイズして制御できます。 

Azure AD B2C では、認証プロセス中にアプリケーションで表示される UI をブランド化し、カスタマイズすることもできます。

## <a name="supported-app-types-and-scenarios"></a>サポートされているアプリの種類とシナリオ

MSAL.js を使用すると、[シングルページ アプリケーション](../../active-directory-b2c/application-types.md#single-page-applications)で、[PKCE による承認コード フロー](../../active-directory-b2c/authorization-code-flow.md)の付与を使用してユーザーを Azure AD B2C にサインインさせることができます。 MSAL.js と Azure AD B2C を使用する場合:

- ユーザーは、各自のソーシャル ID やローカル ID を使用して認証を行うことが **できます**。
- ユーザーは、Azure AD B2C で保護されたリソースへのアクセスの認可を受けることが **できます** (ただし、Azure AD で保護されたリソースにはアクセスできません)。
- ユーザーは、[委任されたアクセス許可](./v2-permissions-and-consent.md#permission-types)を使用して (MS Graph API など) Microsoft API のトークンを取得することは **できません**。
- 管理者特権を持つユーザーは、[委任されたアクセス許可](./v2-permissions-and-consent.md#permission-types)を使用して (MS Graph API など) Microsoft API のトークンを取得することが **できます**。

詳細については、[Azure AD B2C の操作](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/working-with-b2c.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

次の方法に関するチュートリアルを完了します。

- [Azure AD B2C を使用してシングルページ アプリケーションでユーザーをサインインさせる](../../active-directory-b2c/configure-authentication-sample-spa-app.md)
- [Azure AD B2C で保護された Web API を呼び出す](../../active-directory-b2c/enable-authentication-web-api.md)