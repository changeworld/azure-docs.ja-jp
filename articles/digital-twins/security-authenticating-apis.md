---
title: Azure Digital Twins API の認証の理解 | Microsoft Docs
description: Azure Digital Twins を使用して API を接続および認証する
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: f85ab05e785ea559962490b43e75b196d1602159
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016218"
---
# <a name="connect-and-authenticate-to-apis"></a>API に接続および認証する

Azure Digital Twins は、Azure Active Directory (Azure AD) を使用してユーザーを認証し、アプリケーションを保護します。 Azure AD では、さまざまな最新アーキテクチャ用の認証がサポートされます。 すべての認証は、業界標準のプロトコルである OAuth 2.0 または OpenID Connect に基づいています。 また、開発者は Azure AD を使用して、シングル テナントと基幹業務 (LOB) アプリケーションを構築することができます。 また開発者は、Azure AD を使用してマルチ テナント アプリケーションを開発することもできます。

Azure AD の概要については、[基礎ページ](https://docs.microsoft.com/azure/active-directory/fundamentals/index)にアクセスしてステップ バイ ステップ ガイド、概念、およびクイック スタートを確認してください。

アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。 詳しい手順とスクリーンショットについては、[こちらのクイック スタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)をご覧ください。

Azure AD でサポートされる [5 つの主要なアプリケーション シナリオ](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)は、次のとおりです。

* シングル ページ アプリケーション (SPA): ユーザーは、Azure AD によって保護されたシングル ページ アプリケーションにサインインする必要があります。
* Web ブラウザー対 Web アプリケーション: ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります。
* ネイティブ アプリケーション対 Web API: スマートフォン、タブレット、または PC で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するために、ユーザーを認証する必要があります。
* Web アプリケーション対 Web API: Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。
* デーモンまたはサーバー アプリケーション対 Web API: Web UI を備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。

Microsoft Azure 認証ライブラリでは、さまざまな方法で Active Directory トークンを取得できます。 ライブラリとコード サンプルについて詳しくは、[こちらの記事](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)をご覧ください。

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>中間層 Web API から Digital Twins を呼び出す

開発者が Digital Twins ソリューションを設計する際、通常は中間層アプリケーションか API を作成します。 その後、アプリまたは API によって、ダウンストリームの Digital Twins API を呼び出します。 ユーザーはまず中間層アプリケーションに対して認証を行い、代理のトークン フローを使用して、ダウンストリームが呼び出されます。 代理フローを統合する方法については、[こちらのページ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)をご覧ください。 なお、[こちらのページ](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)でコード サンプルも参照できます。


## <a name="test-with-the-postman-client"></a>Postman クライアントでのテストの実行

Digital Twins API を起動して実行するには、API 環境として Postman などのクライアントを使用できます。 Postman は複雑な HTTP 要求をすばやく作成するのに役立ちます。 以下の手順は、Postman UI 内で Digital Twins を呼び出すために必要な Azure AD トークンの取得方法を示したものです。


1. https://www.getpostman.com/ に移動してアプリをダウンロードします。
1. [こちらのクイック スタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)の手順に従って、Azure AD アプリケーションを作成します。 なお、既存の登録を再利用することもできます。 
1. **[必要なアクセス許可]** に "Azure Digital Twins" と入力して **[委任されたアクセス許可]** を選択します。 その後、**[アクセス許可の付与]** を選択します。
1. アプリケーション マニフェストを開き、**oauth2AllowImplicitFlow** を true に設定します。
1. 応答 URL を [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) に構成します。
1. **[承認] タブ**を選択し、**[OAuth 2.0]** を選択して **[Get New Access Token]\(新しいアクセス トークンの取得\)** を選択します。

    |**フィールド**  |**値** |
    |---------|---------|
    | [付与タイプ] | 暗黙 |
    | コールバック URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 認証 URL | https://login.microsoftonline.com/<Your Azure AD Tenant e.g. Contoso>.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0 |
    | クライアント ID | 手順 2 で作成または別の用途のために作り変えた Azure AD アプリのアプリケーション ID を使用します。 |
    | Scope (スコープ) | 空白のままにします。 |
    | State (状態) | 空白のままにします。 |
    | クライアント認証 | 基本 Auth ヘッダーとして送信します。 |

1. **[Request Token]\(要求トークン\)** を選択します。

    >[!NOTE]
    >「OAuth 2 couldn’t be completed」(OAuth 2 を完了できませんでした) というエラー メッセージを受け取った場合は、次の操作を試してください。
    > * Postman を閉じて再起動し、もう一度実行する。
   
1. 下へスクロールし、**[Use Token]\(トークンの使用\)** を選択します。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
