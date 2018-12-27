---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7ff04789a4ba5e5a689b3d3815852bc0fbcdc6a7
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988350"
---
## <a name="register-your-application"></a>アプリケーションの登録

次の 2 つのセクションで説明する方法のいずれかを使用して、アプリケーションを登録できます。

### <a name="option-1-express"></a>オプション 1: 簡易

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure)に移動します。
2. **[アプリケーション名]** に、アプリケーションの名前を入力します。
3. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオンになっていることを確認し、**[作成]** を選択します。
4. 手順に従ってアプリケーション ID を取得し、それをコードに貼り付けます。

### <a name="option-2-advanced"></a>オプション 2: 詳細

1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動します。
2. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。
3. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。
4. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。
5. **[app]** > **[java]** > **[{host}.{namespace}]** で、`MainActivity` を開きます。 
6. *[Enter the application Id here]* をアプリケーション/クライアント ID に置き換えます。

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. **[app]** > **[manifests]** で、*AndroidManifest.xml* ファイルを開きます。
8. `manifest\application` で、次のアクティビティを追加します。 認証の完了後に Microsoft がアプリケーションにコールバックできるようにする `BrowserTabActivity` アクティビティ:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
<!-- Workaround for Docs conversion bug -->
9. `BrowserTabActivity` で、`[Enter the application Id here]` をアプリケーション/クライアント ID に置き換えます。
