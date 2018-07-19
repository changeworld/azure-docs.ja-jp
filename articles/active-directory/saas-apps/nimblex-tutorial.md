---
title: 'チュートリアル: Azure Active Directory と Nimblex の統合 | Microsoft Docs'
description: Azure Active Directory と Nimblex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 7b5dc6d892741f63596589a48ad5d45891b14c21
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040407"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>チュートリアル: Azure Active Directory と Nimblex の統合

このチュートリアルでは、Nimblex と Azure Active Directory (Azure AD) を統合する方法について説明します。

Nimblex と Azure AD の統合には、次の利点があります。

- Nimblex にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Nimblex にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Nimblex と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Nimblex シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Nimblex の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-nimblex-from-the-gallery"></a>ギャラリーからの Nimblex の追加
Azure AD への Nimblex の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nimblex を追加する必要があります。

**ギャラリーから Nimblex を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Nimblex**」と入力し、結果パネルで **[Nimblex]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Nimblex](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Nimblex で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Nimblex ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Nimblex の関連ユーザーの間で、リンク関係が確立されている必要があります。

Nimblex で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Nimblex のテスト ユーザーの作成](#create-a-nimblex-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Nimblex で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Nimblex アプリケーションでシングル サインオンを構成します。

**Nimblex で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Nimblex** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. **[Nimblex のドメインと URL]** セクションで、次の手順を行います。

    ![[Nimblex のドメインと URL] のシングル サインオン情報](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. **[サインオン URL]** ボックスに、`https://<YOUR APPLICATION PATH>/Login.aspx` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<YOUR APPLICATION PATH>/` の形式で URL を入力します。

    c. **[応答 URL]** ボックスに、`https://<path-to-application>/SamlReply.aspx` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 この値を取得するには、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/nimblex-tutorial/tutorial_general_400.png)

6. **[Nimblex Configuration]\(Nimblex 構成\)** セクションで、**[Configure Nimblex]\(Nimblex の構成\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Nimblex 構成](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. 別の Web ブラウザー ウィンドウで、Nimblex にセキュリティ管理者としてログインします。

9. ページの右上の**設定**ロゴをクリックします。

    ![Nimblex 設定](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. **コントロール パネル** ページの **[セキュリティ]** の下で、**[シングル サインオン]** をクリックします。

    ![Nimblex 設定](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. **[シングル サインオンの管理]** ページで、インスタンス名を選択して **[編集]** をクリックします。

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. **[SSO プロバイダーの編集]** ページで、次の手順を行います。

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. **[説明]** テキストボックスに、インスタンス名を入力します。

    b. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[証明書]** ボックスに貼り付けます。

    c. **[Identity Provider SSO Target URL]\(ID プロバイダーの SSO ターゲット URL\)** テキストボックスに、Azure portal からコピーした **SAML シングル サインオン サービスの URL** の値を貼り付けます。

    d. **[Save]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/nimblex-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/nimblex-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/nimblex-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/nimblex-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-nimblex-test-user"></a>Nimblex テスト ユーザーの作成

このセクションの目的は、Nimblex で Britta Simon というユーザーを作成することです。 Nimblex では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Nimblex ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Nimblex クライアント サポート チーム](mailto:support@ebms.com.au)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Nimblex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Nimblex に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Nimblex]** を選択します。

    ![アプリケーションの一覧の Nimblex のリンク](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Nimblex] タイルをクリックすると、Nimblex アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png

