<properties
   pageTitle="Azure Active Directory 認証プロトコル | Microsoft Azure"
   description="Azure Active Directory (AD) でサポートされる認証プロトコルの概要"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# Azure Active Directory の認証プロトコル

Azure Active Directory (Azure AD) では、最も広く使用されている認証および承認のプロトコルを複数サポートしています。このセクションの各トピックでは、Azure AD でサポートされているプロトコルとその実装について説明します。トピックには、サポートされている要求の種類の確認、フェデレーション メタデータの用途の概要、OAuth 2.0 および SAML 2.0 プロトコルの詳しいリファレンス ドキュメント、トラブルシューティング セクションなどがあります。

## 認証プロトコルの記事とリファレンス

- [Azure AD での署名キーのロールオーバーに関する重要な情報](active-directory-signing-key-rollover.md) - Azure AD の署名キーのロールオーバーの周期、キーを自動的に更新するために可能な変更、最も一般的なアプリケーション シナリオを更新する方法について説明します。


- [サポートされているトークンとクレームの種類](active-directory-token-and-claims.md) - Azure AD が発行するトークンのクレームについて説明します。


- [フェデレーション メタデータ](https://msdn.microsoft.com/library/azure/dn195592.aspx) - Azure AD が生成するメタデータ ドキュメントの検索と解釈の方法について説明します。


- [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) - Azure AD での OAuth 2.0 の実装について説明します。


- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) - 認証に OAuth 2.0 (承認プロトコル) を使用する方法について説明します。


- [SAML プロトコル リファレンス](https://msdn.microsoft.com/library/azure/dn195591.aspx) - Azure AD のシングル サインオンおよびシングル サインアウト SAML プロファイルについて説明します。


- [WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) - Azure AD での WS-Federation 1.2 について説明します。


- [認証プロトコルのトラブルシューティング](https://msdn.microsoft.com/library/azure/dn195584.aspx) - Azure AD の使用時に発生する問題を回避する方法と、エラーを解釈し、解決する方法について説明します。



## 関連項目

[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)

[認証に Azure AD を使用する](../app-service-web/web-sites-authentication-authorization.md)

[Active Directory のコード例](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->