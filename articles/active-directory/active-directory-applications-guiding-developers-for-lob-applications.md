<properties
	pageTitle="Azure AD とアプリケーション: 開発者ガイド | Microsoft Azure"
	description="IT プロフェッショナル向けに記述されたこの記事では、Azure アプリケーションを Active Directory を統合するためのガイドラインを示します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="kgremban"/>

# Azure AD とアプリケーション: 開発者ガイド

## 概要

このガイドでは、Azure Active Directory (AD) 用の基幹業務 (LoB) アプリケーションの開発の概要について説明します。特に Active Directory/Office 365 のグローバル管理者向けに記述されています。

Azure AD と統合されるアプリケーションを構築すると、組織内のユーザーは、Office 365 でシングル サインオンできます。アプリケーションを Azure AD と統合すると、アプリケーションに対して設定する認証ポリシーを管理できます。条件付きアクセスと多要素認証 (MFA) によってアプリを保護する方法については、「[アクセス ルールの構成](active-directory-conditional-access-azuread-connected-apps.md)」を参照してください。

Azure Active Directory を使用するためには、アプリケーションを登録する必要があります。アプリケーションを登録することで、組織の開発者は、Azure AD を使用して、電子メール、予定表、ドキュメントなどのユーザー リソースへのアクセスを要求する組織のメンバーを認証できます。

ディレクトリ内の (ゲスト以外の) すべてのメンバーがアプリケーションを登録できます。これは*アプリケーション オブジェクトの作成*とも呼ばれます。

アプリケーションを登録すると、ユーザーは次の操作を実行できます。

- Azure AD が認識するアプリケーションの ID を取得する
- アプリケーションが自身を AD に対して認証するために使用する 1 つまたは複数のシークレット/キーを取得する
- Azure ポータルで、名前やロゴなどを使用してアプリケーションをブランド化する
- アプリ用の Azure AD 承認機能を活用する
  - アプリケーションのロール ベースのアクセス制御 (RBAC)
  - OAuth 承認サーバーとしての Azure Active Directory (アプリケーションによって公開される API をセキュリティで保護する)

- アプリケーションが期待どおりに機能するために必要なアクセス許可を宣言する。以下が含まれます。
	  - アプリのアクセス許可 (グローバル管理者のみ)。次に例を示します。
	    - 別の Azure AD アプリケーションのロール メンバーシップ、または Azure リソース、リソースグループ、またはサブスクリプションに関係するロール メンバーシップ
	  - 委任されたアクセス許可 (任意のユーザー)次に例を示します。
	    - (Azure AD) サインインとプロファイルの読み取り
	    - (Exchange) メールの送受信
	    - (SharePoint) 閲覧

> [AZURE.NOTE]既定では、すべてのメンバーがアプリケーションを登録できます。アプリケーションを登録するためのアクセス許可を特定のメンバーに制限する方法については、「[How applications are added to Azure AD (アプリケーションを Azure AD に追加する方法)](active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)」を参照してください。

開発者によるアプリケーションの作成をサポートするために、グローバル管理者として次の操作を行う必要があります。

- アクセス規則を構成する (アクセス ポリシー/MFA)
- ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
- 既定のユーザー同意エクスペリエンスを抑制する

## アクセス規則の構成

既に説明したように、次の記事で、任意のアプリケーションのアクセス規則を構成する方法の詳細を参照してください。

[アクセス規則の構成](active-directory-conditional-access-azuread-connected-apps.md)

## ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる

既定では、ユーザーがアプリケーションにアクセスするためのユーザー割り当ては必要ありません。ただし、アプリケーションがロールを公開している場合、またはアプリケーションをユーザーのアクセス パネルに表示する場合は、ユーザー割り当てが必要であり、ユーザーまたはグループを割り当てる必要があります。

[ユーザー割り当ての要求](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Azure AD Premium または Enterprise Mobility Suite (EMS) のサブスクライバーの場合は、グループを活用することを強くお勧めします。グループをアプリケーションに割り当てると、グループの所有者に継続的にアクセスの管理を委任できます。自分でグループを作成するか、グループ管理機能を使用して組織の責任者にグループの作成を依頼できます。

[アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md) [アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)

## ユーザーの同意の抑制

既定では、ユーザーは、サインインするために必要なアクセス許可に同意する必要があります。アプリケーションへのアクセス許可を付与することを求める同意エクスペリエンスは、そのような決定に慣れていないユーザーの混乱を招く可能性があります。

信頼できるアプリケーションの場合は、組織内のすべてのユーザーに代わってアプリケーションに同意できます。

ユーザーの同意と Azure での同意エクスペリエンスの詳細については、「[Azure Active Directory とアプリケーションの統合](active-directory-integrating-applications.md)」を参照してください 。

<!---HONumber=AcomDC_0128_2016-->