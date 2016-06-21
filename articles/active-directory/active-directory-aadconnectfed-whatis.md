<properties
	pageTitle="Azure AD Connect とフェデレーション | Microsoft Azure"
	description="このページは、Azure AD Connect を使用した AD FS の操作に関するすべてのドキュメントの中心となる場所です。"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/06/2016"
	ms.author="anandy"/>


# Azure AD Connect とフェデレーション

Azure AD Connect により、オンプレミスの AD FS と Azure AD でフェデレーションを構成できます。フェデレーション サインオンを設定すると、ユーザーは自分のオンプレミスのパスワードを使用して Azure AD ベースのサービスにサインオンすることができます。また、企業ネットワークにアクセスしているときはパスワードを再入力する必要はありません。AD FS を使用するフェデレーション オプションでは、新しい AD FS をデプロイすることも、Windows Server 2012 R2 ファームの既存の AD FS を指定することもできます。

このトピックは Azure AD Connect のフェデレーション関連機能に関する情報のホームであり、関連する他のすべてのトピックへのリンクを示します。Azure AD Connect へのリンクについては、「オンプレミス ID と Azure Active Directory の統合」を参照してください。

## Azure AD Connect - フェデレーションに関するトピック

| トピック | 内容 |
|:------|:-----------|
| **Azure AD Connect ユーザーのサインイン オプション** ||
| [ユーザー サインイン オプションについて](active-directory-aadconnect-user-signin.md) | さまざまなユーザー サインイン オプションの説明とそれらの Azure サインイン ユーザー エクスペリエンスへの影響 |
| **Azure AD Connect を使用した AD FS のインストール**||
| [前提条件](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) | Azure AD Connect 経由の正常な AD FS インストールの前提条件|
| [AD FS ファームの構成](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) | Azure AD Connect を使用して、新しい AD FS ファームをインストールする方法 |
| **AD FS 構成の変更** | |
| [信頼の修復](active-directory-aadconnect-federation-management.md#reparing-the-trust) | オンプレミスの AD FS と Office 365 または Azure 間の現在の信頼を修復する方法 |
| [新しい AD FS サーバーの追加](active-directory-aadconnect-federation-management.md#adding-a-new-ad-fs-server) | 初期インストール後の追加の AD FS サーバーによる AD FS ファームの拡張 |
| [新しい AD FS WAP サーバーの追加](active-directory-aadconnect-federation-management.md#adding-a-new-wap-server) | 初期インストール後の追加の WAP サーバーによる AD FS ファームの拡張 |
| [新しいフェデレーション ドメインの追加](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain) | Azure AD とフェデレーションする別のドメインの追加 |
|**インストール後のタスク**||
| [カスタムの会社のロゴまたはイラストの追加](active-directory-aadconnect-federation-management.md#add-custom-company-logo-or-illustration)| AD FS サインイン ページに表示されるカスタムのロゴを指定して、サインイン エクスペリエンスを変更する |
| [サインインの説明の追加](active-directory-aadconnect-federation-management.md#add-sign-in-description) | AD FS サインイン ページのサインインの説明の変更 | 
| [AD FS の要求規則の変更](active-directory-aadconnect-federation-management.md#modifying-ad-fs-claim-rules) | Azure AD Connect Sync 構成に対応する AD FS の要求規則の変更または追加 |


## その他のリソース

* [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0608_2016-->