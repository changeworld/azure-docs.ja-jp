---
title: 'チュートリアル: Azure Active Directory と Bridgeline Unbound の統合 | Microsoft Docs'
description: Azure Active Directory と Bridgeline Unbound の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b018472f-c8b3-403d-ae66-9ed26a35f413
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: c429afa12bc11db68d041fef96f66b3f4c7f0b1b
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206692"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>チュートリアル: Azure Active Directory と Bridgeline Unbound の統合

このチュートリアルでは、Bridgeline Unbound と Azure Active Directory (Azure AD) を統合する方法について説明します。

Bridgeline Unbound と Azure AD の統合には、次の利点があります。

- Bridgeline Unbound にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Bridgeline Unbound に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Bridgeline Unbound と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Bridgeline Unbound シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Bridgeline Unbound の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>ギャラリーからの Bridgeline Unbound の追加
Azure AD への Bridgeline Unbound の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Bridgeline Unbound を追加する必要があります。

**ギャラリーから Bridgeline Unbound を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Bridgeline Unbound**」と入力し、結果パネルで **Bridgeline Unbound** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Bridgeline Unbound](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Bridgeline Unbound で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Bridgeline Unbound ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Bridgeline Unbound の関連ユーザーの間で、リンク関係が確立されている必要があります。

Bridgeline Unbound で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Bridgeline Unbound のテスト ユーザーの作成](#create-a-bridgeline-unbound-test-user)** - Bridgeline Unbound で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Bridgeline Unbound アプリケーションでシングル サインオンを構成します。

**Bridgeline Unbound との Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure portal の **Bridgeline Unbound** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_samlbase.png)
 
3. **[Bridgeline Unbound のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Bridgeline Unbound のドメインと URL] のシングル サインオン情報](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url.png)

    a. **[識別子]** ボックスに、`iApps_UPSTT_<ENVIRONMENTNAME>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Bridgeline Unbound のドメインと URL] のシングル サインオン情報](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_url1.png)

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Bridgeline Unbound クライアント サポート チーム](mailto:support@iapps.com)に連絡してください。 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/bridgelineunbound-tutorial/tutorial_general_400.png)

6. **[Bridgeline Unbound 構成]** セクションで、**[Bridgeline Unbound の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Bridgeline Unbound の構成](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_configure.png) 

7. **Bridgeline Unbound** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を [Bridgeline Unbound サポート チーム](mailto:support@iapps.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/bridgelineunbound-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\)** に移動し、**[すべてのユーザー]** をクリックします。

    ![[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\) と [すべてのユーザー] リンク](./media/bridgelineunbound-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/bridgelineunbound-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/bridgelineunbound-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-bridgeline-unbound-test-user"></a>Bridgeline Unbound のテスト ユーザーを作成する

このセクションの目的は、Bridgeline Unbound で Britta Simon というユーザーを作成することです。 Bridgeline Unbound では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Bridgeline Unbound ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Bridgeline Unbound サポート チーム](mailto:support@iapps.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Bridgeline Unbound へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Bridgeline Unbound に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **Bridgeline Unbound** を選択します。

    ![アプリケーション一覧の Bridgeline Unbound リンク](./media/bridgelineunbound-tutorial/tutorial_bridgelineunbound_app.png)  

3. 左側のメニューで **[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\)** をクリックします。

    ![[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\) リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\)** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\)** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[Users and grobridgelineinbound]\(ユーザーと grobridgelineinbound\)** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Bridgeline Unbound] タイルをクリックすると、Bridgeline Unbound アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bridgelineunbound-tutorial/tutorial_general_01.png
[2]: ./media/bridgelineunbound-tutorial/tutorial_general_02.png
[3]: ./media/bridgelineunbound-tutorial/tutorial_general_03.png
[4]: ./media/bridgelineunbound-tutorial/tutorial_general_04.png

[100]: ./media/bridgelineunbound-tutorial/tutorial_general_100.png

[200]: ./media/bridgelineunbound-tutorial/tutorial_general_200.png
[201]: ./media/bridgelineunbound-tutorial/tutorial_general_201.png
[202]: ./media/bridgelineunbound-tutorial/tutorial_general_202.png
[203]: ./media/bridgelineunbound-tutorial/tutorial_general_203.png

