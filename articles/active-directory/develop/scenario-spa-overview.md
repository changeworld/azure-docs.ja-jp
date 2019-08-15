---
title: JavaScript シングルページ アプリケーションのシナリオの概要 - Microsoft ID プラットフォーム
description: Microsoft ID プラットフォームを統合するシングルページ アプリケーションを構築する方法 (シナリオの概要) について説明します。
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852514"
---
# <a name="scenario-single-page-application"></a>シナリオ: シングルページ アプリ

シングルページ アプリケーション (SPA) の構築に必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用の開始

JavaScript SPA のクイック スタートに従って、最初のアプリケーションを作成できます。

> [!div class="nextstepaction"]
> [クイック スタート:シングルページ アプリケーション](./quickstart-v2-javascript.md)

## <a name="overview"></a>概要

最近の Web アプリケーションの多くは、JavaScript または SPA フレームワーク (Angular、Vue.js、React.js など) を使用して記述されたクライアント側のシングルページ アプリケーションとして構築されています。 これらのアプリケーションは、Web ブラウザーで実行され、従来のサーバー側 Web アプリケーションとは異なる認証特性を持ちます。 Microsoft ID プラットフォームにより、シングルページ アプリケーションでユーザーをサインインさせ、[OAuth 2.0 暗黙的フロー](./v2-oauth2-implicit-grant-flow.md)を使用してバックエンド サービスまたは Web API にアクセスするトークンを取得できます。 暗黙的フローにより、アプリケーションから、認証されたユーザーを表す ID トークンを取得し、保護された API の呼び出しに必要なトークンにもアクセスできます。

![シングルページ アプリケーション](./media/scenarios/spa-app.svg)

この認証フローには、Electron、React-Native などのクロスプラットフォーム JavaScript フレームワークを使用したアプリケーション シナリオは含まれません。 これらでは、ネイティブ プラットフォームとの対話にさらに機能が必要になるためです。

## <a name="specifics"></a>詳細

アプリケーションでこのシナリオを有効にするには、次の要素が必要です。

* Azure AD でのアプリケーションの登録には、暗黙的フローの有効化、およびトークンを返すリダイレクト URI の設定が含まれます。
* アプリケーション ID などの登録されたアプリケーション プロパティを使用したアプリケーション構成。
* MSAL ライブラリを使用して認証フローを実行し、サインインしてトークンを取得する。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリの登録](scenario-spa-app-registration.md)
