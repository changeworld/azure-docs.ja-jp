---
title: Azure Active Directory の概要 | Microsoft Docs
description: Azure Active Diretory で、ライセンスの取得、ドメイン名の追加、カスタム サインイン ページの作成、セルフサービスによるパスワードのリセットを行います
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: fc6cc88cabab1bc56b0a04ce8778823647c5b2bf
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-azure-ad"></a>Azure AD の概要
最新の ID 管理には、アプリケーションとサービスの可用性を、認証済みユーザーに対してのみ確保できるように、スケーラブルで一貫性のある信頼性が求められています。 ユーザーの ID 管理のニーズに適切に対応するには、IT には、承認済みパブリック SaaS (サービスとしてのソフトウェア) アプリへのアクセスを提供する方法、内部の基幹業務アプリをホストする方法、さらにはオンプレミスのアプリの開発と使用を強化する方法も必要です。 こうした要件はすべて、クラウド ベースの ID 管理ソリューションの必要性を示しています。      

Azure Active Directory (Azure AD) は、マイクロソフトが提供する、マルチテナントに対応したクラウド ベースのディレクトリと ID の管理サービスです。 Azure AD には主要なディレクトリ サービス、高度な ID 管理機能、アプリケーション アクセスの管理機能が統合されています。 Azure AD はマルチテナント型で地理的に分散した高可用性のデザインです。つまり Azure AD は、最も重要なビジネス ニーズでも信頼できるツールです。

Azure AD には、オンプレミスのリソース情報を同期する機能、カスタマイズ可能な会社のブランド、シンプルなライセンス管理、セルフサービスのパスワード管理など、一連の ID 管理機能が用意されています。 こうした機能は簡単にデプロイできるほか、Azure AD を使用して、クラウド ベース アプリケーションのセキュリティ保護、IT プロセスの効率化、コストの削減を実現し、企業のコンプライアンス目標を確実に満たすのに役立ちます。

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

この記事の残りの部分では、Azure AD の使用を開始する方法について説明します。 

## <a name="sign-up-for-azure-active-directory-premium"></a>Azure Active Directory Premium へのサインアップ
[Azure Active Directory (Azure AD) Premium を使用](active-directory-get-started-premium.md)するには、ライセンスを購入のうえ、ご利用の Azure サブスクリプションに関連付けてください。 新しい Azure サブスクリプションを作成する場合は、ライセンス プランと Azure AD サービス アクセスをアクティブにする必要もあります。 

Active Directory Premium にはいくつかの方法でサインアップできます。 

- Azure サブスクリプションまたは Office 365 サブスクリプションを使用する
- Enterprise Mobility + Security ライセンス プランを使用する
- マイクロソフト ボリューム ライセンス プランを使用する

### <a name="verification-step"></a>確認手順
サブスクリプションをアクティブにした後、サービスにログインできることを確認します。

## <a name="add-a-custom-domain-name"></a>カスタム ドメイン名を追加する
Azure AD ディレクトリにはすべて、*domainname*.onmicrosoft.com の形式の初期ドメイン名が付いています。初期ドメイン名は変更も削除もできませんが、[企業ドメイン名を Azure AD に追加することもできます](add-custom-domain.md)。 たとえば、組織には多くの場合、ビジネスを行うために使用する別のドメイン名と、企業ドメイン名を使用してサインインするユーザーが存在します。 Azure AD にカスタム ドメイン名を追加すると、ディレクトリ内で、"alice@contoso.com" のようなユーザーにとって馴染みのあるユーザー名を、 "alice@.onmicrosoft.com" の代わりに割り当てることができます。 この場合のプロセスは単純です。

1. カスタム ドメイン名をディレクトリに追加する
2. ドメイン名レジストラーでドメイン名の DNS エントリを追加する
3. Azure AD でカスタム ドメイン名を検証する

### <a name="verification-step"></a>確認手順
カスタム ドメインを追加した後、Azure AD ポータルの **[カスタム ドメイン名]** ブレードで、そのドメインの状態が **[確認済み]** であることを確認します。

## <a name="add-company-branding-to-your-sign-in-page"></a>サインイン ページに会社のブランドを追加する 
多くの企業は、管理下にある Web サイトとサービスに関して利用者が戸惑わないよう、そのすべての外観に統一感を持たせたいと考えています。 Azure Active Directory (Azure AD) では、[会社のロゴや独自の配色でサインイン ページの外観をカスタマイズ](customize-branding.md)できるようにすることでこれを実現します。 サインイン ページは、Office 365 など、Azure AD を ID プロバイダーとして使用する Web ベースのアプリケーションにサインインするときに表示されるページです。 このページを使用して資格情報を入力します。

### <a name="verification-step"></a>確認手順
Azure Portal にログインし、カスタマイズされたサインイン ページと、追加の言語カスタマイズが正しく構成されていることを確認します。 

## <a name="add-new-users"></a>新しいユーザーを追加する
[組織の Azure AD に新しいユーザーを追加](add-users-azure-active-directory.md)するには、Azure Portal を使用して 1 人ずつ追加するか、オンプレミスの Windows Server AD リソース データを同期します。 Azure AD ポータルから直接クラウド ベースのユーザーを追加するか、オンプレミスのユーザー情報を同期することができます。

Azure AD へのオンプレミスの ID 同期を有効にするには、組織のサーバーに [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) をインストールして構成する必要があります。 このアプリケーションは、既存の ID ソースから Azure AD テナントへのユーザーとグループの同期を処理します。

### <a name="verification-step"></a>確認手順
新しいユーザーを作成および同期した後、そのユーザーが Azure AD に表示されることを確認します。

## <a name="assign-licenses"></a>ライセンスを割り当てる
有料機能を構成するうえで必要なことはサブスクリプションの入手だけですが、引き続き Azure AD Premium の有料機能の[ライセンスはユーザーに割り当てる](license-users-groups.md)必要があります。 Azure AD の有料機能へのアクセスが必要なユーザーや Azure AD の有料機能で管理されているユーザーには、ライセンスを割り当てる必要があります。 ライセンスの割り当てとは、ユーザーと、Azure AD Premium、Basic、Enterprise Mobility + Security などの購入済みのサービスとのマッピングです。

次の例に示すように、グループベースのライセンス割り当てを使用して、規則を設定できます。

- ディレクトリ内のすべてのユーザーが自動的にライセンスを取得する
- 適切な役職を持つすべてのユーザーがライセンスを取得する
- 組織内の他の管理者に決定を委任することができる (セルフサービス グループを使用)

### <a name="verification-step"></a>確認手順
**[Azure Active Directory]**  >  **[ライセンス]**  >  **[すべての製品]** で、割り当てられている利用可能なライセンスを確認します。

## <a name="configure-self-service-password-reset"></a>セルフサービスのパスワードのリセットを構成する
[セルフサービスのパスワードのリセット (SSPR)](authentication/quickstart-sspr.md) は、IT 管理者が、ユーザーによるパスワードやアカウントのリセットまたはロック解除を、簡単に許可できるようにします。 このシステムには、ユーザーがいつシステムを使用したかを追跡する詳細なレポートと、誤用や悪用について警告する通知が用意されています。

### <a name="verification-step"></a>確認手順
**Azure Active Directory**  >  **[パスワードのリセット]** の有効な SSPR プロパティに、適切なユーザーとグループが割り当てられていることを確認します。 


## <a name="next-steps"></a>次の手順
[Azure Active Directory のサービス ページ](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory の価格情報ページ](https://azure.microsoft.com/pricing/details/active-directory/)