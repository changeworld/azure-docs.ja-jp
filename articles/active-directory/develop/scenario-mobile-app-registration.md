---
title: Web API を呼び出すモバイル アプリを構成する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法 (アプリのコード構成) について説明します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c5160d88e390f9bfe905dfee0684983a7a49365
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919887"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Web API を呼び出すモバイル アプリ - アプリの登録

この記事では、モバイル アプリケーションを作成するためのアプリの登録手順が記載されています。

## <a name="supported-accounts-types"></a>サポートされているアカウントの種類

モバイル アプリケーションでサポートされるアカウントの種類は、有効にするエクスペリエンスと使用するフローによって異なります。

### <a name="audience-for-interactive-token-acquisition"></a>対話型トークン取得の対象ユーザー

ほとんどのモバイル アプリケーションでは、対話型認証が使用されています。 その場合は、任意の[アカウントの種類](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)からユーザーをサインインさせることができます。

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>統合認証の対象ユーザー、ユーザー名/パスワード、B2C

- 統合 Windows 認証 (UWP アプリで可能) またはユーザー名/パスワードを使用する場合は、アプリケーションが独自のテナント (LOB 開発者) 内のユーザー、または Azure Active Directory 組織 (ISV シナリオ) にサインインする必要があります。 これらの認証フローは、Microsoft の個人用アカウントではサポートされていません。
- B2C 機関とポリシーを渡すソーシャル ID を使用してユーザーにサインインさせる場合は、対話型のユーザー名とパスワード認証のみを使用できます。 ユーザー名とパスワードは現在、Xamarin.iOS、Xamarin.Android、および UWP でのみサポートされています。

全体像については、「[シナリオとサポートされている認証フロー](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)」と「[シナリオとサポートされているプラットフォームと言語](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)」を参照してください。

## <a name="platform-configuration-and-redirect-uris"></a>プラットフォーム構成とリダイレクト URI  

### <a name="interactive-authentication"></a>対話型認証

対話型認証を使用してモバイル アプリをビルドするときに最も重要な登録手順は、リダイレクト URI です。 これは、[認証ブレードのプラットフォーム構成](https://aka.ms/MobileAppReg)で設定できます。

このエクスペリエンスにより、お使いのアプリで Microsoft Authenticator (および Android の Intune ポータル サイト) を介してシングル サインオン (SSO) できるようになり、デバイス管理ポリシーもサポートされます。

アプリ登録ポータルでは、iOS および Android アプリケーションの仲介型応答 URI を計算するのに役立つプレビュー エクスペリエンスがあることに注目してください。

1. アプリの登録で、 **[認証]** を選択し、 **[新しいエクスペリエンスを試す]** を選択します。
   ![イメージ](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **[プラットフォームの追加]** を選択します。
   ![イメージ](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. プラットフォームの一覧がサポートされている場合は、 **[iOS]** を選択します。
   ![イメージ](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. 要求されたらバンドル ID を入力し、 **[登録]** を押します。
   ![イメージ](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. リダイレクト URI が自動的に計算されます。
   ![画像](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

リダイレクト URI を手動で構成する場合は、アプリケーション マニフェストを介して行えます。 推奨フォーマットは以下のとおりです。

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (例: "msauth.com.yourcompany.appName://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android の署名のハッシュは、KeyTool コマンドを通じて、リリース キーまたはデバッグ キーを使用して生成できます。

### <a name="username-password"></a>ユーザー名とパスワード

アプリでユーザー名/パスワードのみを使用する場合は、アプリケーションのリダイレクト URI を登録する必要はありません。 実際には、このフローが Microsoft ID プラットフォームの v2.0 エンドポイントへのラウンド トリップを行い、アプリケーションは特定の URI にコールバックされません。 ただし、アプリケーションがパブリック クライアント アプリケーションであることを示す必要があります。 この構成を実現するには、アプリケーションの **[認証]** セクションに移動して、 **[詳細設定]** のサブセクションで、( **[既定のクライアントの種類]** の段落で) **[アプリケーションは、パブリック クライアントとして扱います]** の質問に対して **[はい]** を選択します。

## <a name="api-permissions"></a>API のアクセス許可

モバイル アプリケーションでは、サインインしたユーザーの代わりに API を呼び出せます。 お使いのアプリでは、委任されたアクセス許可 (スコープとも呼ばれる) を要求する必要があります。 これは、目的のエクスペリエンスに応じて、Azure portal を介して静的に実行することも、実行時に動的に実行することもできます。 アクセス許可を静的に登録すれば、管理者は簡単にアプリを承認できるため、この方法をお勧めします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コード構成](scenario-mobile-app-configuration.md)
