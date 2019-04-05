---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a6fcbc0e8adac75f17d7379ff512ba650d0bb118
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203312"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript シングル ページ アプリケーション (SPA) から Microsoft Graph API を呼び出す

このガイドでは、JavaScript シングル ページ アプリケーション (SPA) が個人アカウント、または職場および学校アカウントにサインインし、アクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2.0 エンドポイントから呼び出す方法について説明します。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro-updated.png)

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API または Azure Active Directory v2.0 エンドポイントからトークンを受け取る Web API にクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript プレビュー用の Microsoft Authentication Library|

> [!NOTE]
> *msal.js* は、*Azure Active Directory v2.0 エンドポイント*を対象とします。これにより、個人アカウント、または学校および職場アカウントでサインインして、トークンを取得することができます。 *Azure Active Directory v2.0 エンドポイント*には、[いくつかの制限](../articles/active-directory/develop/active-directory-v2-limitations.md)があります。
> v1.0 エンドポイントと v2.0 のエンドポイントの相違点を理解するには、[エンドポイントの比較ガイド](../articles/active-directory/develop/azure-ad-endpoint-comparison.md)を参照してください。

<!--end-collapse-->
