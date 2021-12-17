---
title: 'チュートリアル: Azure Active Directory と ZIVVER の統合 | Microsoft Docs'
description: Azure Active Directory と ZIVVER の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: jeedes
ms.openlocfilehash: e007f6026d402fd144e880238cd69df356977f1d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332499"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>チュートリアル: Azure Active Directory と ZIVVER の統合

このチュートリアルでは、ZIVVER と Azure Active Directory (Azure AD) を統合する方法について説明します。 ZIVVER を Azure AD と統合すると、次のことを実行できます。

* ZIVVER にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントで ZIVVER に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Azure AD と ZIVVER の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ZIVVER のシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ZIVVER では、**IDP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-zivver-from-the-gallery"></a>ギャラリーから ZIVVER を追加する

Azure AD への ZIVVER の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ZIVVER を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**ZIVVER**」と入力します。
1. 結果のパネルから **[ZIVVER]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zivver"></a>ZIVVER のための Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ZIVVER 用に Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと ZIVVER の関連ユーザーとの間にリンク関係を確立する必要があります。

ZIVVER 用に Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ZIVVER SSO の構成](#configure-zivver-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ZIVVER のテスト ユーザーの作成](#create-zivver-test-user)** - ZIVVER で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ZIVVER** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    **[識別子]** ボックスに、`https://app.zivver.com/SAML/Zivver` という URL を入力します。

5. ZIVVER アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 ZIVVER アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、**[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

6. その他に、ZIVVER アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | 名前 | 名前空間 | ソース属性 |
    | ---------------|---------| ---------- |
    | ZivverAccountKey | https:\//zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >オンプレミスの Active Directory と Azure AD Connect Tool のハイブリッド セットアップを使用している場合、VALUE を `user.objectGUID` に設定する必要があります

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](common/new-save-attribute.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[保存]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして **フェデレーション メタデータ XML** をダウンロードします。次に、**コピー** アイコンをクリックして、要件に適した特定のオプションの **アプリのフェデレーション メタデータ URL** をコピーして、コンピューターに保存します。

    ![証明書 URL のダウンロードのリンク](./media/zivver-tutorial/metadataxmlurl.png)

8. **[ZIVVER の設定]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に ZIVVER へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[ZIVVER]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zivver-sso"></a>ZIVVER SSO を構成する

1. 別の Web ブラウザー ウィンドウで、ZIVVER 企業[サイト](https://app.zivver.com/login)に管理者としてサインインします。

2. ブラウザー ウィンドウの左下にある **組織設定** アイコンをクックします。

3. **[Single sign-on]\(シングル サインオン\)** に移動します。

4. Azure portal からダウンロードしたフェデレーション メタデータ XML ファイルを開きます。

5. **[Identity Provider metadata URL]\(ID プロバイダー メタデータ URL\)** テキスト ボックスに、前に Azure portal から保存した **アプリ フェデレーション メタデータ URL** を貼り付けます。

6. チェックボックス **[Turn on SSO]\(SSO をオンにする\)** をオンにします。

7. **[保存]** をクリックします。

### <a name="create-zivver-test-user"></a>ZIVVER テスト ユーザーの作成

このセクションでは、ZIVVER で Britta Simon というユーザーを作成します。 [ZIVVER サポート チーム](https://support.zivver.com/)と連携し、ZIVVER プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した ZIVVER に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [ZIVVER] タイルをクリックすると、SSO を設定した ZIVVER に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ZIVVER を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
