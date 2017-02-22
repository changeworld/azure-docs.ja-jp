---
title: "Azure Active Directory B2C: Android アプリケーションから Web API を呼び出す | Microsoft Docs"
description: "この記事では、OAuth 2.0 ベアラー トークンを使用して Node.js Web API を呼び出す Android の &quot;To-Do List&quot; アプリを作成する方法について説明します。 Android アプリと Web API は、どちらも Azure Active Directory B2C を使用してユーザー ID を管理し、ユーザーを認証します。"
services: active-directory-b2c
documentationcenter: android
author: xerners
manager: mbaldwin
editor: 
ms.assetid: d00947c3-dcaa-4cb3-8c2e-d94e0746d8b2
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 01/31/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 3f89a0e06ea4596d38f7f7b5e52cea54c5e30780
ms.openlocfilehash: bb94ab8d794d1e836df57e1bc42df42825f2668d


---
# <a name="azure-ad-b2c-call-a-web-api-from-an-android-application"></a>Azure AD B2C: Android アプリケーションから Web API を呼び出す
> [!WARNING]
> Microsoft では、[こちら](https://github.com/Azure-Samples/active-directory-b2c-android-native-nodejs-webapi)で最新の Android コード サンプルを公開しています。  このサンプルでは、Azure AD B2C を使用する基本的なシナリオでの互換性がテスト済みであるサード パーティ製のライブラリを使用しています。  このライブラリでは、システム ブラウザーではなく組み込みの Web ビューを使用します。  Google は、組み込みの Web ビューでの Google アカウントによるサインインのサポートを 2017 年 4 月 20 日に終了することを[発表](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)しました。Google アカウントのサポートを希望する開発者は、この時点でライブラリを更新する必要があります。  

>Microsoft では、サード パーティ製のライブラリの修正プログラムは提供していません。また、これらのライブラリのレビューも実施していません。 問題や機能に関する要望は、ライブラリのオープン ソース プロジェクトにお送りください。 詳細については、[こちらの記事](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)をご覧ください。
>
>




<!--HONumber=Feb17_HO1-->


