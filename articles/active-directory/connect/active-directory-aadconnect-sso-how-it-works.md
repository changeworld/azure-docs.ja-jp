---
title: 'Azure AD Connect: シームレス シングル サインオン - しくみ | Microsoft Docs'
description: この記事では、Azure Active Directory シームレス シングル サインオン機能のしくみについて説明します。
services: active-directory
keywords: Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6507158a63de508164fc74bcafe39785046a2c79
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213352"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory シームレス シングル サインオン: 技術的な詳細

この記事では、Azure Active Directory シームレス シングル サインオン (シームレス SSO) 機能の技術的なしくみについて説明します。

## <a name="how-does-seamless-sso-work"></a>シームレス SSO のしくみ

このセクションは、3 つの部分に分かれています。
1. シームレス SSO 機能の設定。
2. Web ブラウザーでの 1 人のユーザーのシングル サインイン トランザクションのシームレス SSO での動作。
3. ネイティブ クライアントでの 1 人のユーザーのシングル サインイン トランザクションのシームレス SSO での動作。

### <a name="how-does-set-up-work"></a>設定のしくみ

シームレス SSO は、[こちら](active-directory-aadconnect-sso-quick-start.md)で示す通り、Azure AD Connect を使用して有効にできます。 この機能を有効にすると、次の手順が発生します。
- 各 Active Directory (AD) フォレスト内のオンプレミスの AD に (Azure AD を表す) `AZUREADSSOACC` という名前のコンピューター アカウントが作成されます。
- コンピューター アカウントの Kerberos の復号化キーは、Azure AD と安全に共有されます。 複数の AD フォレストがある場合は、それぞれに専用の Kerberos 復号化キーが作成されます。
- また、Azure AD のサインイン時に使用される 2 つの URL を表す、2 つの Kerberos サービス プリンシパル名 (SPN) も作成されます。

>[!NOTE]
> (Azure AD Connect を使用して) Azure AD と同期する各 AD フォレストとシームレス SSO を有効にするユーザー用に、コンピューター アカウントと Kerberos SPN が作成されます。 同じ方法で管理され削除されないことを保証するために、その他のコンピューター アカウントが格納されている `AZUREADSSOACC` コンピューター アカウントに、組織単位 (OU) が移動されます。

>[!IMPORTANT]
>少なくとも 30 日ごとに、`AZUREADSSOACC` コンピューター アカウントの [Kerberos の復号化キーをロールオーバーする](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)ことを強くお勧めします。

このセットアップが完了すると、シームレス SSO は、統合 Windows 認証 (IWA) を使用するその他のサインインと同様に機能します。

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Web ブラウザーでのシームレス SSO によるサインインのしくみ

Web ブラウザーでのサインインのフローは次のとおりです。

1. ユーザーは、web のアプリケーション（たとえば、outlook　Web アプリケーションに）、 https://outlook.office365.com/owa/)企業ネットワーク内のドメインに参加している、会社のデバイスからアクセスします。
2. まだサインインしていない場合、ユーザーは、Azure AD のサインイン ページにリダイレクトされます。
3. ユーザーが、Azure AD サインイン ページにユーザー名を入力します。

  >[!NOTE]
  >[特定のアプリケーション](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso)では、手順 2. と 3. をスキップします。

4. Azure AD が JavaScript をバックグラウンドで使用して、Kerberos チケットを提供するよう、401 認証エラーを通じてクライアントに要求します。
5. ブラウザーは、代わりに Active Directory から (Azure AD を表す) `AZUREADSSOACC` コンピューター アカウント用にチケットを要求します。
6. Active Directory がコンピューター アカウントを検索し、コンピューター アカウントのシークレットで暗号化された Kerberos チケットをブラウザーに返します。
7. ブラウザーは、Active Directory から取得した Kerberos チケットを Azure AD に転送します。
8. Azure AD が、会社のデバイスにサインインしているユーザーの ID を含む Kerberos チケットを以前に共有していたキーを使用して解読します。
9. 評価後、Azure AD はアプリケーションにトークンを返すか、多要素認証などの他の検査を実行するようユーザーに要求します。
10. ユーザーのサインインが成功すると、アプリケーションにアクセスできるようになります。

次の図に、すべてのコンポーネントと必要な手順を示します。

![シームレス シングル サインオン - Web アプリのフロー](./media/active-directory-aadconnect-sso/sso2.png)

シームレス SSO は便宜的であり、何らかの理由で失敗した場合、サインイン エクスペリエンスはその通常の動作に戻ります。つまり、ユーザーはサインインするためにパスワードを入力する必要があります。

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>ネイティブ クライアントでのシームレス SSO によるサインインのしくみ

ネイティブ クライアントでのサインインのフローは次のとおりです。

1. ユーザーは、(Outlook クライアントなどの) ネイティブ アプリケーションに企業ネットワーク内のドメインに参加している会社のデバイスからアクセスします。
2. ユーザーがまだサインインしていない場合は、ネイティブ アプリケーションが、デバイスの Windows セッションからそのユーザーのユーザー名を取得します。
3. アプリが、Azure AD にユーザー名を送信し、テナントの WS-Trust MEX エンドポイントを取得します。
4. 次にアプリは、統合認証エンドポイントが使用可能かどうかを確認するために、WS-Trust MEX エンドポイントにクエリを実行します。
5. 手順 4. が成功した場合は、Kerberos チャレンジが発行されます。
6. アプリが Kerberos チケットを取得できる場合は、Azure AD の統合認証エンドポイントにそのチケットを転送します。
7. Azure AD が Kerberos チケットを復号化して検証します。
8. Azure AD は、ユーザーをサインインさせ、アプリに SAML トークンを発行します。
9. アプリは、Azure AD の OAuth2 トークン エンドポイントに SAML トークンを送信します。
10. Azure AD は SAML トークンを検証し、指定されたリソースのアクセス トークンおよび更新トークンと、ID トークンをアプリに発行します。
11. ユーザーは、アプリのリソースにアクセスできます。

次の図に、すべてのコンポーネントと必要な手順を示します。

![シームレス シングル サインオン - ネイティブ アプリ フロー](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>次の手順

- [**クイック スタート**](active-directory-aadconnect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**よく寄せられる質問**](active-directory-aadconnect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。
