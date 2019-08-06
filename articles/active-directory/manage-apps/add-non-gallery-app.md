---
title: ギャラリー以外のアプリケーションの追加 - Microsoft ID プラットフォーム | Microsoft Docs
description: Azure AD テナントにギャラリー以外のアプリケーションを追加します。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 06/18/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: db8d8d6df16c5df7e29d8bb870c5d5eda6d8a2d3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68477259"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>一覧にない (ギャラリー以外の) アプリケーションを Azure AD 組織に追加する

[Azure AD アプリケーション ギャラリー](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)の選択項目に加えて、**ギャラリー以外のアプリケーション**を追加することもできます。 組織に既に存在するアプリケーションや、Azure AD ギャラリーに属していないベンダーのサードパーティ アプリケーションを追加できます。 [使用許諾契約書](https://azure.microsoft.com/pricing/details/active-directory/)に応じて、以下の機能を使用することができます。

- [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0) ID プロバイダーをサポートする任意のアプリケーションのセルフサービス統合 (SP または IdP によって開始)
- [パスワードベースの SSO](what-is-single-sign-on.md#password-based-sso)
- [ユーザー プロビジョニング用の System for Cross-Domain Identity Management (SCIM) プロトコル](use-scim-to-provision-users-and-groups.md)を使用するアプリケーションのセルフサービス接続
- [Office 365 アプリ ランチャー](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/)または [Azure AD アクセス パネル](what-is-single-sign-on.md#linked-sign-on)での任意のアプリケーションへのリンクの追加機能

この記事では、コードを作成せずに Azure portal で**エンタープライズ アプリケーション**にギャラリー以外のアプリケーションを追加する方法について説明します。 カスタム アプリケーションと Azure AD を統合する方法に関する開発者向けガイダンスをお探しの場合は、[Azure AD の認証シナリオ](../develop/authentication-scenarios.md)に関するページを参照してください。 [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) などの最新のプロトコルを使用してユーザーを認証するアプリを開発した場合は、それを Azure portal の[アプリの登録](../develop/quickstart-register-app.md)エクスペリエンスを使用して Microsoft ID プラットフォームに登録できます。

## <a name="add-a-non-gallery-application"></a>ギャラリー以外のアプリケーションを追加する

1. お使いの Microsoft ID プラットフォーム管理者アカウントを使用して、[Azure Active Directory ポータル](https://aad.portal.azure.com/)にサインインします。
1. **[エンタープライズ アプリケーション]**  >  **[新しいアプリケーション]** の順に選択します。
2. (省略可能だが推奨) **[ギャラリーから追加する]** 検索ボックスに、アプリケーションの表示名を入力します。 検索結果にアプリケーションが表示されたら、それを選択し、この手順の残りをスキップします。
3. **[ギャラリー以外のアプリケーション]** を選択します。 **[独自のアプリケーションの追加]** ページが表示されます。

   ![[アプリケーションの追加]](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. 新しいアプリケーションの表示名を入力します。
6. **[追加]** を選択します。 アプリケーションの **[概要]** ページが開きます。

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

Azure AD 組織にアプリケーションを追加したので、使用する[シングル サインオン方法を選択](what-is-single-sign-on.md#choosing-a-single-sign-on-method)し、次の該当する記事を参照します。

- [SAML ベースのシングル サインオンの構成](configure-single-sign-on-non-gallery-applications.md)
- [パスワード シングル サインオンの構成](configure-password-single-sign-on-non-gallery-applications.md)
- [リンクされたサインオンの構成](configure-linked-sign-on.md)
