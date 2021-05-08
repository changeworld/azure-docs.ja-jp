---
title: 'チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合 | Microsoft Docs'
description: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218721"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>チュートリアル: Azure Active Directory と Five9 Plus Adapter (CTI、Contact Center Agents) の統合

このチュートリアルでは、Five9 Plus Adapter (CTI、Contact Center Agents) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Five9 Plus Adapter (CTI、Contact Center Agents) を Azure AD と統合すると、次のことができます。

* Five9 Plus Adapter (CTI、Contact Center Agents) にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Five9 Plus Adapter (CTI、Contact Center Agents) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Five9 Plus Adapter (CTI、Contact Center Agents) でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Five9 Plus Adapter (CTI、Contact Center Agents) では、**IDP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>ギャラリーからの Five9 Plus Adapter (CTI、Contact Center Agents) の追加

Azure AD への Five9 Plus Adapter (CTI、Contact Center Agents) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Five9 Plus Adapter (CTI、Contact Center Agents) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Five9 Plus Adapter (CTI, Contact Center Agents)** 」と入力します。
1. 結果パネルで **[Five9 Plus Adapter (CTI, Contact Center Agents)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Five9 Plus Adapter (CTI、Contact Center Agents) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Five9 Plus Adapter (CTI、Contact Center Agents) に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Five9 Plus Adapter (CTI、Contact Center Agents) の関連ユーザーとの間にリンク関係を確立する必要があります。

Five9 Plus Adapter (CTI、Contact Center Agents) に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Five9 Plus Adapter (CTI、Contact Center Agents) の SSO の構成](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** - Five9 Plus Adapter (CTI、Contact Center Agents) で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Five9 Plus Adapter (CTI、Contact Center Agents)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[識別子]** ボックスに、次のいずれかの URL を入力します。
    
    |    環境      |       URL      |
    | :-- | :-- |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. **[応答 URL]** ボックスに、次のいずれかの URL を入力します。

    |      環境     |      URL      |
    | :--                  | :--           |
    | "Five9 Plus Adapter for Microsoft Dynamics CRM" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 Plus Adapter for Zendesk" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 Plus Adapter for Agent Desktop Toolkit" の場合 | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Five9 Plus Adapter (CTI、Contact Center Agents) の設定]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Five9 Plus Adapter (CTI, Contact Center Agents)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Five9 Plus Adapter (CTI、Contact Center Agents) の SSO の構成

1. **Five9 Plus Adapter (CTI、Contact Center Agents)** 側にシングル サインオンを構成するには、ダウンロードされた **証明書 (Base64)** と適切な URL を [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)に送信する必要があります。 また、SSO をさらに構成するために、アダプターに従って以下の手順のようにしてください。

    a. "Five9 Plus Adapter for Agent Desktop Toolkit" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus Adapter for Microsoft Dynamics CRM" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus Adapter for Zendesk" 管理ガイド: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus Adapter (CTI、Contact Center Agents) テスト ユーザーの作成

このセクションでは、Five9 Plus Adapter (CTI、Contact Center Agents) で Britta Simon というユーザーを作成します。 [Five9 Plus Adapter (CTI、Contact Center Agents) サポート チーム](https://www.five9.com/about/contact)と協力し、Five9 Plus Adapter (CTI、Contact Center Agents) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックします。すると、SSO を設定した Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインインします。

* Microsoft マイ アプリを使用することができます。 マイ アプリで Five9 Plus Adapter (CTI、Contact Center Agents) のタイルをクリックすると、SSO を設定した Five9 Plus Adapter (CTI、Contact Center Agents) に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Five9 Plus Adapter (CTI、Contact Center Agents) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
