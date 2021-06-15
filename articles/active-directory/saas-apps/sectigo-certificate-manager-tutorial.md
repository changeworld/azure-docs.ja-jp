---
title: チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合 | Microsoft Docs
description: Azure Active Directory と Sectigo Certificate Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2021
ms.author: jeedes
ms.openlocfilehash: 05070571a4c8a1b673ff145a9449c9e744718026
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891430"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>チュートリアル:Azure Active Directory と Sectigo Certificate Manager の統合

このチュートリアルでは、Sectigo Certificate Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Sectigo Certificate Manager を統合すると、次のことができます。

* Sectigo Certificate Manager にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Sectigo Certificate Manager に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

Sectigo Certificate Manager と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Sectigo Certificate Manager アカウント。

> [!NOTE]
> Sectigo では、複数の Sectigo Certificate Manager インスタンスを運用しています。 Sectigo Certificate Manager のメイン インスタンスは **https:\//cert-manager.com** です。このチュートリアルでは、この URL を使用します。  アカウントが別のインスタンスにある場合は、それに応じて URL を調整する必要があります。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、Sectigo Certificate Manager を Azure AD と統合します。

Sectigo Certificate Manager では、次の機能がサポートされます。

* **SP Initiated シングル サインオン**。
* **IDP Initiated シングル サインオン**。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Azure portal で Sectigo Certificate Manager を追加する

Azure AD への Sectigo Certificate Manager の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Sectigo Certificate Manager を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Sectigo Certificate Manager**」と入力します。
1. 結果のパネルから **[Sectigo Certificate Manager]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sectigo-certificate-manager"></a>Sectigo Certificate Manager の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Sectigo Certificate Manager に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Sectigo Certificate Manager の関連ユーザーとの間にリンク関係を確立する必要があります。

Sectigo Certificate Manager に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Sectigo Certificate Manager の SSO の構成](#configure-sectigo-certificate-manager-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Sectigo Certificate Manager のテスト ユーザーの作成](#create-sectigo-certificate-manager-test-user)** - Sectigo Certificate Manager で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Sectigo Certificate Manager** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    1. Sectigo Certificate Manager のメイン インスタンスの場合には、 **[識別子 (エンティティ ID)]** ボックスに「**https:\//cert-manager.com/shibboleth**」と入力します。

    1. Sectigo Certificate Manager のメイン インスタンスの場合には、 **[応答 URL]** ボックスに「**https:\//cert-manager.com/Shibboleth.sso/SAML2/POST**」と入力します。
        
    > [!NOTE]
    > 一般に "**SP Initiated モード**" では **サインオン URL** が必須となっていますが、Sectigo Certificate Manager からのログインでは不要です。        

1. 必要に応じて、**IDP Initiated モード** を構成し、**テスト** を機能させるために、 **[基本的な SAML 構成]** セクションで次の手順を実行します。

    1. **[追加の URL を設定します]** を選択します。

    1. **[リレー状態]** ボックスに、Sectigo Certificate Manager の顧客別 URL を入力します。 Sectigo Certificate Manager のメイン インスタンスの場合には、「**https:\//cert-manager.com/customer/\<customerURI\>/idp**」を入力します。

1. **[ユーザー属性と要求]** セクションで、次の手順に実行します。

    1. **追加の要求** をすべて削除します。
    
    1. **[新しいクレームの追加]** を選択し、次の 4 つのクレームを追加します。
    
        | 名前 | 名前空間 | source | ソース属性 | [説明] |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | 属性 | user.userprincipalname | 管理者用 Sectigo Certificate Manager の **[IdP Person ID]\(IdP 人物 ID\)** フィールドの内容と合致している必要があります。 |
        | mail | empty | 属性 | User.mail | 必須 |
        | givenName | empty | 属性 | User.givenname | オプション |
        | sn | empty | 属性 | User.surname | オプション |

       ![Sectigo Certificate Manager - 4 つのクレームの追加](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** の隣にある **[ダウンロード]** を選択します。 XML ファイルを、お使いのコンピューターに保存します。

    ![フェデレーション メタデータ XML のダウンロード オプション](common/metadataxml.png)

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

このセクションでは、B.Simon に Sectigo Certificate Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Sectigo Certificate Manager]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sectigo-certificate-manager-sso"></a>Sectigo Certificate Manager の SSO の構成

Sectigo Certificate Manager 側でシングル サインオンを構成するには、ダウンロードしたフェデレーション メタデータ XML ファイルを [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)に送信します。 Sectigo Certificate Manager チームは、送られてきた情報を使用して、SAML シングル サインオン接続が両方の側で正しく設定されているかどうかを確認します。

### <a name="create-sectigo-certificate-manager-test-user"></a>Sectigo Certificate Manager のテスト ユーザーの作成

このセクションでは、Sectigo Certificate Manager で Britta Simon というユーザーを作成します。 [Sectigo Certificate Manager サポート チーム](https://sectigo.com/support)と連携して、Sectigo Certificate Manager プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、Azure AD のシングル サインオン構成をテストします。

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Sectigo Certificate Manager からのテスト (SP Initiated シングル サインオン)

自分の顧客別 URL (Sectigo Certificate Manager のメイン インスタンスの場合には、https:\//cert-manager.com/customer/\<customerURI\>/) を参照し、 **[Or Sign In With]\(または、他のサインイン手段を使用する\)** の下にあるボタンを選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Azure のシングル サインオン構成からのテスト (IDP Initiated シングル サインオン)

**Sectigo Certificate Manager** アプリケーション統合ペインで、 **[シングル サインオン]** 、 **[テスト]** の順に選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>マイ アプリ ポータルを使ったテスト (IDP Initiated シングル サインオン)

マイ アプリ ポータルで **[Sectigo Certificate Manager]** を選択します。  正しく構成されている場合には、Sectigo Certificate Manager に自動的にサインインします。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Sectigo Certificate Manager を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。