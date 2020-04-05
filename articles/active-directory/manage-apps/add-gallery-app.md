---
title: ギャラリー アプリを追加する - Azure Active Directory | Microsoft Docs
description: Azure AD ギャラリーから Azure エンタープライズ アプリケーションにアプリを追加する方法について説明します。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 10/29/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: af0a826c499790c86a20ea7061075a6c3e66b3a4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "73062566"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Azure AD 組織にギャラリー アプリを追加する

Azure Active Directory (Azure AD) には、Enterprise Single Sign-on に対応した何千もの事前統合されたアプリケーションを含むギャラリーがあります。 この記事では、ギャラリーから Azure AD 組織にアプリを追加するための一般的な手順について説明します。

> [!IMPORTANT]
> まず、[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)で、自分のアプリを確認します。 追加したいギャラリー アプリを追加および構成するためのステップ バイ ステップのガイダンスが見つかるでしょう。

## <a name="add-a-gallery-application"></a>ギャラリー アプリケーションの追加

1. Azure AD テナントの全体管理者、クラウド アプリケーション管理者、またはアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

1. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](media/add-gallery-app/open-enterprise-apps.png)


3. テナントにギャラリー アプリを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択してテナントにギャラリー アプリケーションを追加](media/add-gallery-app/new-application.png)

 4. 新しいギャラリー プレビュー エクスペリエンスに切り替えます。 **[アプリケーションの追加]** ページの上部にあるバナーで、 **[Click here to try out the new and improved app gallery] (改善された新しいアプリ ギャラリーを試すには、こちらをクリックしてください)** というリンクを選択します。

5. **[Azure AD ギャラリーの参照]** ペインが開き、クラウド プラットフォーム、オンプレミス アプリケーション、および注目のアプリケーションのタイルが表示されます。 なお、 **[注目のアプリケーション]** セクションに一覧表示されているアプリケーションには、フェデレーション シングル サインオン (SSO) とプロビジョニングをサポートしているかどうかを示すアイコンがあります。

    ![名前またはカテゴリーでアプリを検索する](media/add-gallery-app/browse-gallery.png)

6. 追加するアプリケーションをギャラリーで参照するか、検索ボックスに名前を入力してアプリケーションを検索します。 次に、結果からアプリケーションを選択します。 (必要な場合) フォームで、組織のニーズに合わせてアプリケーションの名前を編集できます。

    ![ギャラリーからアプリケーションを追加する方法を示します](media/add-gallery-app/create-application.png)

7. **［作成］** を選択します [作業の開始] ページが表示され、組織のアプリケーションを構成するためのオプションが表示されます。

## <a name="configure-user-sign-in-properties"></a>ユーザーのサインイン プロパティを構成する

1. **[プロパティ]** を選択して、編集用のプロパティ ウィンドウを開きます。

    ![プロパティの編集ウィンドウ](media/add-gallery-app/edit-properties.png)

1. 以下のオプションを設定して、アプリケーションに割り当てられているユーザーまたは割り当てられていないユーザーがアプリケーションにサインインする方法と、ユーザーがアクセス パネルでアプリケーションを表示できるかどうかを決定します。

    - **[ユーザーのサインインが有効になっていますか?]** は、アプリケーションに割り当てられているユーザーがサインインできるかどうかを決定します。
    - **[ユーザーの割り当てが必要ですか?]** は、アプリケーションに割り当てられていないユーザーがサインインできるかどうかを決定します。
    - **[ユーザーに表示しますか?]** は、アプリケーションに割り当てられているユーザーのアクセス パネルと O365 ランチャーにアプリケーションを表示するかどうかを決定します。

      **割り当てられている**ユーザーの動作:

       | アプリケーション プロパティの設定 | | | 割り当てられているユーザーのエクスペリエンス | |
       |---|---|---|---|---|
       | ユーザーのサインインが有効になっていますか? | ユーザーの割り当てが必要ですか? | ユーザーに表示しますか? | 割り当てられているユーザーはサインインできますか? | 割り当てられているユーザーにアプリケーションが表示されますか?* |
       | はい | はい | はい | はい | はい  |
       | はい | はい | no  | はい | no   |
       | はい | no  | はい | はい | はい  |
       | はい | no  | no  | はい | no   |
       | no  | はい | はい | no  | no   |
       | no  | はい | no  | no  | no   |
       | no  | no  | はい | no  | no   |
       | no  | no  | no  | no  | no   |

      **割り当てられていない**ユーザーの動作:

       | アプリケーション プロパティの設定 | | | 割り当てられていないユーザーのエクスペリエンス | |
       |---|---|---|---|---|
       | ユーザーのサインインが有効になっていますか? | ユーザーの割り当てが必要ですか? | ユーザーに表示しますか? | 割り当てられていないユーザーはサインインできますか? | 割り当てられていないユーザーにアプリケーションが表示されますか?* |
       | はい | はい | はい | no  | no   |
       | はい | はい | no  | no  | no   |
       | はい | no  | はい | はい | no   |
       | はい | no  | no  | はい | no   |
       | no  | はい | はい | no  | no   |
       | no  | はい | no  | no  | no   |
       | no  | no  | はい | no  | no   |
       | no  | no  | no  | no  | no   |

     \* ユーザーのアクセス パネルと Office 365 アプリ ランチャーにアプリケーションが表示されますか?

1. カスタム ロゴを使用するには、215 x 215 ピクセルのロゴを作成し、それを PNG 形式で保存します。 その後、ロゴを参照し、アップロードします。

    ![ロゴを変更する](media/add-gallery-app/change-logo.png)

1. 完了したら、 **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

Azure AD 組織にアプリケーションを追加したので、使用する[シングル サインオン方法を選択](what-is-single-sign-on.md#choosing-a-single-sign-on-method)し、次の該当する記事を参照します。

- [SAML ベースのシングル サインオンの構成](configure-single-sign-on-non-gallery-applications.md)
- [パスワード シングル サインオンの構成](configure-password-single-sign-on-non-gallery-applications.md)
- [リンクされたサインオンの構成](configure-linked-sign-on.md)

