---
title: クイック スタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する
description: このクイック スタートでは、Azure portal を使用して、Azure Active Directory (Azure AD) テナントにギャラリー アプリケーションを追加します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 10/29/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044c55e64c6532bf08d31b2c052e4ee9f5069ff1
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956102"
---
# <a name="quickstart-add-an-application-to-your-azure-active-directory-azure-ad-tenant"></a>クイック スタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する

Azure Active Directory (Azure AD) には、何千もの事前統合されたアプリケーションを含むギャラリーがあります。 組織で使用しているアプリケーションの多くは、おそらく既にギャラリーにあります。 このクイックスタートでは、Azure portal を使用して、Azure AD テナントにギャラリー アプリケーションを追加します。 このクイックスタートでは、既にギャラリーに含まれているアプリの追加に焦点を当てます。 ギャラリーに含まれていないアプリも Azure AD と統合できますが、このクイックスタートでは説明されていません。 

アプリケーションを Azure AD テナントに追加すると、次のことが可能になります。

- アプリ用にプロパティを構成する。
- 条件付きアクセス ポリシーを使用してアプリへのユーザー アクセスを管理する。
- ユーザーが Azure AD の資格情報でアプリにサインインできるように、シングル サインオンを構成する。

## <a name="prerequisites"></a>前提条件

Azure AD テナントにアプリケーションを追加するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- (省略可能: [アプリの表示](view-applications-portal.md)の完了)。

>[!IMPORTANT]
>このクイックスタートの手順をテストする場合は、非運用環境を使用することをお勧めします。

## <a name="add-an-app-to-your-azure-ad-tenant"></a>Azure AD テナントにアプリを追加する

Azure AD テナントにギャラリー アプリケーションを追加するには:

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
2. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 **[すべてのアプリケーション]** ウィンドウが開き、Azure AD テナントのアプリケーションのランダム サンプルが表示されます。
3. **[エンタープライズ アプリケーション]** ペインで、 **[新しいアプリケーション]** を選択します。 
    ![[新しいアプリケーション] を選択して、テナントにギャラリー アプリケーションを追加する](media/add-application-portal/new-application.png)
4. 新しいギャラリー プレビュー エクスペリエンスに切り替えます。 **[アプリケーションの追加]** ページの上部にあるバナーで、 **[Click here to try out the new and improved app gallery] (改善された新しいアプリ ギャラリーを試すには、こちらをクリックしてください)** というリンクを選択します。
5. **[Azure AD ギャラリーの参照]** (プレビュー) ペインが開き、クラウド プラットフォーム、オンプレミス アプリケーション、および注目のアプリケーションのタイルが表示されます。 **[注目のアプリケーション]** セクションに一覧表示されているアプリケーションには、フェデレーション シングル サインオン (SSO) とプロビジョニングをサポートしているかどうかを示すアイコンがあります。
    ![名前またはカテゴリーでアプリを検索する](media/add-application-portal/browse-gallery.png)
6. 追加するアプリケーションはギャラリーで参照できます。または、検索ボックスに名前を入力してアプリケーションを検索します。 次に、結果からアプリケーションを選択します。 フォームでは、組織のニーズに合わせてアプリケーションの名前を編集できます。 この例では、名前を **GitHub-test** に変更しました。
    ![ギャラリーからのアプリケーションの追加方法が示されている](media/add-application-portal/create-application.png)
7. **［作成］** を選択します [作業の開始] ページが表示され、組織のアプリケーションを構成するためのオプションが表示されます。

アプリケーションの追加が完了しました。 次のクイックスタートでは、アプリケーションのロゴを変更し、他のプロパティを編集する方法を示します。

## <a name="next-steps"></a>次のステップ

- [アプリを構成する](add-application-portal-configure.md)
- [シングル サインオンを設定する](add-application-portal-setup-sso.md)
- [アプリを削除する](delete-application-portal.md)