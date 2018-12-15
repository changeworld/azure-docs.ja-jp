---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 2cb4895fc2f884d6da41b55faa91fbcb9e88f52f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978653"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript シングル ページ アプリケーション (SPA) から Microsoft Graph API を呼び出す

このガイドでは、JavaScript シングル ページ アプリケーション (SPA) が個人アカウント、または職場および学校アカウントにサインインし、アクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2.0 エンドポイントから呼び出す方法について説明します。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このガイドで生成されたサンプル アプリの動作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

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
