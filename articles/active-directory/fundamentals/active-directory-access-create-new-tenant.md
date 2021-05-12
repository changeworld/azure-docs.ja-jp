---
title: クイックスタート - Azure AD にアクセスして新しいテナントを作成する - Azure AD
description: Azure Active Directory の検索方法と、組織の新しいテナントの作成方法に関する手順。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0c5f342fccf2f313777961e803643daee5b91a1
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2021
ms.locfileid: "109517246"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>クイック スタート:Azure Active Directory で新しいテナントを作成する

Azure Active Directory (Azure AD) ポータルを使用して、組織の新しいテナントの作成など、すべての管理タスクを実行できます。 

このクイック スタートでは、Azure portal と Azure Active Directory にアクセスする方法と、組織の基本的なテナントを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-a-new-tenant-for-your-organization"></a>組織の新しいテナントを作成する

Azure portal にサインインすると、組織の新しいテナントを作成できます。 新しいテナントは組織を表し、社内外のユーザー向けに特定インスタンスの Microsoft クラウド サービスを管理するために役立ちます。

### <a name="to-create-a-new-tenant"></a>新しいテナントを作成するには

1. 組織の [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure portal メニューから **[Azure Active Directory]** を選択します。  

    <kbd>![Azure Active Directory - [概要] ページ - テナントの作成](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. **[Create a tenant]\(テナントの作成\)** を選択します。

1. [基本] タブで、作成するテナントの種類を選択します ( **[Azure Active Directory]** または **[Azure Active Directory (B2C)]** )。

1. **Next:Configuration\(次へ: 構成\)** を選択して 構成 タブに移動します。

    <kbd>![Azure Active Directory - [Create a tenant]\(テナントの作成\) ページ - [構成] タブ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  [構成] タブで、次の情報を入力します。
    
    - **[組織名]** ボックスに「_Contoso Organization_」と入力します。

    - **[初期ドメイン名]** ボックスに「_Contosoorg_」と入力します。

    - **[国またはリージョン]** ボックスは _[米国]_ オプションのままにします。

1. **確認と作成** を選択します。 入力した情報を確認し、誤りがなければ **[作成]** を選択します。

    <kbd>![Azure Active Directory - テナントの確認と作成のためのページ](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

新しいテナントは、ドメイン contoso.onmicrosoft.com で作成されます。

## <a name="your-user-account-in-the-new-tenant"></a>新しいテナントのユーザー アカウント

新しい Azure AD テナントを作成したユーザーが、そのテナントの最初のユーザーになります。 最初のユーザーには、[グローバル管理者](../roles/permissions-reference.md#global-administrator)ロールが自動的に割り当てられます。 [ **[ユーザー]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) ページに移動して、ユーザー アカウントを確認します。

既定では、テナントの[技術部連絡先](/microsoft-365/admin/manage/change-address-contact-and-more#what-do-these-fields-mean)としても表示されます。 技術部連絡先の情報は、[ **[プロパティ]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) で変更できます。

> [!WARNING]
> ディレクトリに、グローバル管理者特権が割り当てられているアカウントが少なくとも 2 つあることを確認します。 これは、グローバル管理者のどちらかがロックアウトされた場合に役立ちます。詳細については、「[Azure AD で緊急アクセス用管理者アカウントを管理する](../roles/security-emergency-access.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用してテナントを削除することができます。

- Azure portal で **[ディレクトリ + サブスクリプション]** フィルターを使用して、削除するディレクトリにサインインしていることを確認します。 必要に応じてターゲット ディレクトリに切り替えます。
- **[Azure Active Directory]** を選択し、**[Contoso - 概要]** ページで **[ディレクトリの削除]** を選択します。

    テナントとその関連情報は削除されます。

    <kbd>![概要ページ。[ディレクトリの削除] ボタンが強調表示されている](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>次のステップ

- 追加のドメイン名を変更または追加する方法については、[Azure Active Directory にカスタム ドメイン名を追加する方法](add-custom-domain.md)に関するページを参照してください。

- ユーザーの追加については、[新しいユーザーの追加または削除](add-users-azure-active-directory.md)に関するページを参照してください。

- グループとメンバーの追加については、[基本的なグループの作成とメンバーの追加](active-directory-groups-create-azure-portal.md)に関するページを参照してください。

- 組織のアプリケーションとリソース アクセスの管理方法については、[Privileged Identity Management を使用したロールベースのアクセス](../../role-based-access-control/best-practices.md)と[条件付きのアクセス](../../role-based-access-control/conditional-access-azure-management.md)に関するページを参照してください。

- [基本的なライセンスの情報、用語、関連機能](active-directory-whatis.md)など、Azure AD について学習します。