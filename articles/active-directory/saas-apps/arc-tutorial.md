---
title: 'チュートリアル: Azure Active Directory と Arc Publishing - SSO の統合 | Microsoft Docs'
description: Azure Active Directory と Arc Publishing - SSO の間のシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: eafd7998e5bc21a539b6709794fe3cd70d9e3179
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054320"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>チュートリアル: Azure Active Directory と Arc Publishing - SSO の統合

このチュートリアルでは、Arc Publishing - SSO と Azure Active Directory (Azure AD) を統合する方法について説明します。

Arc Publishing - SSO と Azure AD の統合には、次の利点があります。

- Arc Publishing - SSO にアクセスするユーザーを Azure AD 上でコントロールできます。
- ユーザーが自分の Azure AD アカウントで自動的に Arc Publishing - SSO にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Arc Publishing - SSO の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Arc Publishing - SSO でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Arc Publishing - SSO の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>ギャラリーからの Arc Publishing - SSO の追加
Azure AD への Arc Publishing - SSO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Arc Publishing - SSO を追加する必要があります。

**ギャラリーから Arc Publishing - SSO を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Arc Publishing - SSO**」と入力し、結果パネルで **[Arc Publishing - SSO]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Arc Publishing - SSO](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーを基に、Arc Publishing - SSO で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Arc Publishing - SSO ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Arc Publishing - SSO の関連ユーザーの間で、リンク関係が確立されている必要があります。

Arc Publishing - SSO で Azure AD のシングル サインオンを構成およびテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Arc Publishing - SSO のテスト ユーザーの作成](#create-an-arc-publishing---sso-test-user)** - Arc Publishing - SSO で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Arc Publishing - SSO アプリケーションでシングル サインオンを構成します。

**Arc Publishing - SSO で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Arc Publishing - SSO** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. **[Arc Publishing - SSO のドメインと URL]** セクションで、**IDP**開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    ![[Arc Publishing - SSO のドメインと URL] のシングル サインオン情報](./media/arc-tutorial/tutorial_arc_url.png)

    1. **[識別子]** ボックスに、`https://www.okta.com/saml2/service-provider/<Unique ID>` の形式で URL を入力します。

    1. **[応答 URL]** ボックスに、`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Arc Publishing - SSO のドメインと URL] のシングル サインオン情報](./media/arc-tutorial/tutorial_arc_url1.png)

    **[サインオン URL]** ボックスに、`https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[Arc Publishing - SSO クライアント サポート チーム](mailto:inf@washpost.com)にお問い合わせください。 

1. Arc Publishing - SSO アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/arc-tutorial/tutorial_arc_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | firstName | User.givenname |
    | lastName | User.surname |
    | email | User.mail |
    | groups | user.assignedroles |

    1. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

     ![Configure single sign-on](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Configure single sign-on](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    1. **[値]** 一覧から、その行に対して表示される値を入力します。

    1. **[名前空間]** は空白のままにします。
    
    1. **[OK]** をクリックします。

    > [!NOTE]
    > ここで、**group** 属性は **user.assignedroles** とマップされます。 これらは、アプリケーションでグループ名をマップし直すために Azure AD で作成されたカスタム ロールです。 Azure AD でカスタム ロールを作成する方法についての詳しいガイダンスは、[ここ](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)に記載されています。 

1. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/arc-tutorial/tutorial_general_400.png)
    
1. **[Arc Publishing - SSO 構成]** セクションで、**[Arc Publishing - SSO の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Arc Publishing - SSO の構成](./media/arc-tutorial/tutorial_arc_configure.png) 

1. **Arc Publishing - SSO** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)、サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** を [Arc Publishing - SSO サポート チーム](mailto:inf@washpost.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/arc-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/arc-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/arc-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/arc-tutorial/create_aaduser_04.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    1. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    1. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    1. **Create** をクリックしてください。
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Arc Publishing - SSO のテスト ユーザーの作成

このセクションの目的は、Arc Publishing - SSO で Britta Simon というユーザーを作成することです。 Arc Publishing - SSO では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Arc Publishing - SSO にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Arc Publishing - SSO サポート チーム](mailto:inf@washpost.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Arc Publishing - SSO へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Arc Publishing - SSO に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Arc Publishing - SSO]** を選択します。

    ![アプリケーションの一覧の Arc Publishing - SSO のリンク](./media/arc-tutorial/tutorial_arc_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Arc Publishing - SSO のタイルをクリックすると、自動的に Arc Publishing - SSO アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

