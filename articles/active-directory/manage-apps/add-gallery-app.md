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
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bdf275bbafa9c46cfc4577ac2843da0be74c7ef
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477284"
---
# <a name="add-a-gallery-app-to-your-azure-ad-organization"></a>Azure AD 組織にギャラリー アプリを追加する

Azure Active Directory (Azure AD) には、Enterprise Single Sign-on に対応した何千もの事前統合されたアプリケーションを含むギャラリーがあります。 この記事では、ギャラリーから Azure AD 組織にアプリを追加するための一般的な手順について説明します。

> [!IMPORTANT]
> まず、[SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)で、自分のアプリを確認します。 追加したいギャラリー アプリを追加および構成するためのステップ バイ ステップのガイダンスが見つかるでしょう。

## <a name="add-a-gallery-application"></a>ギャラリー アプリケーションの追加

1. Azure AD テナントの全体管理者、クラウド アプリケーション管理者、またはアプリケーション管理者として [Azure portal](https://portal.azure.com) にサインインします。

1. [Azure portal](https://portal.azure.com) の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

1. **[Azure Active Directory]** ウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。

    ![エンタープライズ アプリケーションを開く](media/add-application-portal/open-enterprise-apps.png)

1. **[新しいアプリケーション]** を選択します。

    ![新規アプリケーション](media/add-application-portal/new-application.png)

1. **[ギャラリーから追加する]** の下の検索ボックスに、追加するアプリケーションの名前を入力します。 

    ![名前またはカテゴリによる検索](media/add-application-portal/categories.png)

1. 結果からアプリケーションを選択します。

1. (オプション) アプリケーション固有のフォームで、組織のニーズに合わせてアプリケーションの名前を編集できます。

1. **[追加]** を選択します。 アプリケーションの **[概要]** ページが開かれます。

## <a name="configure-user-sign-in-properties"></a>ユーザーのサインイン プロパティを構成する

1. **[プロパティ]** を選択して、編集用のプロパティ ウィンドウを開きます。

    ![プロパティの編集ウィンドウ](media/add-application-portal/edit-properties.png)

1. 以下のオプションを設定して、アプリケーションに割り当てられているユーザーまたは割り当てられていないユーザーがアプリケーションにサインインする方法と、ユーザーがアクセス パネルでアプリケーションを表示できるかどうかを決定します。

    - **[ユーザーのサインインが有効になっていますか?]** は、アプリケーションに割り当てられているユーザーがサインインできるかどうかを決定します。
    - **[ユーザーの割り当てが必要ですか?]** は、アプリケーションに割り当てられていないユーザーがサインインできるかどうかを決定します。
    - **[ユーザーに表示しますか?]** は、アプリケーションに割り当てられているユーザーのアクセス パネルと O365 ランチャーにアプリケーションを表示するかどうかを決定します。

      **割り当てられている**ユーザーの動作:

       | アプリケーション プロパティの設定 | | | 割り当てられているユーザーのエクスペリエンス | |
       |---|---|---|---|---|
       | ユーザーのサインインが有効になっていますか? | ユーザーの割り当てが必要ですか? | ユーザーに表示しますか? | 割り当てられているユーザーはサインインできますか? | 割り当てられているユーザーにアプリケーションが表示されますか?* |
       | はい | はい | はい | はい | はい  |
       | はい | はい | ×  | はい | ×   |
       | はい | ×  | はい | はい | はい  |
       | はい | ×  | ×  | はい | ×   |
       | ×  | はい | はい | ×  | ×   |
       | ×  | はい | ×  | ×  | ×   |
       | ×  | ×  | はい | ×  | ×   |
       | ×  | ×  | ×  | ×  | ×   |

      **割り当てられていない**ユーザーの動作:

       | アプリケーション プロパティの設定 | | | 割り当てられていないユーザーのエクスペリエンス | |
       |---|---|---|---|---|
       | ユーザーのサインインが有効になっていますか? | ユーザーの割り当てが必要ですか? | ユーザーに表示しますか? | 割り当てられていないユーザーはサインインできますか? | 割り当てられていないユーザーにアプリケーションが表示されますか?* |
       | はい | はい | はい | ×  | ×   |
       | はい | はい | ×  | ×  | ×   |
       | はい | ×  | はい | はい | ×   |
       | はい | ×  | ×  | はい | ×   |
       | ×  | はい | はい | ×  | ×   |
       | ×  | はい | ×  | ×  | ×   |
       | ×  | ×  | はい | ×  | ×   |
       | ×  | ×  | ×  | ×  | ×   |

     \* ユーザーのアクセス パネルと Office 365 アプリ ランチャーにアプリケーションが表示されますか?

1. カスタム ロゴを使用するには、215 x 215 ピクセルのロゴを作成し、それを PNG 形式で保存します。 その後、ロゴを参照し、アップロードします。

    ![ロゴを変更する](media/add-application-portal/change-logo.png)

1. 完了したら、 **[保存]** をクリックします。

## <a name="next-steps"></a>次の手順

Azure AD 組織にアプリケーションを追加したので、使用する[シングル サインオン方法を選択](what-is-single-sign-on.md#choosing-a-single-sign-on-method)し、以下の該当する記事を参照します。

- [SAML ベースのシングル サインオンの構成](configure-single-sign-on-non-gallery-applications.md)
- [パスワード シングル サインオンの構成](configure-password-single-sign-on-non-gallery-applications.md)
- [リンクされたサインオンの構成](configure-linked-sign-on.md)

