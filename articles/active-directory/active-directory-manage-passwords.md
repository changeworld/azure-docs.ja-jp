<properties
	pageTitle="Azure Active Directory 内のパスワードを管理する | Microsoft Azure"
	description="Azure Active Directory のパスワードを管理する方法。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="curtand"/>

# Azure Active Directory のパスワードを管理する

管理者は、Azure クラシック ポータルで Azure Active Directory (Azure AD) のユーザーのパスワードをリセットできます。ディレクトリの名前をクリックし、[ユーザー] ページでユーザーの名前をクリックして、ポータルの下部にある **[パスワードのリセット]** をクリックします。

このトピックの以降の部分では、Azure Active Directory がサポートする次のようなパスワード管理機能について詳しく説明します。

- **セルフ サービスによるパスワード**の変更では、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、有効期限が切れた、または有効期限が切れていないパスワードを変更できます。
- **セルフ サービスによるパスワード**のリセットでは、エンド ユーザーまたは管理者は、管理者またはヘルプ デスクにサポートを依頼することなく、パスワードを自動的にリセットできます。セルフ サービスのパスワード リセットには、Azure AD Premium または Basic が必要です。詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
- **管理者によるパスワードのリセット**では、管理者は、Azure クラシック ポータル内からエンド ユーザーまたは別の管理者のパスワードをリセットできます。
- **パスワード管理アクティビティ レポート**では、組織内で発生したパスワードのリセットおよび登録アクティビティの詳細が管理者に提供されます。
- **パスワード ライトバック**では、クラウドからオンプレミスのパスワードを管理できるので、フェデレーション ユーザーまたはパスワード同期済みユーザーは、またはこれらのユーザーに代わって、上記のシナリオをすべて実行できます。パスワード ライトバックには Azure AD Premium が必要です。詳細については、「[Azure Active Directory Premium の概要](active-directory-get-started-premium.md)」を参照してください。

> [AZURE.NOTE]
Azure AD のワールドワイド インスタンスを使用している中国のお客様は、Azure AD Premium を利用できます。中国の 21Vianet が運営する Microsoft Azure サービスでは、Azure AD Premium は現在サポートされていません。詳細については、[Azure Active Directory フォーラム](https://feedback.azure.com/forums/169401-azure-active-directory/)からお問い合わせください。

次のリンクを使用して、最も関心のあるドキュメントに移動してください。

- [概要: Azure AD でのパスワード管理](active-directory-passwords-how-it-works.md)
- [Azure AD のセルフ サービス パスワードのリセット: セルフ サービスのパスワード リセットを有効化、構成、テストする方法](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)
- [Azure AD のセルフ サービス パスワードのリセット: ニーズに合わせてパスワード リセットをカスタマイズする方法](active-directory-passwords-customize.md)
- [Azure AD のセルフ サービス パスワードのリセット: 展開と管理のベスト プラクティス](active-directory-passwords-best-practices.md)
- [Azure AD でのパスワード管理のレポート: テナントでパスワード管理アクティビティを表示する方法](active-directory-passwords-get-insights.md)
- [パスワード ライトバック: オンプレミスのパスワードの管理用２に Azure AD を構成する方法](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)
- [Azure AD のパスワード管理に関するトラブルシューティング](active-directory-passwords-troubleshoot.md)
- [Azure AD のパスワード管理に関する FAQ](active-directory-passwords-faq.md)


## 次の手順

- [Administer your Azure AD directory (Azure AD ディレクトリの管理)](active-directory-administer.md)
- [Azure AD でのユーザーの作成または編集](active-directory-create-users.md)
- [Azure AD でのグループの管理](active-directory-manage-groups.md)

<!---HONumber=AcomDC_0128_2016-->