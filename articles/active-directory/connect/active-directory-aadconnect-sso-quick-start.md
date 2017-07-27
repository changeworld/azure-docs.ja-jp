---
title: "Azure AD Connect: シームレス シングル サインオン - クイック スタート| Microsoft Docs"
description: "この記事では、Azure Active Directory シームレス シングル サインオンの開始方法について説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 451d4fd24dc506fb4a659edb710ab67a66cbbde7
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-quick-start"></a>Azure Active Directory シームレス シングル サインオン: クイック スタート

Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデスクトップを使用するときに、自動的にサインインを行います。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。

## <a name="how-to-deploy-azure-ad-seamless-sso"></a>Azure AD シームレス SSO をデプロイする方法

シームレス SSO をデプロイするには、以下の手順に従います。
1. *前提条件を確認する*: 機能を有効にする前に、テナントとオンプレミス環境を正しく設定します。
2. *機能を有効にする*: Azure AD Connect を使用して、テナントでシームレス SSO を有効にします。
3. *機能をロールアウトする*: グループ ポリシーを使って一部またはすべてのユーザーに機能をロールアウトします。
4. *機能をテストする*: シームレス SSO を使用して、ユーザー サインインをテストします。

## <a name="step-1-check-prerequisites"></a>手順 1: 前提条件を確認する

次の前提条件が満たされていることを確認します。

1. Azure AD Connect サーバーがセットアップされている: サインイン方法として[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)を使用する場合、追加の前提条件はありません。 サインイン方法として[パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md)を使用する場合や、Azure AD Connect と Azure AD の間にファイアウォールがある場合は、次の点を確認してください。
- Azure AD Connect のバージョン 1.1.484.0 以降を使用している。
- Azure AD Connect が `*.msappproxy.net` URL とポート 443 経由で通信できる。 この前提条件は、機能を有効にした場合にのみ適用され、実際のユーザー サインインには適用されません。
- Azure AD Connect が [Azure データ センターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に対して直接 IP 接続できる。 この前提条件も、機能を有効にした場合にのみ適用されます。
2. (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストとシームレス SSO を有効にするユーザーに、ドメイン管理者の資格情報が必要です。

## <a name="step-2-enable-the-feature"></a>手順 2: 機能を有効にする

シームレス SSO は、[Azure AD Connect](active-directory-aadconnect.md) を使用して有効にできます。

Azure AD Connect の新しいインストールを実行する場合は、[カスタム インストール パス](active-directory-aadconnect-get-started-custom.md)を選択します。 [ユーザー サインイン] ページで、[シングル サインオンを有効にする] チェック ボックスをオンにします。

![Azure AD Connect - [ユーザー サインイン]](./media/active-directory-aadconnect-sso/sso8.png)

Azure AD Connect を既にインストールしている場合は、Azure AD Connect で [ユーザー サインインの変更] を選択して、[次へ] をクリックします。 次に、[シングル サインオンを有効にする] チェック ボックスをオンにします。

![Azure AD Connect - [ユーザー サインインの変更]](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

[シングル サインオンを有効にする] ページが表示されるまで、ウィザードの手順を続行します。 (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストとシームレス SSO を有効にするユーザーに、ドメイン管理者の資格情報を付与します。 

ウィザードの完了後、シームレスな SSO がテナントで有効になります。

>[!NOTE]
> ドメイン管理者の資格情報は Azure AD Connect にも Azure AD にも保存されず、機能の有効化のみに使用されます。

## <a name="step-3-roll-out-the-feature"></a>手順 3: 機能をロールアウトする

ユーザーに機能をロールアウトするには、Active Directory のグループ ポリシーによって、2 つの Azure AD URL (https://autologon.microsoftazuread-sso.com および https://aadg.windows.net.nsatc.net) をユーザーのイントラネット ゾーン設定に追加する必要があります。

>[!NOTE]
> 以下の手順は、Windows 上の Internet Explorer と Google Chrome (信頼済みサイト URL のセットを Internet Explorer と共有する場合) でのみ機能します。 Mac 上の Mozilla Firefox および Mac 上の Google Chrome をセットアップする方法については、次のセクションをご覧ください。

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>ユーザーのイントラネット ゾーンの設定を変更する必要がある理由

既定では、ブラウザーは、URL から適切なゾーン (インターネットまたはイントラネット) を自動的に判断します。 たとえば、http://contoso/ はイントラネット ゾーンにマップされ、http://intranet.contoso.com/ はインターネット ゾーンにマップされます (URL にピリオドが含まれているため)。 クラウド エンドポイントの URL が、2 つの Azure AD URL と同様にブラウザーのイントラネット ゾーンに明示的に追加されていなければ、ブラウザーは Kerberos チケットをクラウド エンドポイントに送信しません。

### <a name="detailed-steps"></a>詳細な手順

1. グループ ポリシー管理ツールを開きます。
2. 一部またはすべてのユーザーに適用されるグループ ポリシーを編集します。 この例では、**既定のドメイン ポリシー**を使用します。
3. **[ユーザーの構成]、[管理用テンプレート]、[Windows コンポーネント]、[Internet Explorer]、[インターネット コントロール パネル]、[セキュリティ]** の順に移動して、**[サイトとゾーンの割り当て一覧]** を選択します。
![シングル サインオン](./media/active-directory-aadconnect-sso/sso6.png)  
4. ポリシーを有効にし、ダイアログ ボックスで次の値 (Kerberos チケットの転送先となる Azure AD の URL) とデータ (イントラネット ゾーンを示す「*1*」) を入力します。

        Value: https://autologon.microsoftazuread-sso.com
        Data: 1
        Value: https://aadg.windows.net.nsatc.net
        Data: 1
>[!NOTE]
> 一部のユーザー (共有キオスクでサインインするユーザーなど) にシームレス SSO の利用を許可しないようにする場合は、前述の値を「*4*」に設定します。 この操作によって、Azure AD の URL が制限付きゾーンに追加され、シームレス SSO は常に失敗します。

5. **[OK]** をクリックし、もう一度 **[OK]** をクリックします。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso7.png)

### <a name="browser-considerations"></a>ブラウザーの考慮事項

#### <a name="mozilla-firefox"></a>Mozilla Firefox

Mozilla Firefox は、Kerberos 認証を自動的に行いません。 各ユーザーが、次の手順に従って、Firefox の設定に Azure AD の URL を手動で追加する必要があります。
1. Firefox を実行し、アドレス バーに「`about:config`」と入力します。 表示されているすべての通知を無視します。
2. **network.negotiate-auth.trusted-uris** の設定を検索します。 この設定は、Kerberos 認証用の Firefox の信頼済みサイトを一覧表示します。
3. 右クリックし、[変更] を選択します。
4. フィールドに「https://autologon.microsoftazuread-sso.com」と「https://aadg.windows.net.nsatc.net」を入力します。
5. [OK] をクリックし、ブラウザーを再度開きます。

>[!NOTE]
>シームレス SSO は、Firefox のプライベート ブラウズ モードでは動作しません。

#### <a name="google-chrome-on-mac"></a>Mac 上の Google Chrome

Mac などの Windows 以外のプラットフォームで Google Chrome を使用し、統合認証で Azure AD の URL をホワイトリスト化する方法については、[この記事](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist)を参照してください。

Mac ユーザーがサードパーティの Active Directory グループ ポリシーの拡張機能を使用して、Azure AD の URL を Firefox および Mac 上の Google Chrome にロールアウトする場合については、この記事の範囲外です。

## <a name="step-4-test-the-feature"></a>手順 4: 機能をテストする

特定のユーザーに対してこの機能をテストするには、次の条件が_すべて_満たされていることを確認します。
  - ユーザーが会社のデバイスでサインインしている。
  - デバイスが Active Directory (AD) ドメインに既に参加している。
  - デバイスが、企業のワイヤードまたはワイヤレス ネットワーク上や、VPN 接続などのリモート アクセス接続経由でドメイン コントローラー (DC) に直接接続している。
  - グループ ポリシーを使用して、このユーザーに[機能がロールアウト](##step-3-roll-out-the-feature)されている。

ユーザーがユーザー名のみを入力し、パスワードを入力しないシナリオをテストする場合:
- 新しいプライベート ブラウザー セッションで *https://myapps.microsoft.com/* にサインインします。

ユーザーがユーザー名またはパスワードを入力する必要がないシナリオをテストする場合: 
- 新しいプライベート ブラウザー セッションで *https://myapps.microsoft.com/contoso.onmicrosoft.com* にサインインします。 "*contoso*" を自分のテナント名に置き換えます。
- または、新しいプライベート ブラウザー セッションで *https://myapps.microsoft.com/contoso.com* にサインインします。 "*contoso.com*" を、テナントで検証されたドメイン (フェデレーション ドメインではなく) に置き換えます。

## <a name="next-steps"></a>次のステップ

- [**技術的な詳細**](active-directory-aadconnect-sso-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

