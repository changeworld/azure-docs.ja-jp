---
title: 'チュートリアル: Azure Active Directory と YouEarnedIt の統合 | Microsoft Docs'
description: Azure Active Directory と YouEarnedIt の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: jeedes
ms.openlocfilehash: 46e113641b292adf8072c3fe798a331416a6eef9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323843"
---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>チュートリアル: Azure Active Directory と YouEarnedIt の統合

このチュートリアルでは、YouEarnedIt と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と YouEarnedIt を統合すると、次のことができます。

* YouEarnedIt にアクセスできる Azure AD ユーザーで制御します。
* ユーザーが自分の Azure AD アカウントを使用して YouEarnedIt に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* YouEarnedIt でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* YouEarnedIt では、**SP** によって開始される SSO がサポートされます。

## <a name="add-youearnedit-from-the-gallery"></a>ギャラリーからの YouEarnedIt の追加

Azure AD への YouEarnedIt の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に YouEarnedIt を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、 **[YouEarnedIt]** と入力します。
1. 結果パネルから **[YouEarnedIt]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-youearnedit"></a>YouEarnedIt の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、YouEarnedIt で Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと YouEarnedIt の関連ユーザーとの間にリンク関係を確立する必要があります。

YouEarnedIt を使用した Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[YouEarnedIt SSO の構成](#configure-youearnedit-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[YouEarnedIt のテスト ユーザーの作成](#create-youearnedit-test-user)** - YouEarnedItで B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **YouEarnedIt** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[識別子]** テキストボックスに、次のいずれかのパターンで値を入力します。

    | 環境  | Pattern  |
    |:--- |:--- |
    | Production | `<company name>.youearnedit.com` |
    | サンドボックス  |`<company name>.sandbox.youearnedit.com` |

    b. **[サインオン URL]** ボックスに、次のいずれかの形式で URL を入力します。

    | 環境  | Pattern  |
    |:--- |:--- |
    | Production | `https://<company name>.youearnedit.com/users/sign_in` |
    | サンドボックス  |`https://<company name>.sandbox.youearnedit.com/users/sign_in` |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の識別子とサインオン URL で更新してください。 これらの値は、担当の YouEarnedIt 顧客対応マネージャーにお問い合わせください。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Set up YouEarnedIt]\(YouEarnedIt のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に YouEarnedIt へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[YouEarnedIt]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-youearnedit-sso"></a>YouEarnedIt SSO の構成

**YouEarnedIt** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と、コピーした当該 URL を Azure portal から担当の YouEarnedIt 顧客対応マネージャーに送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-youearnedit-test-user"></a>YouEarnedIt テスト ユーザーの作成

このセクションでは、YouEarnedIt で Britta Simon というユーザーを作成します。 担当の YouEarnedIt 顧客対応マネージャーと連携して、YouEarnedIt プラットフォームにユーザーを追加します。

> [!NOTE]
> YouEarnedIt は、ID プロバイダーが NameID 属性の EmailAddress または UserName を提供することを求めています。 対応する UserName または EmailAddress がデータベース内に見つからないか、正確に一致しない場合は、認証に失敗します。 その場合は、SSO 統合前に、YouEarnedIt システムにアカウントをインポートする必要があります (通常、API または CSV インポートを使用します)。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる YouEarnedIt のサインオン URL にリダイレクトされます。 

* YouEarnedIt のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [YouEarnedIt] タイルをクリックすると、YouEarnedIt のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

YouEarnedIt を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
