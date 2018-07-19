---
title: 'チュートリアル: Azure Active Directory と Adobe Sign の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Sign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: jeedes
ms.openlocfilehash: d5cdc2ec0c6cfcf52f84629485d0dd879fbf6fa2
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054000"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>チュートリアル: Azure Active Directory と Adobe Sign の統合

このチュートリアルでは、Adobe Sign と Azure Active Directory (Azure AD) を統合する方法について説明します。

Adobe Sign と Azure AD の統合には、次の利点があります。

- Adobe Sign にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Adobe Sign にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Adobe Sign と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Adobe Sign でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Adobe Sign の追加。
2. Azure AD シングル サインオンの構成とテスト。

## <a name="add-adobe-sign-from-the-gallery"></a>ギャラリーからの Adobe Sign の追加
Azure AD への Adobe Sign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Sign を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のアイコンのスクリーンショット][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** に移動します。

    ![Azure Active Directory メニューのスクリーンショット ([エンタープライズ アプリケーション] と [すべてのアプリケーション] が赤枠で示されています)][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部の **[新しいアプリケーション]** を選択します。

    ![ダイアログ ボックスの上部にある [新しいアプリケーション] オプションのスクリーンショット][3]

4. [検索] ボックスに、「**Adobe Sign**」と入力します。

    ![検索ボックスのスクリーンショット](./media/adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. 結果パネルで **[Adobe Sign]** を選択し、**[追加]** を選択します。

    ![結果パネルのスクリーンショット](./media/adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Adobe Sign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、 Azure AD ユーザーと Adobe Sign 内の関連ユーザーとのリンク関係が Azure AD によって認識される必要があります。

リンク関係を確立するには、Adobe Sign で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てます。

Adobe Sign で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了します。

1. [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [Adobe Sign テスト ユーザーの作成](#creating-an-adobe-sign-test-user) - Adobe Sign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#testing-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Adobe Sign アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **Adobe Sign** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![Adobe Sign アプリケーション統合ページのスクリーンショット ([シングル サインオン] が赤枠で示されています)][4]

2. **[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックスのスクリーンショット ([モード] ボックスが赤枠で示されています)](./media/adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. **[Adobe Sign のドメインと URL]** セクションで、次の手順を実行します。

    ![[Adobe Sign のドメインと URL] セクションのスクリーンショット](./media/adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.echosign.com/` という形式で URL を入力します。

    b. **[識別子]** ボックスに、`https://<companyname>.echosign.com` という形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に問い合わせください。

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** を選び、コンピューターに証明書ファイルを保存します。

    ![[SAML 署名証明書] セクションのスクリーンショット](./media/adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. **[保存]** を選択します。

    ![[保存] ボタンのスクリーンショット](./media/adobe-echosign-tutorial/tutorial_general_400.png)

6. **[Adobe Sign Configuration (Adobe Sign 構成)]** セクションで、**[Configure Adobe Sign (Adobe Sign を構成する)]** を選択して、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** をコピーします。

    ![[Adobe Sign Configuration (Adobe Sign 構成)] セクションのスクリーンショット ([Configure Adobe Sign (Adobe Sign を構成する)] が赤枠で示されています)](./media/adobe-echosign-tutorial/tutorial_adobesign_configure.png)

7. Adobe Sign で自分のドメインをホワイトリストに登録するには、構成する前に、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に連絡してください。 ドメインの追加方法は次のとおりです。

    a. [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)からランダムに生成されたトークンが送信されます。 ドメインの場合、トークンは **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx** のようになります。

    b. DNS テキスト レコードで検証トークンを発行し、[Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知します。
    
    > [!NOTE]
    > これには数日以上かかる場合があります。 DNS への反映の遅延は、DNS で公開された値が 1 時間以上表示されない可能性があることを意味することに注意してください。 DNS テキスト レコードでこのトークンを発行する方法については、通常、組織の IT 管理者が熟知しています。
    
    c. トークンが発行された後に、サポート チケットを通じて [Adobe Sign クライアント サポート チーム](https://helpx.adobe.com/in/contact/support.html)に通知すると、サポート チームがドメインを検証し、それをユーザーのアカウントに追加します。
    
    d. 通常、DNS レコードでトークンを発行する手順は次のとおりです。

    * ドメイン アカウントにサインインする
    * DNS レコードを更新するためのページを検索する。 このページは、DNS 管理、ネーム サーバー管理、または詳細設定と呼ばれる場合があります。
    * 自分のドメインの TXT レコードを検索する。
    * Adobe から提供された完全なトークン値を使用して TXT レコードを追加する。
    * 変更を保存します。

8. 別の Web ブラウザーのウィンドウで、管理者として Adobe Sign 企業サイトにサインインします。

9. [SAML] メニューで、**[アカウント設定]** > **[SAML 設定]** を選択します。
   
    ![Adobe Sign の [SAML 設定] ページ](./media/adobe-echosign-tutorial/ic789520.png "[アカウント]") のスクリーンショット

10. **[SAML 設定]** セクションで、次の手順に従います。
  
    ![[SAML 設定]](./media/adobe-echosign-tutorial/ic789521.png "[SAML 設定]") のスクリーンショット
   
    a. **[SAML モード]** で **[SAML Mandatory]** を選択します。
   
    b. **[Echosign 資格情報を使用して、Echosign アカウント管理者のログインを許可する]** を選択します。
   
    c. **[ユーザーの作成]** で、**[SAML を使用して認証されたユーザーを自動的に追加]** を選択します。

    d. Azure Portal からコピーした **SAML エンティティ ID** を **[Entity ID/Issuer URL]\(エンティティ ID/発行者の URL\)** ボックスに貼り付けます。
    
    e. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[Login URL/SSO Endpoint]\(ログイン URL/SSO エンドポイント\)** ボックスに貼り付けます。
   
    f. Azure Portal からコピーした **サインアウト URL** を **[Logout URL/SLO Endpoint]\(ログアウト URL/SSO エンドポイント\)** ボックスに貼り付けます。

    g. ダウンロードした**証明書 (Base64)** ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、**[IdP Certificate]\(IdP 証明書\)** ボックスに貼り付けます。

    h. **[変更の保存]** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure Portal のテスト ユーザー名のスクリーンショット][100]

1. **Azure Portal** の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure AD アイコンのスクリーンショット](./media/adobe-echosign-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選択します。
    
    ![Azure AD メニューのスクリーンショット ([ユーザーとグループ] と [すべてのユーザー] が赤枠で示されています)](./media/adobe-echosign-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログ ボックスを開くには、**[追加]** を選択します。
 
    ![[すべてのユーザー] ダイアログ ボックス上部のスクリーンショット ([追加] オプションが赤枠で示されています)](./media/adobe-echosign-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。
 
    ![[ユーザー] ダイアログ ボックスのスクリーンショット](./media/adobe-echosign-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]** を選択します。
 
### <a name="create-an-adobe-sign-test-user"></a>Adobe Sign のテスト ユーザーの作成

Azure AD ユーザーが Adobe Sign にサインインできるようにするには、ユーザーを Adobe Sign にプロビジョニングする必要があります。 この設定は手動で行います。

>[!NOTE]
>他の Adobe Sign ユーザー アカウント作成ツールまたは Adobe Sign から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

1. **Adobe Sign** 企業サイトに管理者としてサインインします。

2. 上部のメニューで、**[アカウント]** を選択します。 次に、左側のウィンドウで **[ユーザーとグループ]** > **[新規ユーザーを作成]** を選択します。
   
    ![Adobe Sign 企業サイトのスクリーンショット ([アカウント]、[ユーザーとグループ]、[新規ユーザーを作成] が赤枠で示されています)](./media/adobe-echosign-tutorial/ic789524.png "アカウント")
   
3. **[新しいユーザーの作成]** セクションで、次の手順に従います。
   
    ![[新規ユーザーを作成] セクションのスクリーンショット](./media/adobe-echosign-tutorial/ic789525.png "ユーザーの作成")
   
    a. 関連するテキスト ボックスに、プロビジョニングする有効な Azure AD アカウントの **[メール アドレス]**、**[名]**、**[姓]** を入力します。
   
    b. **[ユーザーを作成]** を選択します。

>[!NOTE]
>Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Adobe Sign へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![Azure Portal のシングル サインオンのスクリーンショット][200] 

1. Azure ポータルで、アプリケーション ビューを開きます。 次にディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動した後、**[すべてのアプリケーション]** を選択します。

    ![Azure Portal のアプリケーション ビューのスクリーンショット ([エンタープライズ アプリケーション] と [すべてのアプリケーション] が赤枠で示されています)][201] 

2. アプリケーションの一覧で **[Adobe Sign]** を選択します。

    ![アプリケーション一覧のスクリーンショット (Adobe Sign が赤枠で示されています)](./media/adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![メニューのスクリーンショット ([ユーザーとグループ] が赤枠で示されています)][202] 

4. **[追加]** を選択します。 **[割り当ての追加]** セクションで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] ページと [割り当ての追加] セクションのスクリーンショット][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

アクセス パネルで Adobe Sign のタイルを選択すると、自動的に Adobe Sign アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/adobe-echosign-tutorial/tutorial_general_203.png
