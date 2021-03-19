---
title: Apple デバイス用の Microsoft Enterprise SSO プラグイン
titleSuffix: Microsoft identity platform | Azure
description: iOS、iPadOS、macOS デバイス用の Microsoft の Azure Active Directory SSO プラグインについて説明します。
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
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427098"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー)

>[!IMPORTANT]
> この機能[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

"*Apple デバイス用の Microsoft Enterprise SSO プラグイン*" は、Apple の [Enterprise Single Sign-On](https://developer.apple.com/documentation/authenticationservices) 機能をサポートするすべてのアプリケーションで、macOS、iOS、iPadOS 上の Azure Active Directory (Azure AD) アカウントに対するシングル サインオン (SSO) を提供します。 これには、お客様のビジネスで使用されている可能性がある、最新の ID ライブラリまたはプロトコルをまだサポートしていない古いアプリケーションも含まれます。 Microsoft は Apple と密接に連携してこのプラグインを開発し、アプリケーションの使いやすさを向上させ、Apple と Microsoft が提供できる最適な保護を提供しています。

Enterprise SSO プラグインは、現在、次のアプリの組み込み機能として利用できます。

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) - iOS、iPadOS
* Microsoft Intune [ポータル サイト](/mem/intune/apps/apps-company-portal-macos) - macOS

## <a name="features"></a>特徴

Apple デバイス用の Microsoft Enterprise SSO プラグインには、次のような利点があります。

- Apple の Enterprise Single Sign-On 機能をサポートするすべてのアプリケーションで、Azure AD アカウントに対する SSO が提供されます。
- 任意のモバイル デバイス管理 （MDM） ソリューションで有効にすることができます。
- Microsoft ID プラットフォーム ライブラリをまだ使用していないアプリケーションに SSO を拡張します。
- OAuth2、OpenID Connect、SAML を使用するアプリケーションに SSO を拡張します。

## <a name="requirements"></a>必要条件

Apple デバイス用の Microsoft Enterprise SSO プラグインを使用するには:

- デバイスで **サポート** され、Apple デバイス用の Microsoft Enterprise SSO プラグインを含むアプリが **インストール** されている必要があります。
  - iOS 13.0 以降: [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 以降: [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 以降: [Intune ポータル サイト アプリ](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- デバイスは、**MDM に登録されている** 必要があります (たとえば、Microsoft Intune)。
- デバイスで Enterprise SSO プラグインを有効にするには、構成が **デバイスにプッシュされる** 必要があります。 このセキュリティ制約は、Apple によって要求されます。

### <a name="ios-requirements"></a>iOS の要件:
- iOS 13.0 以降がデバイスにインストールされている必要があります。
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューでは、これらのアプリケーションは [Microsoft Authenticator アプリ](/intune/user-help/user-help-auth-app-overview.md)です。


### <a name="macos-requirements"></a>macOS の要件:
- macOS 10.15 以降がデバイスにインストールされている必要があります。 
- Apple デバイス用の Microsoft Enterprise SSO プラグインを提供する Microsoft アプリケーションが、デバイスにインストールされている必要があります。 パブリック プレビューでは、これらのアプリケーションには [Intune ポータル サイト アプリ](/intune/user-help/enroll-your-device-in-intune-macos-cp.md)が含まれます。

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>モバイル デバイス管理 (MDM) で SSO プラグインを有効にする

### <a name="microsoft-intune-configuration"></a>Microsoft Intune の構成

MDM サービスとして Microsoft Intune を使用する場合は、組み込みの構成プロファイル設定を使用して、Microsoft Enterprise SSO プラグインを有効にすることができます。

最初に、構成プロファイルの[シングル サインオン アプリ拡張機能](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension)の設定を構成し、[プロファイルをユーザーまたはデバイス グループに割り当てます](/mem/intune/configuration/device-profile-assign) (まだ割り当てられていない場合)。

SSO プラグインを有効にするプロファイル設定は、各デバイスが次回 Intune でチェックインするときに、グループのデバイスに自動的に適用されます。

### <a name="manual-configuration-for-other-mdm-services"></a>他の MDM サービスの手動構成

モバイル デバイス管理に Microsoft Intune を使用していない場合は、次のパラメーターを使用して、Apple デバイス用の Microsoft Enterprise SSO プラグインを構成します。

#### <a name="ios-settings"></a>iOS の設定:

- **拡張機能 ID**: `com.microsoft.azureauthenticator.ssoextension`
- **チーム ID**: (iOS ではこのフィールドは必要ありません)

#### <a name="macos-settings"></a>macOS の設定:

- **拡張機能 ID**: `com.microsoft.CompanyPortalMac.ssoextension`
- **チーム ID**: `UBF8T346G9`

#### <a name="common-settings"></a>共通設定:

- **[種類]** :リダイレクト
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

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft ID プラットフォーム ライブラリを使用しないアプリの SSO を有効にする

SSO プラグインを使用すると、Microsoft 認証ライブラリ (MSAL) などの Microsoft SDK を使用して開発されていないアプリケーションでも、シングル サインオンに参加させることができます。

SSO プラグインは、iOS および iPadOS の Microsoft Authenticator アプリ、または macOS の Intune ポータル サイト アプリをダウンロードし、そのデバイスを組織に登録したデバイスによって自動的にインストールされます。 組織では、多要素認証、パスワードなしの認証、条件付きアクセスなどのシナリオで Authenticator アプリを既に使用している可能性があります。 任意の MDM プロバイダーを使用するアプリケーションに対して有効にすることができますが、Microsoft では、Intune の Microsoft Endpoint Manager 内で簡単に構成できるようにしています。 許可リストは、SSO プラグインを使用するようにこれらのアプリケーションを構成するために使用されます。

>[!IMPORTANT]
> ネイティブの Apple ネットワーク テクノロジまたは Web ビューを使用するアプリのみがサポートされています。 アプリケーションに独自のネットワーク レイヤー実装が組み込まれている場合、Microsoft Enterprise SSO プラグインはサポートされません。  

Microsoft ID プラットフォーム ライブラリを使用しないアプリ用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

特定のアプリの一覧を指定する場合は、次のようにします。

- **キー**: `AppAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID のコンマ区切りの一覧
- **例**: `com.contoso.workapp, com.contoso.travelapp`

プレフィックスの一覧を指定する場合は、次のようにします。
- **キー**: `AppPrefixAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID プレフィックスのコンマ区切りの一覧です。 これにより、特定のプレフィックスで始まるすべてのアプリが SSO に参加できるようになることに注意してください。
- **例**: `com.contoso., com.fabrikam.`

MDM 管理者によって SSO への参加を許可されている[同意済みアプリ](./application-consent-experience.md)は、エンド ユーザーのトークンをサイレントで取得できます。 そのため、信頼できるアプリケーションのみを許可リストに追加することが重要です。 

>[!NOTE]
> MSAL または ASWebAuthenticationSession を使用するアプリケーションをこのリストに追加する必要はありません。 これらのアプリケーションは既定で有効になっています。 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>iOS デバイスでアプリ バンドル識別子を検出する方法

Appleは、App Store からバンドル ID を検出するための簡単な方法を提供していません。 SSO に使用するアプリのバンドル ID を検出する最も簡単な方法は、ベンダーまたはアプリの開発者に問い合わせることです。 この選択肢を使用できない場合は、お使いの MDM 構成を使用してバンドル ID を検出できます。 

お使いの MDM 構成で次のフラグを一時的に有効にします。

- **キー**: `admin_debug_mode_enabled`
- **型**: `Integer`
- **値**: 1 または 0

このフラグがオンであるときに、バンドル ID を知りたいデバイスの iOS アプリにサインインします。 次に、Microsoft Authenticator アプリ -> [ヘルプ] -> [ログの送信] -> [ログの表示] を開きます。 

ログ ファイルで、次の行を探します。

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

これにより、SSO 拡張機能が認識可能なすべてのアプリケーション バンドル識別子が得られます。 次に、それらの識別子を使用して、それらのアプリの SSO を構成できます。 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>不明なアプリケーションおよび Safari ブラウザーからユーザーがサインインできるようにします。

既定では、Microsoft Enterprise SSO プラグインは、ADAL や MSAL などの Microsoft ID プラットフォーム ライブラリを使用するアプリからユーザーがサインインした場合にのみ、許可されたアプリに対する SSO を提供します。 Microsoft Enterprise SSO プラグインは、新しいトークンの取得中に Microsoft ID プラットフォーム ライブラリを使用する別のアプリによって呼び出されたときに、共有資格情報を取得することもできます。

`browser_sso_interaction_enabled` フラグを有効にすると、Microsoft ID プラットフォーム ライブラリを使用していないアプリが初期ブートストラップを実行し、共有資格情報を取得できるようになります。 また、Safari ブラウザーが初期ブートストラップを実行し、共有資格情報を取得できるようになります。 Microsoft Enterprise SSO プラグインにまだ共有資格情報がない場合、Safari ブラウザー、ASWebAuthenticationSession、SafariViewController、または別の許可されたネイティブ アプリケーション内の Azure AD URL からサインインが要求されると、資格情報の取得が試行されます。  

- **キー**: `browser_sso_interaction_enabled`
- **型**: `Integer`
- **値**: 1 または 0

macOS の場合、すべてのアプリでより一貫したエクスペリエンスを得るために、この設定が必要です。 iOS と iPadOS の場合、ほとんどのアプリがサインインに Microsoft Authenticator アプリケーションを使用するため、この設定は必要ありません。 ただし、iOS または iPadOS で Microsoft Authenticator を使用しないアプリケーションがある場合、このフラグによってエクスペリエンスが向上するため、この設定を有効にすることをお勧めします。 この機能は既定で無効になっています。

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>初期ブートストラップで MFA の要求を無効にする

既定では、Microsoft Enterprise SSO プラグインは、最初のブートストラップを実行して共有資格情報を取得するときに、ユーザーが起動した現在のアプリケーションに必要でない場合でも、常に多要素認証 (MFA) をユーザーに求めます。 これは、後で MFA が必要になった場合に、ユーザーにプロンプトを表示することなく、追加のすべてのアプリケーションで共有資格情報を簡単に使用できるようにするためです。 これは、デバイス上でユーザーにプロンプトを表示する必要がある回数が減るため、一般的に適切な決定です。

`browser_sso_disable_mfa` を有効にすると、これはオフになり、アプリケーションまたはリソースで MFA が必要な場合にのみ、ユーザーにプロンプトが表示されます。 

- **キー**: `browser_sso_disable_mfa`
- **型**: `Integer`
- **値**: 1 または 0

このフラグを無効にしておくことをお勧めします。これにより、デバイス上でユーザーにプロンプトを表示する必要がある回数が減るためです。 MFA をほとんど使用しない組織の場合は、このフラグを有効にすることもできますが、その代わりに MFA をより頻繁に使用することをお勧めします。 このような理由から、それは既定では無効になっています。

#### <a name="disable-oauth2-application-prompts"></a>OAuth2 アプリケーションのプロンプトを無効にする

Microsoft Enterprise SSO プラグインでは、許可されたアプリケーションからのネットワーク要求に共有資格情報を追加することで SSO を提供します。 ただし、OAuth2 アプリケーションによっては、プロトコル レイヤーでエンドユーザーのプロンプトが誤って強制されることがあります。 このような場合、それらのアプリでは、Microsoft Enterprise SSO プラグインが他のアプリケーションに対して機能していても、共有資格情報が無視され、ユーザーにサインインを求めるプロンプトが表示されます。  

`disable_explicit_app_prompt` フラグを有効にすると、ネイティブ アプリケーションと Web アプリケーションの両方の機能が制限され、プロトコル レイヤーに対するエンドユーザー プロンプトが強制され、SSO をバイパスされます。

- **キー**: `disable_explicit_app_prompt`
- **型**: `Integer`
- **値**: 1 または 0

すべてのアプリでより一貫したエクスペリエンスを得るには、このフラグを有効にすることをお勧めします。 この機能は既定で無効になっています。 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>特定のアプリケーションに対して Cookie を使用して SSO を有効にする

少数のアプリが SSO 拡張機能と互換性がない場合があります。 特に、高度なネットワーク設定を持つアプリで SSO が有効になっていると、予期しない問題が発生する可能性があります (たとえば、ネットワーク要求が取り消されたか、中断されたことを示すエラーが表示される場合があります)。 

`Enable SSO for apps that don't use MSAL` セクションで説明されている方法を使用してサインインするときに問題が発生した場合は、それらのアプリの代替構成を試すことができます。 

それらの特定のアプリ用の Microsoft Enterprise SSO プラグインを構成するには、次のパラメーターを使用します。

- **キー**: `AppCookieSSOAllowList`
- **型**: `String`
- **値**: SSO への参加が許可されているアプリケーションのアプリケーション バンドル ID プレフィックスのコンマ区切りの一覧です。 これにより、特定のプレフィックスで始まるすべてのアプリが SSO に参加できるようになることに注意してください。
- **例**: `com.contoso.myapp1, com.fabrikam.myapp2`

このメカニズムを使用して SSO が有効になっているアプリケーションは、`AppCookieSSOAllowList` と `AppPrefixAllowList` の両方に追加する必要があることに注意して ください。

このオプションは、予期しないサインイン エラーが発生したアプリケーションに対してのみ使用することをお勧めします。 

#### <a name="use-intune-for-simplified-configuration"></a>Intune を使用して構成を簡略化する

既に説明したように、Microsoft Intune を MDM サービスとして使用すると、プラグインを有効にしたり、古いアプリを許可リストに追加して SSO を取得するなど、Microsoft Enterprise SSO プラグインの構成を容易にすることができます。 詳細については、[Intune の構成に関するドキュメント](/intune/configuration/ios-device-features-settings)を参照してください。

## <a name="using-the-sso-plug-in-in-your-application"></a>アプリケーションでの SSO プラグインの使用

[Apple デバイス用の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) バージョン 1.1.0 以降では、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。 これは、Microsoft Enterprise SSO プラグインのサポートを追加するために推奨される方法であり、これにより Microsoft ID プラットフォームのすべての機能を確実に利用できるようになります。

Frontline Worker シナリオ向けにアプリケーションを構築している場合、機能の追加設定については、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」を参照してください。

## <a name="how-the-sso-plug-in-works"></a>SSO プラグインのしくみ

Microsoft Enterprise SSO プラグインは、[Apple の Enterprise Single Sign-On フレームワーク](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc)に依存しています。 このフレームワークにオンボードしている ID プロバイダーは、ドメインのネットワーク トラフィックを傍受し、それらの要求の処理方法を強化または変更することができます。 たとえば、SSO プラグインでは、エンドユーザーの資格情報を安全に収集する追加の UI を表示すること、MFA を要求すること、またはアプリケーションにトークンをサイレントで提供することができます。

ネイティブ アプリケーションは、カスタム操作を実装し、SSO プラグインと直接対話することもできます。
シングル サインイン フレームワークについては、この [Apple の 2019 WWDC 動画](https://developer.apple.com/videos/play/tech-talks/301/)をご覧ください。

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Microsoft ID プラットフォーム ライブラリを使用するアプリケーション

[Apple デバイス用の Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) バージョン 1.1.0 以降では、職場または学校アカウントのネイティブで、Apple デバイス用の Microsoft Enterprise SSO プラグインがサポートされています。 

[すべての推奨される手順](./quickstart-v2-ios.md)を実行し、既定の[リダイレクト URI 形式](./redirect-uris-ios.md)を使用した場合、特別な構成は必要ありません。 SSO プラグインがインストールされたデバイス上で実行されている場合、MSAL によって、すべての対話型およびサイレント トークン要求、およびアカウントの列挙とアカウントの削除操作に対して自動的に呼び出されます。 MSAL にはカスタム操作に依存するネイティブ SSO プラグイン プロトコルが実装されているため、この設定にすると、エンド ユーザーにとって最もスムーズなネイティブ エクスペリエンスが提供されます。 

MDM で SSO プラグインが有効にされておらず、デバイスに Microsoft Authenticator アプリがインストールされている場合、MSAL では、対話型トークンの要求に代わりに Microsoft Authenticator アプリが使用されます。 SSO プラグインは、Microsoft Authenticator アプリと SSO を共有します。

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Microsoft ID プラットフォーム ライブラリを使用しないアプリケーション

MSAL などの Microsoft ID プラットフォーム ライブラリを使用しないアプリケーションでも、管理者がそれらを明示的に許可リストに追加すると、SSO を取得できます。 

以下の条件が満たされている限り、このようなアプリでコードを変更する必要はありません。

- アプリケーションでは、Apple フレームワークを使用してネットワーク要求を実行している (たとえば、[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)、[NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession))。 
- アプリケーションでは、標準プロトコルを使用して Azure AD と通信している (たとえば、OAuth2、SAML、WS-Federation)
- アプリケーションのネイティブ UI でプレーンテキストのユーザー名とパスワードを収集しない

この場合、アプリケーションでネットワーク要求を作成し、Web ブラウザーを開いてユーザーをサインインすると、SSO が提供されます。 ユーザーが Azure AD ログイン URL にリダイレクトされると、SSO プラグインによって URL が検証され、その URL に使用できる SSO 資格情報があるかどうかが確認されます。 存在する場合は、SSO プラグインから Azure AD に SSO 資格情報が渡されます。これにより、ユーザーに資格情報の入力が求められることなく、アプリケーションがネットワーク要求を完了できるようになります。 さらに、デバイスが Azure AD に認識されている場合、デバイスベースの条件付きアクセス チェックを満たすデバイス証明書も SSO プラグインによって渡されます。 

非 MSAL アプリに対する SSO をサポートするために、SSO プラグインには、「[プライマリ更新トークンとは](../devices/concept-primary-refresh-token.md#browser-sso-using-prt)」で説明されている Windows ブラウザー プラグインと同様のプロトコルが実装されています。 

MSAL ベースのアプリと比較すると、SSO プラグインは、アプリが提供する既存のブラウザー ログイン エクスペリエンスと統合することにより、非 MSAL アプリに対してさらに透過的に機能します。 エンド ユーザーにはなじみのあるエクスペリエンスが表示され、各アプリケーションで追加のサインインを実行する必要がないという利点があります。 たとえば、SSO プラグインでは、ネイティブ アカウント ピッカーを表示する代わりに、SSO セッションを Web ベースのアカウント ピッカー エクスペリエンスに追加します。 

## <a name="next-steps"></a>次のステップ

iOS での共有デバイス モードの詳細については、「[iOS デバイスの共有デバイス モード](msal-ios-shared-devices.md)」を参照してください。
