---
title: "Azure AD v2 Android の概要 - 構成 | Microsoft Docs"
description: "Android アプリがアクセス トークンを取得し、Microsoft Graph API またはアクセス トークンを必要とする API を、Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b034bf99351c5b33c51d9a8401434160bddb15be
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---

## <a name="add-the-applications-registration-information-to-your-app"></a>アプリへのアプリケーション登録情報の追加

この手順では、アプリケーション ID をプロジェクトに追加する必要があります。

1.    `MainActivity` を開きます ([`app`]  >  [`java`]  >  [*`{host}.{namespace}`*] の順)。
2.    `final static String CLIENT_ID` で始まる行を以下に置き換えます。
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. [`app`]  >  [`manifests`] (マニフェスト)  >  [`AndroidManifest.xml`] の順に開きます。
4. ノード `manifest\application` に次のアクティビティを追加します。 これにより `BrowserTabActivity` が登録され、OS は認証の完了後、アプリケーションを再開することが可能になります。

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

### <a name="what-is-next"></a>次の手順

[テストおよび検証](active-directory-mobileanddesktopapp-android-test.md)

