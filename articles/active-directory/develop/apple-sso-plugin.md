---
title: Apple デバイス用の Microsoft Enterprise SSO プラグイン
titleSuffix: Microsoft identity platform | Azure
description: iOS および macOS デバイス用の Microsoft の Azure Active Directory SSO プラグインについて説明します。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561082"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー)

>[!IMPORTANT]
> この機能[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

*Apple デバイス用の Microsoft Enterprise SSO プラグイン* では、Apple の [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 機能がサポートされているすべてのアプリケーションでの Azure Active Directory (Azure AD) アカウントに対するシングル サインオン (SSO) が提供されます。 Microsoft は Apple と密接に連携してこのプラグインを開発し、アプリケーションの使いやすさを向上させ、Apple と Microsoft が提供できる最適な保護を提供しています。

Enterprise SSO プラグインのこのパブリック プレビュー リリースは、iOS デバイスでのみ使用でき、特定の Microsoft アプリケーションで配布されます。

## <a name="features"></a>特徴

Apple デバイス用の Microsoft Enterprise SSO プラグインには、次のような利点があります。

- Apple の Enterprise Single Sign-On 機能をサポートするすべてのアプリケーションで、Azure AD アカウントに対する SSO が提供されます。
- Microsoft Authenticator に自動的に提供され、任意のモバイル デバイス管理 (MDM) ソリューションで有効にすることができます。

## <a name="requirements"></a>必要条件

Apple デバイス用の Microsoft Enterprise SSO プラグインを使用するには:

- iOS 13.0 以降がデバイスにインストールされている必要があります。
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューでは、このようなアプリケーションには [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)が含まれます。
- デバイスは、MDM に登録されている必要があります (たとえば、Microsoft Intune)。
- デバイスで Apple デバイス用の Microsoft Enterprise SSO プラグインを有効にするには、構成がデバイスにプッシュされる必要があります。 このセキュリティ制約は、Apple によって要求されます。

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>モバイル デバイス管理 (MDM) で SSO プラグインを有効にする

Apple デバイス用の Microsoft Enterprise SSO プラグインを有効にするには、MDM サービスを介してデバイスに信号が送信される必要があります。 Enterprise SSO プラグインは [Microsoft Authenticator アプリ](..//user-help/user-help-auth-app-overview.md)に組み込まれているため、MDM を使用して Microsoft Enterprise SSO プラグインを有効にするようにアプリを構成します。

Apple デバイス用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

- **[種類]** :リダイレクト
- **拡張機能 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **チーム ID**: (iOS ではこのフィールドは必要ありません)
- **URL**:
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>追加の構成オプション
さらに構成オプションを追加して、SSO 機能を他のアプリに拡張することができます。

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>MSAL を使用しないアプリで SSO を有効にする

SSO プラグインを使用すると、Microsoft 認証ライブラリ (MSAL) などの Microsoft SDK を使用して開発されていないアプリケーションでも、シングル サインオンに参加させることができます。

SSO プラグインは、Microsoft Authenticator アプリをダウンロードし、デバイスを組織に登録したデバイスによって自動的にインストールされます。 組織では、多要素認証、パスワードなしの認証、条件付きアクセスなどのシナリオで Authenticator アプリを既に使用している可能性があります。 任意の MDM プロバイダーを使用するアプリケーションに対して有効にすることができますが、Microsoft では、Intune の Microsoft Endpoint Manager 内で簡単に構成できるようにしています。 許可リストは、Authenticator アプリによってインストールされた SSO プラグインを使用するようにこれらのアプリケーションを構成するために使用されます。

ネイティブの Apple ネットワーク テクノロジまたは Web ビューを使用するアプリのみがサポートされています。 アプリケーションに独自のネットワーク レイヤー実装が組み込まれている場合、Microsoft Enterprise SSO プラグインはサポートされません。  

MSAL を使用しないアプリ用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

- **キー**: `AppAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID のコンマ区切りの一覧
- **例**: `com.contoso.workapp, com.contoso.travelapp`

MDM 管理者によって SSO への参加を許可されている[同意済みアプリ](./application-consent-experience.md)は、エンド ユーザーのトークンをサイレントで取得できます。 そのため、信頼できるアプリケーションのみを許可リストに追加することが重要です。 

MSAL または ASWebAuthenticationSession を使用するアプリケーションをこのリストに追加する必要はありません。 これらのアプリケーションは既定で有効になっています。 

#### <a name="allow-creating-sso-session-from-any-application"></a>任意のアプリケーションからの SSO セッションの作成を許可する

Microsoft Enterprise SSO プラグインの既定では、SSO プラグインが既に共有資格情報を持っている場合にのみ、承認済みアプリに SSO を提供します。 Microsoft Enterprise SSO プラグインは、トークンの取得中に別の ADAL または MSAL ベースのアプリケーションによって呼び出されると、共有資格情報を取得できます。 ほとんどの Microsoft アプリには、Microsoft Authenticator または SSO プラグインが使用されています。 つまり、既定では、ネイティブ アプリ フローの外部で SSO を行うことがベスト エフォートです。  

`browser_sso_interaction_enabled` フラグを有効にすると、非 MSAL アプリと Safari ブラウザーが初期ブートストラップを実行し、共有資格情報を取得できるようになります。 Microsoft Enterprise SSO プラグインにまだ共有資格情報がない場合、Safari ブラウザー、ASWebAuthenticationSession、SafariViewController、または別の許可されたネイティブ アプリケーション内の Azure AD URL からサインインが要求されると、資格情報の取得が試行されます。  

- **キー**: `browser_sso_interaction_enabled`
- **型**: `Integer`
- **値**: 1 または 0

すべてのアプリでより一貫したエクスペリエンスを得るには、このフラグを有効にすることをお勧めします。 この機能は既定で無効になっています。 

#### <a name="disable-oauth2-application-prompts"></a>OAuth2 アプリケーションのプロンプトを無効にする

Microsoft Enterprise SSO プラグインでは、許可されたアプリケーションからのネットワーク要求に共有資格情報を追加することで SSO を提供します。 一部の OAuth2 アプリケーションでは、プロトコル レイヤーでエンドユーザー プロンプトを強制している場合があります。 このようなアプリでは、共有資格情報は無視されます。  

`disable_explicit_app_prompt` フラグを有効にすると、ネイティブ アプリケーションと Web アプリケーションの両方の機能が制限され、プロトコル レイヤーに対するエンドユーザー プロンプトが強制され、SSO をバイパスされます。

- **キー**: `disable_explicit_app_prompt`
- **型**: `Integer`
- **値**: 1 または 0

すべてのアプリでより一貫したエクスペリエンスを得るには、このフラグを有効にすることをお勧めします。 この機能は既定で無効になっています。 

#### <a name="use-intune-for-simplified-configuration"></a>Intune を使用して構成を簡略化する

Microsoft Intune を MDM サービスとして使用し、Microsoft Enterprise SSO プラグインの構成を容易にすることができます。 詳細については、[Intune の構成に関するドキュメント](/intune/configuration/ios-device-features-settings)を参照してください。

## <a name="using-the-sso-plug-in-in-your-application"></a>アプリケーションでの SSO プラグインの使用

[Apple デバイス用の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) バージョン 1.1.0 以降では、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。

Frontline Worker シナリオ向けにアプリケーションを構築している場合、機能の追加設定については、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」を参照してください。

## <a name="how-the-sso-plug-in-works"></a>SSO プラグインのしくみ

Microsoft Enterprise SSO プラグインは、[Apple の Enterprise Single Sign-On フレームワーク](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)に依存しています。 このフレームワークにオンボードしている ID プロバイダーは、ドメインのネットワーク トラフィックを傍受し、それらの要求の処理方法を強化または変更することができます。 たとえば、SSO プラグインでは、エンドユーザーの資格情報を安全に収集する追加の UI を表示すること、MFA を要求すること、またはアプリケーションにトークンをサイレントで提供することができます。

ネイティブ アプリケーションは、カスタム操作を実装し、SSO プラグインと直接対話することもできます。
シングル サインイン フレームワークについては、この [Apple の 2019 WWDC 動画](https://developer.apple.com/videos/play/tech-talks/301/)をご覧ください。

### <a name="applications-that-use-msal"></a>MSAL を使用するアプリケーション

[Apple デバイス用の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) バージョン 1.1.0 以降では、職場または学校アカウントのネイティブで、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。 

[すべての推奨される手順](./quickstart-v2-ios.md)を実行し、既定の[リダイレクト URI 形式](./redirect-uris-ios.md)を使用した場合、特別な構成は必要ありません。 SSO プラグインがインストールされたデバイス上で実行されている場合、MSAL によって、すべての対話型およびサイレント トークン要求、およびアカウントの列挙とアカウントの削除操作に対して自動的に呼び出されます。 MSAL にはカスタム操作に依存するネイティブ SSO プラグイン プロトコルが実装されているため、この設定にすると、エンド ユーザーにとって最もスムーズなネイティブ エクスペリエンスが提供されます。 

MDM で SSO プラグインが有効にされておらず、デバイスに Microsoft Authenticator アプリがインストールされている場合、MSAL では、対話型トークンの要求に代わりに Microsoft Authenticator アプリが使用されます。 SSO プラグインは、Microsoft Authenticator アプリと SSO を共有します。

### <a name="applications-that-dont-use-msal"></a>MSAL を使用しないアプリケーション

MSAL を使用しないアプリケーションでも、管理者が許可リストに明示的に追加すると、SSO を取得できます。 

以下の条件が満たされている限り、このようなアプリでコードを変更する必要はありません。

- アプリケーションでは、Apple フレームワークを使用してネットワーク要求を実行している (たとえば、[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)、[NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession))。 
- アプリケーションでは、標準プロトコルを使用して Azure AD と通信している (たとえば、OAuth2、SAML、WS-Federation)
- アプリケーションのネイティブ UI でプレーンテキストのユーザー名とパスワードを収集しない

この場合、アプリケーションでネットワーク要求を作成し、Web ブラウザーを開いてユーザーをサインインすると、SSO が提供されます。 ユーザーが Azure AD ログイン URL にリダイレクトされると、SSO プラグインによって URL が検証され、その URL に使用できる SSO 資格情報があるかどうかが確認されます。 存在する場合は、SSO プラグインから Azure AD に SSO 資格情報が渡されます。これにより、ユーザーに資格情報の入力が求められることなく、アプリケーションがネットワーク要求を完了できるようになります。 さらに、デバイスが Azure AD に認識されている場合、デバイスベースの条件付きアクセス チェックを満たすデバイス証明書も SSO プラグインによって渡されます。 

非 MSAL アプリに対する SSO をサポートするために、SSO プラグインには、「[プライマリ更新トークンとは](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)」で説明されている Windows ブラウザー プラグインと同様のプロトコルが実装されています。 

MSAL ベースのアプリと比較すると、SSO プラグインは、アプリが提供する既存のブラウザー ログイン エクスペリエンスと統合することにより、非 MSAL アプリに対してさらに透過的に機能します。 エンド ユーザーにはなじみのあるエクスペリエンスが表示され、各アプリケーションで追加のサインインを実行する必要がないという利点があります。 たとえば、SSO プラグインでは、ネイティブ アカウント ピッカーを表示する代わりに、SSO セッションを Web ベースのアカウント ピッカー エクスペリエンスに追加します。 

## <a name="next-steps"></a>次のステップ

iOS での共有デバイス モードの詳細については、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」を参照してください。
