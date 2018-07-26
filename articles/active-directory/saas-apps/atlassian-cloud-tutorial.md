---
title: 'チュートリアル: Azure Active Directory と Atlassian Cloud の統合 | Microsoft Docs'
description: Azure Active Directory と Atlassian Cloud の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: jeedes
ms.openlocfilehash: 68613b8613a2e5a9139b83eb23e66884659efc47
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114936"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>チュートリアル: Azure Active Directory と Atlassian Cloud の統合

このチュートリアルでは、Atlassian Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Atlassian Cloud と Azure AD の統合には、次の利点があります。

- Azure AD で、Atlassian Cloud にアクセスできる ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Atlassian Cloud にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD との統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Atlassian Cloud と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション。
- Atlassian Cloud 製品の Security Assertion Markup Language (SAML) シングル サインオンを有効にするには、Atlassian Access を設定する必要があります。 詳細については、「[Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)」を参照してください。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの Atlassian Cloud の追加
* Azure AD シングル サインオンの構成とテスト

## <a name="add-atlassian-cloud-from-the-gallery"></a>ギャラリーからの Atlassian Cloud の追加
Azure AD と Atlassian Cloud との統合を構成するには、次の手順に従って、ギャラリーから管理対象 SaaS アプリの一覧に Atlassian Cloud を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
3. アプリケーションを追加するには、**[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Atlassian Cloud**」と入力し、検索結果一覧で **[Atlassian Cloud]** を選択してから **[追加]** を選択します。

    ![結果リスト内の Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、*Britta Simon* というテスト ユーザーに基づいて、Atlassian Cloud で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Atlassian Cloud ユーザーが Azure AD で特定されている必要があります。 言い換えると、Azure AD ユーザーと Atlassian Cloud の関連ユーザーとの間で、リンク関係を確立する必要があるということです。

リンクの関係を確立するには、Azure AD の *user name (ユーザー名)* に割り当てられているのと同じ値を、Atlassian Cloud の *Username (ユーザー名)* に割り当てます。

Atlassian Cloud で Azure AD シングル サインオンを構成してテストするには、次のセクションにある基本要素の構成を完了する必要があります。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Atlassian Cloud アプリケーションでシングル サインオンを構成します。

Atlassian Cloud で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. Azure Portal の **Atlassian Cloud** アプリケーション統合ウィンドウで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ウィンドウの **[シングル サインオン モード]** ボックスで、**[SAML ベースのサインオン]** を選択します。

    ![[シングル サインオン] ウィンドウ](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. **IDP 開始**モードでアプリケーションを構成する場合は、**[Atlassian Cloud のドメインと URL]** で、次の手順を行います。

    ![Atlassian Cloud のドメインと URL のシングル サインオン情報](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. **[識別子]** ボックスに、次のパターンで URL を入力します。`https://auth.atlassian.com/saml/<unique ID>`
    
    b. **[応答 URL]** ボックスに、`https://auth.atlassian.com/login/callback?connection=saml-<unique ID>` の形式で URL を入力します。

    c. **[詳細な URL 設定の表示]** をクリックします。

    d. **[リレー状態]** ボックスで次のパターンの URL を入力します。`https://<instancename>.atlassian.net`

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値は、このチュートリアルで後述する Atlassian Cloud SAML 構成画面から取得できます。

4. SP 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** を選択し、**[サインオン URL]** ボックスで次のパターンの URL を入力します。`https://<instancename>.atlassian.net`

    ![Atlassian Cloud のドメインと URL のシングル サインオン情報](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > 上記のサインオン URL の値は、実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 この値を取得するには、[クライアント サポート チーム](https://support.atlassian.com/)に問い合わせてください。

5. **[SAML 署名証明書]** で、**[証明書 (Base64)]** を選択し、ご利用のコンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Atlassian Cloud アプリケーションでは、特定の形式の SAML アサーションを使用するため、ご利用の SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 

    既定では、**[ユーザー識別子]** の値は user.userprincipalname にマップされています。 この値が user.mail にマップされるよう変更します。 お客様の組織の設定によっては他の適切な値を選択することもできますが、ほとんどの場合は電子メールが適切です。

    ![証明書のダウンロードのリンク](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. **[サインオンの構成]** ウィンドウを開くには、**[Atlassian Cloud 構成]** セクションで **[Atlassian Cloud の構成]** を選択します。

9. **[クイック リファレンス]** セクションで、**SAML エンティティ ID** と **SAML シングル サインオン サービス URL** をコピーします。

    ![[Atlassian Cloud 構成]](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. ご利用のアプリケーションの SSO を構成するには、管理者の資格情報で Atlassian ポータルにサインインします。

11. シングル サインオンを構成する前に、ドメインを確認する必要があります。 詳細については、[Atlassian の「Domain verification」(ドメインの確認)](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) ドキュメントを参照してください。

12. 左側のウィンドウで、**[SAML single sign-on]\(SAML シングル サインオン\)** を選択します。 まだ Atlassian の Identity Manager に登録していない場合は、登録します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. **[Add SAML configuration]\(SAML 構成の追加\)** ウィンドウで、次の手順を実行します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. **[Identity provider Entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに、Azure Portal でコピーした SAML エンティティ ID を貼り付けます。

    b. **[Identity provider SSO URL]\(ID プロバイダーの SSO URL\)** ボックスに、Azure Portal でコピーした SAML シングル サインオン サービス URL を貼り付けます。

    c. Azure Portal からダウンロードした証明書を .txt ファイルで開き、*Begin Certificate (証明書の開始)* 行と *End Certificate (証明書の終了)* 行以外の値をコピーして、**[Public X509 certificate]\(公開 X509 証明書\)** ボックスに貼り付けます。
    
    d. **[Save Configuration]\(構成を保存\)** を選択します。
     
14. 確実に正しい URL を設定するには、次の手順を実行して Azure AD 設定を更新します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. SAML ウィンドウで **[SP Identity ID]\(SP 固有 ID\)** の値をコピーし、Azure Portal の **[Atlassian Cloud のドメインと URL]** で、**[識別子]** ボックスに貼り付けます。
    
    b. SAML ウィンドウで **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値をコピーし、Azure Portal の **[Atlassian Cloud のドメインと URL]** で **[応答 URL]** ボックスに貼り付けます。 サインオン URL は、ご利用の Atlassian Cloud のテナント URL です。

    > [!NOTE]
    > 既存のお客様であれば、Azure Portal の **[SP Identity ID]\(SP 固有 ID\)** と **[SP Assertion Consumer Service URL]\(SP アサーション利用者サービス URL\)** の値を更新したあと、**[Yes, update configuration]\(はい、構成を更新します\)** を選択します。 新規のお客様であれば、この手順をスキップしてかまいません。
    
15. Azure Portal で、**[保存]** を選択します。

    ![Configure single sign-on](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure Portal で次の手順に従って Britta Simon というテスト ユーザーを作成します。

   ![Azure AD のテスト ユーザーの作成][100]

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** > **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. **[すべてのユーザー]** ウィンドウで、**[追加]** を選択します。

    ![[追加] ボタン](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ウィンドウで、次の手順を実行します。

    ![[ユーザー] ウィンドウ](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]** を選択します。

### <a name="create-an-atlassian-cloud-test-user"></a>Atlassian Cloud のテスト ユーザーの作成

Azure AD ユーザーが Atlassian Cloud にサインインできるようにするには、Atlassian Cloud で次の手順を実行して、手動でユーザー アカウントをプロビジョニングします。

1. **[Administration]\(管理\)** ウィンドウで、**[Users]\(ユーザー\)** を選択します。

    ![Atlassian Cloud の [Users]\(ユーザー\) リンク](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Atlassian Cloud でユーザーを作成するには、**[Invite user]\(ユーザーの招待\)** を選択します。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. **[Email address]\(電子メール アドレス\)** ボックスにユーザーのメール アドレスを入力してから、アプリケーション アクセスを割り当てます。

    ![Atlassian Cloud ユーザーの作成](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. ユーザーに招待メールを送信するには、**[Invite user]\(ユーザーの招待\)** を選択します。 ユーザーに招待メールが送信され、招待が受け入れられると、システム上でそのユーザーがアクティブになります。

>[!NOTE]
>**[Users]\(ユーザー\)** セクションの **[Bulk Create]\(一括作成\)** を選択することで、まとめて複数のユーザーを作成することもできます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon というユーザーに Atlassian Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。 そのためには、次の手順を実行します。

![ユーザー ロールを割り当てる][200]

1. Azure Portal で **[アプリケーション]** ビューを開いてディレクトリ ビューに移動してから、**[エンタープライズ アプリケーション]** > **[すべてのアプリケーション]** の順に選択します。

    ![ユーザーの割り当て][201]

2. **[アプリケーション]** 一覧で、**[Atlassian Cloud]** を選択します。

    ![[アプリケーション] リストの [Atlassian Cloud] リンク](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** を選択し、**[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ウィンドウの **[ユーザー]** 一覧で、**Britta Simon** を選択します。

6. **[ユーザーとグループ]** ウィンドウで、**[選択]** を選びます。

7. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[Atlassian Cloud]** タイルを選択すると、ご利用の Atlassian Cloud アプリケーションに自動でサインオンされるはずです。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
