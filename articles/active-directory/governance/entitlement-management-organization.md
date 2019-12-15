---
title: Azure AD のエンタイトルメント管理で接続されている組織を追加する - Azure Active Directory
description: 自分の組織外のユーザーがアクセス パッケージを要求して、プロジェクトの共同作業を行うことができるようにする方法について説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dec950f5475a8a64cfecfac1fb25246d6a7aa29
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561917"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Azure AD のエンタイトルメント管理で接続されている組織を追加する

Azure AD のエンタイトルメント管理を利用すると、自分の組織外のユーザーと共同作業を行うことができます。 外部 Azure AD ディレクトリまたはドメイン内のユーザーと頻繁に共同作業を行う場合は、それらのユーザーを接続された組織として追加できます。 この記事では、接続された組織を追加して、自分の組織外のユーザーが自分のディレクトリ内のリソースを要求できるようにする方法について説明します。

## <a name="what-is-a-connected-organization"></a>接続されている組織とは

接続されている組織とは、ご自身と関係のある外部 Azure AD ディレクトリまたはドメインです。

たとえば、あなたは Woodgrove Bank で働いていて、2 つの外部組織 Graphic Design Institute および Contoso と共同作業を行う必要があるとします。 あなたは、Graphic Design Institute の担当者から Graphic Design Institute では Azure AD を使用していて、ユーザーのユーザー プリンシパル名が `graphicdesigninstitute.com` で終わることを聞きました。 また、Contoso の担当者から Contoso では Azure AD を使用していないが、ユーザーのユーザー プリンシパル名が `contoso.com` で終わることを聞きました。

2 つの接続された組織を構成できます。1 つはドメインが `graphicdesigninstitute.com` の Graphic Design Institute、もう 1 つはドメインが `contoso.com` の Contoso です。 その後、これらの 2 つの接続された組織をポリシーに追加すると、ポリシーに一致するユーザー プリンシパル名を持つ各組織のユーザーがアクセス パッケージを要求できるようになります。 さらに、Graphic Design Institute では Azure AD を使用していることがわかっているため、Graphic Design Institute が後から `graphicdesigninstitute.example` などのサブドメインを追加した場合、そのユーザー プリンシパル名を持つユーザーも同じポリシーを使用してアクセス パッケージを要求できるようになります。

![接続されている組織の例](./media/entitlement-management-organization/connected-organization-example.png)

Azure AD ディレクトリまたはドメインのユーザーの認証方法は、認証の種類によって異なります。 接続された組織の認証の種類は次のとおりです。

- Azure AD
- [直接フェデレーション](../b2b/direct-federation.md)
- [ワンタイム パスコード](../b2b/one-time-passcode.md) (ドメイン)

接続された組織を追加する方法のデモについては、次のビデオをご覧ください。

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>接続されている組織の追加

外部 Azure AD ディレクトリまたはドメインを接続された組織として追加するには、次の手順に従います。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはゲスト招待元

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで、 **[接続されている組織]** をクリックした後、 **[接続されている組織の追加]** をクリックします。

    ![[Identity Governance] - [接続されている組織] - [接続されている組織の追加]](./media/entitlement-management-organization/connected-organization.png)

1. **[基本]** タブで、組織の表示名と説明を入力します。

    ![[接続されている組織の追加] - [基本] タブ](./media/entitlement-management-organization/organization-basics.png)

1. **[ディレクトリとドメイン]** タブで、 **[ディレクトリとドメインの追加]** をクリックして、[ディレクトリとドメインの選択] ウィンドウを開きます。

1. ドメイン名を入力して、Azure AD ディレクトリまたはドメインを検索します。 ドメイン名全体を入力する必要があります。

1. 提供された名前と認証の種類によって正しい組織であることを確認します。 ユーザーのサインイン方法は、認証の種類によって異なります。

    ![[接続されている組織の追加] - [ディレクトリとドメインの選択]](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. **[追加]** をクリックして、Azure AD ディレクトリまたはドメインを追加します。 現時点では、接続されている組織ごとに Azure AD ディレクトリまたはドメインを 1 つのみ追加できます。

    > [!NOTE]
    > Azure AD ディレクトリまたはドメインのすべてのユーザーはこのアクセス パッケージを要求できます。 これには、ディレクトリに関連付けられている Azure AD 内のすべてのサブドメインのユーザーが含まれます。ただし、それらのドメインが Azure B2B の許可リストまたは拒否リストによってブロックされている場合を除きます。 詳細については、「[B2B ユーザーに対する特定組織からの招待を許可またはブロックする](../b2b/allow-deny-list.md)」を参照してください。

1. Azure AD ディレクトリまたはドメインを追加したら、 **[選択]** をクリックします。

    組織が一覧に表示されます。

    ![[接続されている組織の追加] - [ディレクトリ] タブ](./media/entitlement-management-organization/organization-directory-domain.png)

1. **[スポンサー]** タブで、この接続された組織のオプションのスポンサーを追加します。

    スポンサーとは、この接続された組織との関係の連絡先となる、自分のディレクトリ内に既に存在する内部または外部のユーザーです。 内部スポンサーは、自分のディレクトリ内のメンバー ユーザーです。 外部スポンサーは、以前に招待され、自分のディレクトリ内に既に存在する、接続されている組織のゲスト ユーザーです。 スポンサーは、この接続されている組織内のユーザーがこのアクセス パッケージへのアクセスを要求したときに、承認者として利用できます。 ゲスト ユーザーを自分のディレクトリに招待する方法については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../b2b/add-users-administrator.md)」を参照してください。

    **[追加/削除]** をクリックすると、内部または外部スポンサーを選択するためのウィンドウが表示されます。 このウィンドウには、自分のディレクトリ内のユーザーとグループのフィルター処理されていない一覧が表示されます。

    ![パッケージへのアクセス - ポリシー - [接続された組織の追加] - [スポンサー] タブ](./media/entitlement-management-organization/organization-sponsors.png)

1. **[確認および作成]** タブで自分の組織の設定を確認し、 **[作成]** をクリックします。

    ![パッケージへのアクセス - ポリシー - [接続された組織の追加] - [確認および作成] タブ](./media/entitlement-management-organization/organization-review-create.png)

## <a name="delete-a-connected-organization"></a>接続されている組織の削除

外部の Azure AD ディレクトリまたはドメインとの関係がなくなった場合は、接続されている組織を削除できます。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはゲスト招待元

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで、 **[接続されている組織]** をクリックした後、接続されている組織をクリックして開きます。

1. [概要] ページで、 **[削除]** をクリックして接続されている組織を削除します。

    現時点では、接続されているユーザーがいない場合にのみ、接続されている組織を削除できます。

    ![[Identity Governance] - [接続されている組織] - 接続されている組織の削除](./media/entitlement-management-organization/organization-delete.png)

## <a name="next-steps"></a>次の手順

- [外部ユーザーのアクセス権の管理](entitlement-management-organization.md)
- [自分のディレクトリ内以外のユーザーの場合](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
