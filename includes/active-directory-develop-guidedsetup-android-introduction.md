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
ms.openlocfilehash: 23b7ca44b72b8840579f369954f41f554d4c8852
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36943421"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Android アプリからユーザーにサインインし、Microsoft Graph API を呼び出す

このガイドでは、ネイティブの Android アプリケーションがアクセス トークンを取得し、Azure Active Directory v2 エンドポイントからのアクセス トークンを必要とする Microsoft Graph API や他の API を 呼び出す方法について説明します。

このガイドを完了すると、アプリケーションは、個人用アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れることができるようになります。 その後、アプリケーションは Azure Active Directory v2 エンドポイントで保護されている API を呼び出すことができます。  

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作
![このサンプルのしくみ](media/active-directory-develop-guidedsetup-android-intro/android-intro.png)

このガイドで作成するサンプル アプリケーションは、Android アプリケーションを使用して、Azure Active Directory v2 エンドポイントからのトークンを受け入れる Web API (ここでは Microsoft Graph API) を照会するシナリオに基づいています。 このシナリオでは、アプリケーションは、Authorization ヘッダーを使用して HTTP 要求に取得したトークンを追加します。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) が処理します。

## <a name="prerequisites"></a>前提条件
* このガイド付きセットアップは Android Studio に焦点を合わせていますが、その他の Android アプリケーション開発環境にも適用できます。 
* Android SDK 21 以降が必要です (SDK 25 を推奨)。
* このリリースの Android 向け MSAL には、Google Chrome またはユーザー設定のタブを使用している Web ブラウザーが必要です。

> [!NOTE]
> Google Chrome は、Visual Studio Emulator for Android には含まれていません。 API 25 を使用するエミュレーター、または Google Chrome がインストールされている API 21 以降のイメージで、このコードをテストすることをお勧めします。

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>保護された Web API にアクセスするためのトークン取得処理

ユーザーが認証されると、サンプル アプリケーションは、Microsoft Graph API または Azure Active Directory v2 で保護された Web API の照会に使用できるアクセス トークンを受け取ります。

Microsoft Graph などの API では、特定のリソースへのアクセスを許可するアクセス トークンが必要になります。 たとえば、ユーザーのプロファイルの読み取り、ユーザーの予定表へのアクセス、電子メールの送信などにアクセス トークンが必要です。 アプリケーションでは、MSAL を使用してアクセス トークンを要求し、API スコープを指定することによってこれらのリソースにアクセスできます。 このアクセス トークンは、保護されたリソースに対するすべての呼び出しで HTTP Authorization ヘッダーに追加されます。 

アクセス トークンのキャッシュと更新は MSAL が管理するため、アプリケーションが管理する必要はありません。

## <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)|Microsoft Authentication Library (MSAL)|
