---
title: API の認証について - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins ツインを使用して API に接続し、認証を行う方法について説明します。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: has-adal-ref
ms.openlocfilehash: 63cc188b5bdabedf0902c02264c1a269ca8111af
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611808"
---
# <a name="connect-to-and-authenticate-with-apis"></a>API に接続して認証を行う

Azure Digital Twins は、Azure Active Directory (Azure AD) を使用してユーザーを認証し、アプリケーションを保護します。 Azure AD では、さまざまな最新アーキテクチャ用の認証がサポートされます。 すべての認証は、業界標準のプロトコルである OAuth 2.0 または OpenID Connect に基づいています。 また、開発者は Azure AD を使用して、シングル テナントと基幹業務 (LOB) アプリケーションを構築することができます。 また開発者は、Azure AD を使用して[マルチテナント アプリケーション](how-to-multitenant-applications.md)を開発することもできます。

Azure AD の概要については、[基礎ページ](https://docs.microsoft.com/azure/active-directory/fundamentals/)にアクセスしてステップ バイ ステップ ガイド、概念、およびクイック スタートを確認してください。

> [!TIP]
> [チュートリアル](tutorial-facilities-setup.md)に従って、Azure Digital Twins サンプル アプリをセットアップして実行します。

アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。 詳しい手順とスクリーンショットについては、[こちらのクイックスタート](../active-directory/develop/quickstart-register-app.md)をご覧ください。

Azure AD でサポートされる [5 つの主要なアプリケーション シナリオ](../active-directory/develop/v2-app-types.md)は、次のとおりです。

* シングル ページ アプリケーション (SPA): ユーザーは、Azure AD によって保護されたシングル ページ アプリケーションにサインインする必要があります。
* Web ブラウザー対 Web アプリケーション: ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります。
* ネイティブ アプリケーション対 Web API: スマートフォン、タブレット、または PC で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するために、ユーザーを認証する必要があります。
* Web アプリケーション対 Web API: Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。
* デーモンまたはサーバー アプリケーション対 Web API: Web UI を備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。

> [!IMPORTANT]
> Azure Digital Twins では、次の両方の認証ライブラリがサポートされています。
> * より新しい [Microsoft 認証ライブラリ (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory 認証ライブラリ (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>中間層 Web API から Digital Twins を呼び出す

開発者が Digital Twins ソリューションを設計する際、通常は中間層アプリケーションか API を作成します。 その後、アプリまたは API によって、ダウンストリームの Digital Twins API を呼び出します。 この標準の Web ソリューション アーキテクチャをサポートするには、まずユーザーが次の手順を実行する必要があります。

1. 中間層アプリケーションの認証を受けます

1. 認証中に OAuth 2.0 On-Behalf-Of トークンが取得されます

1. この取得されたトークンは、認証と、On-Behalf-Of フローを使用するさらにダウンストリームである API の呼び出しに使用されます

On-Behalf-Of フローを調整する方法については、[OAuth 2.0 On-Behalf-Of フロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)に関するページを参照してください。 また、コード サンプルについては、[ダウンストリーム Web API の呼び出し](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

OAuth 2.0 の暗黙的な許可フローを使用して Azure Digital Twins を構成およびテストするには、[Postman の構成](./how-to-configure-postman.md)に関するページを参照してください。

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
