<properties
	pageTitle="カスタム ドメイン名を使用してユーザーのサインインを簡略化する | Microsoft Azure"
	description="Azure Active Directory に独自のドメイン名を追加する方法とその他の関連情報について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="jeffsta"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/08/2016"
	ms.author="curtand;jeffsta"/>

# カスタム ドメイン名を使用してユーザーのサインインを簡略化する

独自のカスタム ドメイン名を使用すると、サインインをはじめとする Azure Active Directory のユーザー エクスペリエンスを高め、簡略化することができます。たとえば、勤務先の組織が "contoso.com" というドメイン名を所有している場合、ユーザーは、慣れ親しんだユーザー名 (joe@contoso.com など) でサインインすることができます。

Azure Active Directory のすべてのディレクトリには、Azure をはじめとする Microsoft の各種サービスを利用できる "contoso.onmicrosoft.com" 形式のドメイン名が最初から存在しています。組み込みのドメインについては、[こちら](#built-in-domain-names-for-azure-active-directory)を参照してください。

## Azure AD でカスタム ドメイン名を使用する

エンド ユーザーにとって馴染みのあるカスタム ドメイン名を所有している組織では、そのカスタム ドメイン名を Azure Active Directory で使用することをお勧めします。Azure Active Directory でカスタム ドメイン名を使用するには、次の作業を行います。

-   [カスタム ドメイン名を追加して確認する](active-directory-add-domain-add-verify-general.md)

-   [カスタム ドメインにユーザーを割り当てる](active-directory-add-domain-add-users.md)

## Office 365 などのサービスでカスタム ドメイン名を使用する

独自に追加、確認したカスタム ドメイン名は、Azure AD における他のリソースと同様、Office 365 や Intune など、Azure AD を使う各種アプリケーションで利用することができます。たとえば、Exchange Online でカスタム ドメイン名を使用すれば、ユーザーは使い慣れた電子メール アドレス (joe@contoso.com など) で電子メールを送受信することができます。そうした他のアプリケーションでカスタム ドメインを利用できるようにするには、アプリケーションごとの手順に従って、DNS レジストラーで DNS エントリを追加登録する必要があります。

-   [Office 365 でカスタム ドメインを使用する](https://support.office.com/article/Add-your-users-and-domain-to-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611?ui=ja-JP&rs=ja-JP&ad=US)

-   [Intune でカスタム ドメインを使用する](https://technet.microsoft.com/library/dn646966.aspx#BKMK_DomainNames)

## Azure AD でドメイン名を管理する

ドメイン名は通常、Azure Active Directory で日常的に管理する必要はありません。

-   [Azure Active Directory に存在するドメイン名の一覧を表示する](active-directory-add-domain-add-users.md)

-   [カスタム ドメイン名の DNS レジストラーを変更する場合にすべきこと](active-directory-add-domain-change-registrar.md)

## カスタム ドメイン名を削除する

カスタム ドメイン名が使用されなくなった場合やそのドメイン名を別の Azure AD で使用する必要が生じた場合、Azure AD からドメイン名を削除することができます。

-   [カスタム ドメイン名を削除する](#_Deleting_a_custom)

## Azure Active Directory の組み込みドメイン名

Azure Active Directory (Azure AD) の組み込みドメインとユーザーによって追加されるカスタム ドメインとの違いは何でしょうか。

-   組み込みドメイン: Azure AD ディレクトリに最初から用意されている contoso.onmicrosoft.com 形式のドメインです。

-   カスタム ドメイン: 組織が既に所有しているドメイン名です (例: contoso.com)。

## PowerShell または Graph API を使用してドメイン名を管理する

Azure Active Directory のドメイン名に関する管理作業の多くは、Microsoft PowerShell を使用するか、プログラムから Graph API を使用して行うこともできます。

-   [PowerShell を使用して Azure AD のドメイン名を管理する](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Graph API を使用して Azure AD のドメイン名を管理する (プレビュー)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)


## 次のステップ

その他、Azure Active Directory におけるドメイン名の使用法に関連したリソースをお探しの場合は、次のページを参照してください。

- [サインイン ページとアクセス パネル ページに対する会社のブランドの追加](active-directory-add-company-branding.md)
- [Add and verify a custom domain name in Azure Active Directory (Azure Active Directory にカスタム ドメイン名を追加して確認する)](active-directory-add-domain-add-verify-general.md)
- [Assign users to a custom domain (カスタム ドメインにユーザーを割り当てる)](active-directory-add-domain-add-users.md)
- [Change the DNS registrar for your custom domain name (カスタム ドメイン名の DNS レジストラーを変更する)](active-directory-add-domain-change-registrar.md)
- [Delete a custom domain in Azure Active Directory (Azure Active Directory からカスタム ドメインを削除する)](active-directory-add-domain-delete-domain.md)

<!---HONumber=AcomDC_0211_2016-->