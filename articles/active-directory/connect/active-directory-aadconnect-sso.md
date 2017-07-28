---
title: "Azure AD Connect: シームレス シングル サインオン | Microsoft Docs"
description: "このトピックでは、Azure Active Directory (Azure AD) シームレス シングル サインオンについて説明します。この機能により、企業ネットワーク内の企業のデスクトップ ユーザーに真のシングル サインオンを提供できます。"
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
ms.openlocfilehash: 72b5217bd8de29fdad753d89f34934f64c551ff2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/16/2017

---

# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオン

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory シームレス シングル サインオンとは

Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなります。また、通常はユーザー名の入力も不要です。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。

シームレス SSO は、サインインの方法として、[パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md)または[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)のどちらとも組み合わせることができます。

![シームレス シングル サインオン](./media/active-directory-aadconnect-sso/sso1.png)

>[!NOTE]
>この機能は、Active Directory フェデレーション サービス (ADFS) には適用_されません_。ADFS はこの機能を既に備えています。

## <a name="key-benefits-of-using-azure-ad-seamless-sso"></a>Azure AD シームレス SSO を使用する主な利点

- *優れたユーザー エクスペリエンス*
  - ユーザーは、オンプレミスとクラウドベースの両方のアプリケーションに自動的にサインインします。
  - ユーザーはパスワードを繰り返し入力する必要はありません。
- *デプロイと管理が容易*
  - オンプレミスでは、この機能の動作のために追加のコンポーネントは不要です。
  - [パスワード ハッシュ同期](active-directory-aadconnectsync-implement-password-synchronization.md)または[パススルー認証](active-directory-aadconnect-pass-through-authentication.md)の、どちらの管理された認証方法でも機能します。
  - グループ ポリシーを使用して、一部のユーザーまたはすべてのユーザーに展開できます。
  - Azure AD で非 Windows 10 デバイスを登録します。 これには、バージョン 2.1 以降の [workplace-join クライアント](https://www.microsoft.com/download/details.aspx?id=53554)が必要です。

## <a name="feature-highlights"></a>機能概要

- サインインのユーザー名には、オンプレミスの既定のユーザー名 (`userPrincipalName`) または Azure AD Connect で構成された別の属性 (`Alternate ID`) を指定できます。
- シームレス SSO は便宜的な機能です。 これが何らかの理由で失敗した場合、ユーザーのサインイン エクスペリエンスは通常の動作に戻ります。つまり、ユーザーはサインイン ページでパスワードを入力する必要があります。
- アプリケーションが Azure AD サインイン要求で `domain_hint` パラメーター (テナントを識別する) または `login_hint` パラメーター (ユーザーを識別する) を転送する場合、、ユーザーはユーザー名やパスワードを入力することなく自動的にサインインします。
- この機能は、Azure AD Connect を使用して有効にできます。
- これは無料の機能であり、この機能を使用するために Azure AD の有料エディションは不要です。
- この機能は、Web ブラウザー ベースのクライアントと、Kerberos 認証に対応したプラットフォームおよびブラウザーで[最新の認証](https://aka.ms/modernauthga)をサポートする Office クライアントでサポートされています。

| OS\ブラウザー |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|あり|サポートされていません|あり|はい\*|該当なし
|Windows 8.1|あり|サポートされていません|あり|はい\*|該当なし
|Windows 8|あり|サポートされていません|あり|はい\*|該当なし
|Windows 7|あり|サポートされていません|あり|はい\*|該当なし 
|Mac OS X|該当なし|該当なし|はい\*|はい\*|サポートされていません

\*[追加の構成](active-directory-aadconnect-sso-quick-start.md#browser-considerations)が必要

>[!NOTE]
>Windows 10 の場合、Azure AD で最適なシングル サインオン エクスペリエンスを実現するために、[Azure AD Join](../active-directory-azureadjoin-overview.md) を使用することをお勧めします。

## <a name="next-steps"></a>次のステップ

- [**クイック スタート**](active-directory-aadconnect-sso-quick-start.md) - Azure AD シームレス SSO を動作させます。
- [**技術的な詳細**](active-directory-aadconnect-sso-how-it-works.md) - この機能のしくみを確認します。
- [**よく寄せられる質問**](active-directory-aadconnect-sso-faq.md) - よく寄せられる質問と回答です。
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - 機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。

