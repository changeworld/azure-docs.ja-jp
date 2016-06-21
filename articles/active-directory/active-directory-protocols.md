<properties
	pageTitle="Active Directory 認証プロトコル | Microsoft Azure"
	description="この記事では、Azure Active Directory でサポートされるさまざまな認証および承認プロトコルの概要を示します。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/31/2016"
	ms.author="priyamo"/>

# Active Directory の認証プロトコル

Azure Active Directory (Azure AD) では、最も広く使用されている認証および承認のプロトコルを複数サポートしています。

この一連の記事では、Azure AD でサポートされているプロトコルとその実装について説明します。要求と応答の例を使用しますが、プロトコルに直接統合するため、これらの記事が言語に大きく依存することはありません。

- [Azure AD での OAuth 2.0](active-directory-protocols-oauth-code.md): Azure AD での OAuth2.0 の承認付与フローとその実装について説明します。
- [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) - Azure AD での OpenID Connect 認証プロトコルの使用方法について説明します。
- [SAML プロトコル リファレンス](active-directory-saml-protocol-reference.md) - Azure AD で SAML プロトコルを使用して、[シングル サインオン](active-directory-single-sign-on-protocol-reference.md)と[シングル サインアウト](active-directory-single-sign-out-protocol-reference.md)をサポートする方法について説明します。


## リファレンスおよびトラブルシューティング

この一連の記事では、Azure AD アプリケーションのトラブルシューティングと、Azure AD をより深く理解するのに役立つ追加情報が提供されます。

- [フェデレーション メタデータ](active-directory-federation-metadata.md): Azure AD が生成するメタデータ ドキュメントの検索と解釈の方法について説明します。
- [サポートされているトークンと要求の種類](active-directory-token-and-claims.md): Azure AD が発行するトークンのさまざまな要求について説明します。
- [Azure AD の署名キーのロールオーバー](active-directory-signing-key-rollover.md): Azure AD の署名キーのロールオーバーの周期と、最も一般的なアプリケーション シナリオでのキーの更新方法について説明します。
- [認証プロトコルのトラブルシューティング](active-directory-error-handling.md): OAuth 2.0 と Azure AD の使用時に発生する最も一般的なエラーを解釈し、解決する方法について説明します。
- [Azure AD での OAuth 2.0 のベスト プラクティス](active-directory-oauth-best-practices.md): Azure AD で OAuth 2.0 を使用する場合のベスト プラクティスと、よくある落とし穴の回避方法について説明します。

<!---HONumber=AcomDC_0608_2016-->