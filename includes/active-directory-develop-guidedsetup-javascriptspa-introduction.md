---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: cfbf4dc08ccea95f99216f6cc82c1ee56d65768a
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943353"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript Single Page Application (SPA) から Microsoft Graph API を呼び出す

このガイドでは、JavaScript Single Page Application (SPA) が個人アカウント、または職場および学校アカウントにサインインし、アクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする他の API を、Azure Active Directory v2 エンドポイントから呼び出す方法について説明します。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このガイドで生成されたサンプル アプリの動作](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API または Azure Active Directory v2 エンドポイントからトークンを受け取る Web API にクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript プレビュー用の Microsoft Authentication Library|

> [!NOTE]
> *msal.js* は、"*Azure Active Directory v2 エンドポイント*" を対象とします。これにより、個人アカウント、または学校および職場アカウントでサインインして、トークンを取得することができます。 "*Azure Active Directory v2 エンドポイント*" には、[いくつかの制限](..\articles\active-directory\develop\active-directory-v2-limitations.md)があります。 学校および職場アカウントのみを使用する場合は、*adal.js* と "*V1 エンドポイント*" を使用します。 v1 エンドポイントと v2 のエンドポイントの相違点を理解するには、[v1 と v2 の比較](..\articles\active-directory\develop\active-directory-v2-compare.md)に関するページを参照してください。

<!--end-collapse-->
