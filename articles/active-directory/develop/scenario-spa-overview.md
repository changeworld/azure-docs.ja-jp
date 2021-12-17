---
title: JavaScript シングルページ アプリのシナリオ
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームを使用してシングルページ アプリケーションを構築する方法 (シナリオの概要) について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2021
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 19692739814a4db3ef2f981461254de415c9bdcc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444176"
---
# <a name="scenario-single-page-application"></a>シナリオ:シングルページ アプリ

シングルページ アプリケーション (SPA) の構築に必要なすべてのことについて説明します。 Azure Static Web Apps に関する手順については、代わりに「[Azure Static Web Apps の認証と承認](../../static-web-apps/authentication-authorization.md)」を参照してください。

## <a name="getting-started"></a>作業の開始

初めてのアプリケーションをまだ作成していない場合は、次の JavaScript SPA クイック スタートを完了して作成してください:

[クイック スタート: シングルページ アプリケーション](./quickstart-v2-javascript-auth-code.md)

## <a name="overview"></a>概要

多くの最新の Web アプリケーションは、クライアント側のシングル ページ アプリケーションとして構築されています。 開発者は、JavaScript または SPA フレームワーク (Angular、Vue、React など) を使用してそれらを作成します。 これらのアプリケーションは Web ブラウザーで実行され、その認証には、従来のサーバー側 Web アプリケーションとは異なる特性があります。

Microsoft ID プラットフォームにより、シングルページ アプリケーションでユーザーをサインインさせ、バックエンド サービスまたは Web API にアクセスするトークンを取得するための **2 つ** のオプションが提供されます。

- [OAuth 2.0 認証コード フロー (PKCE あり)](./v2-oauth2-auth-code-flow.md)。 この認証コード フローでは、認証されたユーザーを表す **ID** トークンと保護されている API を呼び出すために必要な **アクセス** トークンの認証コードを交換することをアプリケーションに許可します。 

    コード交換の証明キー、または _PKCE_ は、承認コード インジェクション攻撃を防止するための承認コード フローの拡張機能です。 この IETF 標準は、[RFC 7636](https://datatracker.ietf.org/doc/html/rfc7636) に記載されているように、パブリック クライアントからのセキュリティで保護された OAuth 交換をインターセプトして有効にする承認コードを持つ脅威を軽減します。 また、ユーザーが操作しなくてもこれらのユーザーの代わりにリソースへの長期間アクセスを提供する **更新** トークンが返されます。 

    PKCE で承認コード フローを使用することは、ネイティブおよびブラウザーベースの JavaScript アプリだけでなく、他のすべての種類の OAuth クライアントに対して、より安全で **推奨** される承認方法です。

![シングルページ アプリケーション認証](./media/scenarios/spa-app-auth.svg)

- [OAuth 2.0 暗黙的フロー](./v2-oauth2-implicit-grant-flow.md)。 暗黙的な許可フローでは、**ID** と **アクセス** トークンを取得することがアプリケーションに許可されます。 認証コード フロートは異なり、暗黙的な許可フローでは **更新トークン** が返されません。

![シングルページ アプリケーション暗黙的](./media/scenarios/spa-app.svg)

この認証フローには、Electron や React-Native などのクロスプラットフォーム JavaScript フレームワークを使用するアプリケーション シナリオは含まれません。 それらでは、ネイティブ プラットフォームと対話するための追加の機能が必要になります。

## <a name="specifics"></a>詳細

アプリケーションでこのシナリオを有効にするには、次のことが必要です。

* Azure Active Directory (Azure AD) へのアプリケーションの登録。 登録手順は、暗黙的な許可フローと認証コード フローの間で異なります。
* アプリケーション ID などの登録済みアプリケーション プロパティを使用したアプリケーション構成。
* Microsoft Authentication Library for JavaScript (MSAL.js) を使用した、サインインしてトークンを取得するための認証フローの実行。

## <a name="recommended-reading"></a>推奨資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](scenario-spa-app-registration.md)」に進みます。
