---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Zenya の統合 | Microsoft Docs'
description: Azure Active Directory と Zenya の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 25440815a219b353ebd44ba6ce60e504cb1862b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346536"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zenya"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zenya の統合

このチュートリアルでは、Zenya と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Zenya を統合すると、次のことが可能になります。

* Zenya にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Zenya に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Zenya でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zenya では、**SP** Initiated SSO がサポートされます
* Zenya では、[自動化されたユーザー プロビジョニング](iprova-provisioning-tutorial.md)がサポートされます。

## <a name="add-zenya-from-the-gallery"></a>ギャラリーから Zenya を追加する

Azure AD への Zenya の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zenya を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zenya**」と入力します。
1. 結果のパネルから **[Zenya]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zenya"></a>Zenya の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zenya に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Zenya の関連ユーザーとの間にリンク関係を確立する必要があります。

Zenya に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zenya の SSO の構成](#configure-zenya-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zenya のテスト ユーザーの作成](#create-zenya-test-user)** - Zenya で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="retrieve-configuration-information-from-zenya"></a>Zenya からの構成情報の取得

このセクションでは、Azure AD のシングル サインオンを構成するための情報を Zenya から取得します。

1. Web ブラウザーを開き、次の URL パターンを使用して Zenya の **[SAML2 info]\(SAML2 の情報\)** ページに移動します。
    
     `https://<SUBDOMAIN>.zenya.work/saml2info`   
     `https://<SUBDOMAIN>.iprova.nl/saml2info`  
     `https://<SUBDOMAIN>.iprova.be/saml2info`  
     `https://<SUBDOMAIN>.iprova.eu/saml2info` 

    ![Zenya SAML2 情報ページを表示する](media/iprova-tutorial/information.png)

1. そのブラウザー タブを開いたまま、別のブラウザー タブで次の手順に進みます。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zenya** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **Zenya の [SAML2 info]\(SAML2 の情報\)** ページで、 **[Sign-on URL]\(サインオン URL\)** というラベルの後にある **[Sign-on URL]\(サインオン URL\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    b. Zenya の **[SAML2 info]\(SAML2 の情報\)** ページで、 **[EntityID]** というラベルの後にある **[Identifier]\(識別子\)** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

    c. Zenya の **[SAML2 info]\(SAML2 の情報\)** ページで、 **[Reply URL]\(応答 URL\)** というラベルの後にある **[Reply-URL]** ボックスに値を入力します。 このページは他のブラウザー タブで開いたままです。

1. Zenya アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Zenya アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性| 名前空間  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

## <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Zenya へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zenya]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zenya-sso"></a>Zenya SSO の構成

1. **Administrator** アカウントを使用して Zenya にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[System settings]\(システム設定\)** パネルの **[General]\(全般\)** を選択します。

5. **[編集]** を選択します。

6. **[Access control]\(アクセス制御\)** まで下にスクロールします。

    ![Zenya のアクセス制御設定](media/iprova-tutorial/access-control.png)

7. **[Users are automatically logged on with their network accounts]\(ユーザーのネットワーク アカウントを使用して自動的にログオンする\)** 設定を探し、**[Yes, authentication via SAML]\(はい、SAML 経由で認証します\)** に変更します。 これで、追加のオプションが表示されます。

8. **[セットアップ]** を選択します。

9. **[次へ]** を選択します。

10. Zenya によって、URL からフェデレーション データをダウンロードするか、それともファイルからアップロードするかを問われます。 **[from URL]\(URL から\)** オプションを選択します。

    ![Azure AD メタデータをダウンロードする](media/iprova-tutorial/metadata.png)

11. 「Azure AD シングル サインオンの構成」セクションの最後の手順で保存したメタデータ URL を貼り付けます。

12. 矢印形のボタンを選択して、Azure AD からメタデータをダウンロードします。

13. ダウンロードが完了したら、**Valid Federation Data file downloaded (有効なフェデレーション データ ファイルがダウンロードされました)** という確認メッセージが表示されます。

14. **[次へ]** を選択します。

15. ここでは **[Test login]\(テスト ログイン\)** オプションをスキップし、**[Next]\(次へ\)** を選択します。

16. **[Claim to use]\(使用する要求\)** ドロップダウン ボックスで **[windowsaccountname]** を選択します。

17. **[完了]** を選択します。

18. これで、**[Edit general settings]\(全般設定の編集\)** 画面に戻ります。 ページの下部までスクロールし、**[OK]** を選択して自分の構成を保存します。

## <a name="create-zenya-test-user"></a>Zenya テスト ユーザーの作成

1. **Administrator** アカウントを使用して Zenya にサインインします。

2. **[Go to]\(移動\)** メニューを開きます。

3. **[Application management]\(アプリケーション管理\)** を選択します。

4. **[Users and user groups]\(ユーザーとユーザー グループ\)** パネルの **[Users]\(ユーザー\)** を選択します。

5. **[追加]** を選択します。

6. **[ユーザー名]** ボックスにユーザー名 (`B.Simon@contoso.com` など) を入力します。

7. **[Full name]\(フル ネーム\)** ボックスにユーザーのフル ネームを入力します (「**B.Simon**」など)。

8. **[No password (use single sign-on)]\(パスワードなし (シングル サインオンを使用する)\)** オプションを選択します。

9. **[E-mail address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (`B.Simon@contoso.com` など) を入力します。

10. ページの一番下までスクロールし、**[Finish]\(完了\)** を選択します。

> [!NOTE]
> Zenya では、自動ユーザー プロビジョニングもサポートされます。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./iprova-provisioning-tutorial.md)をご覧ください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zenya のサインオン URL にリダイレクトされます。 

* Zenya のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zenya] タイルをクリックすると、Zenya のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zenya を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Defender for Cloud Apps でセッション制御を適用する方法をご覧ください](/cloud-app-security/proxy-deployment-aad)。
