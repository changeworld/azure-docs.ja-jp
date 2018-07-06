---
title: Azure Active Directory でのユーザーの追加または削除 | Microsoft Docs
description: Azure Active Directory で新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 01/08/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: e6e21ea09909a3bd92a21e15428af347e3f20b93
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37767474"
---
# <a name="quickstart-add-new-users-to-azure-active-directory"></a>クイック スタート: Azure Active Directory に新しいユーザーを追加する
この記事では、組織のユーザーを組織の Azure Active Directory (Azure AD) テナントで削除または追加する場合に、Azure Portal を使用する方法や、オンプレミスの Windows Server AD ユーザー アカウントのデータを同期する方法を説明します。 

## <a name="add-cloud-based-users"></a>クラウドベースのユーザーを追加する
1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、ディレクトリの全体管理者のアカウントでサインインします。
2. **[Azure Active Directory]** を選択し、**[ユーザーとグループ]** を選択します。
3. **[ユーザーとグループ]** で、**[すべてのユーザー]** を選択し、**[新しいユーザー]** を選択します。
   ![[追加] コマンドの選択](./media/add-users-azure-active-directory/add-user.png)
4. **名前**や**ユーザー名**など、ユーザーの詳細を入力します。 ユーザー名のドメイン名の部分は、既定の初期ドメイン名 "[ドメイン名].onmicrosoft.com"、または検証済みの非フェデレーション [カスタム ドメイン名](add-custom-domain.md) ("contoso.com" など) のいずれかである必要があります。
5. このプロセスの完了後、ユーザーに提供できるように、生成されたユーザー パスワードをコピーするか、メモしておきます。
6. 必要に応じて、ユーザーの **[プロファイル]**、**[グループ]**、または **[ディレクトリ ロール]** を開き、情報を入力します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。
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
* [別のディレクトリからのゲスト ユーザーの追加](../b2b/what-is-b2b.md) 
* [Azure AD でのロールへのユーザーの割り当て](active-directory-users-assign-role-azure-portal.md)
* [ユーザー プロファイルの管理](active-directory-users-profile-azure-portal.md)
* [削除済みユーザーの復元](active-directory-users-restore.md)



## <a name="next-steps"></a>次の手順
このクイック スタートでは、Azure AD Premium に新しいユーザーを追加する方法について説明しました。 

次のリンクを使用して、Azure ポータルから Azure AD 内に新しいユーザーを作成できます。

>[!div class="nextstepaction"]
>[Azure AD へのユーザーの追加](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/)