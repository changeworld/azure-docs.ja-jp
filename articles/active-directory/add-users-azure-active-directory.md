---
title: "Azure Active Directory でのユーザーの追加または削除 | Microsoft Docs"
description: "Azure Active Directory で新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 3df0324cae65418f35743b6543753c405dee01d0
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>クイック スタート: Azure Active Directory に新しいユーザーを追加する
この記事では、Azure ポータルを使用して組織の新しいユーザーを Azure Active Directory (Azure AD) に 1 度に追加するか、オンプレミスの Windows Server AD ユーザー アカウントのデータを同期することによって追加する方法を説明します。 

## <a name="add-cloud-based-users"></a>クラウドベースのユーザーを追加する
1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントでサインインします。
2. **[Azure Active Directory]**を選択し、**[ユーザーとグループ]** を選択します。
3. **[ユーザーとグループ]** で、**[すべてのユーザー]** を選択し、**[新しいユーザー]** を選択します。
   ![[追加] コマンドの選択](./media/add-users-azure-active-directory/add-user.png)
4. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 "[ドメイン名].onmicrosoft.com"、または検証済みの非フェデレーション [カスタム ドメイン名](add-custom-domain.md) ("contoso.com" など) のいずれかである必要があります。
5. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
6. 必要に応じて、ユーザーの **[プロファイル]**、**[グループ]**、または **[ディレクトリ ロール]** を開き、情報を入力します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。
7. **[ユーザー]** で、**[作成]** を選択します。
8. ユーザーがサインインできるように、新しいユーザーに生成されたパスワードを安全に配布します。

> [!TIP]
> オンプレミスの Windows Server AD からユーザー アカウントのデータを同期することもできます。 Microsoft の ID ソリューションでは、オンプレミスとクラウドを基盤とする機能を利用する際に、場所に関係なく、1 つのユーザー ID ですべてのリソースの認証と権限付与を行います。 これをハイブリッド ID と呼んでいます。 ハイブリッド ID シナリオ用に、[Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) を使用してオンプレミスのディレクトリを Azure Active Directory と統合できます。 Office 365、Azure、SaaS など Azure AD と連動するアプリケーションに関して、ユーザーの ID を共通化することができます。 

## <a name="delete-users-from-azure-ad"></a>Azure AD からユーザーを削除する
1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントでサインインします。
2. **[ユーザーとグループ]** を選択します。
3. **[ユーザーとグループ]** ブレードで、削除するユーザーを一覧から選択します。 
4. 選択したユーザーのブレードで、**[概要]** を選択し、コマンド バーの **[削除]** をクリックします。
   ![[追加] コマンドの選択](./media/add-users-azure-active-directory/delete-user.png)


### <a name="learn-more"></a>詳細情報 
* [別のディレクトリからのゲスト ユーザーの追加](active-directory-b2b-what-is-azure-ad-b2b.md) 

* [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)

## <a name="next-steps"></a>次のステップ
このクイック スタートでは、Azure AD Premium に新しいユーザーを追加する方法について説明しました。 

次のリンクを使用して、Azure ポータルから Azure AD 内に新しいユーザーを作成できます。

> [!div class="nextstepaction"]
> [Azure AD へのユーザーの追加](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) 