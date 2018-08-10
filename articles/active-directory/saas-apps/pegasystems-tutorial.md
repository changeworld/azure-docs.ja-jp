---
title: 'チュートリアル: Azure Active Directory と Pega Systems の統合 | Microsoft Docs'
description: Azure Active Directory と Pega Systems の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 224120f01cf6e1a32c85d1f50c6e3a30f50d243a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39443795"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>チュートリアル: Azure Active Directory と Pega Systems の統合

このチュートリアルでは、Pega Systems と Azure Active Directory (Azure AD) を統合する方法について説明します。

Pega Systems と Azure AD の統合には、次の利点があります。

- Pega Systems にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで Pega Systems に自動的にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Pega Systems と Azure AD の統合を構成するには、以下が必要です。

- Azure AD サブスクリプション
- Pega Systems でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Pega Systems の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-pega-systems-from-the-gallery"></a>ギャラリーからの Pega Systems の追加
Azure AD への Pega Systems の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Pega Systems を追加する必要があります。

**ギャラリーから Pega Systems を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**Pega Systems**」と入力し、結果パネルで **Pega Systems** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Pega Systems](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Pega Systems で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンが機能するには、Azure AD ユーザーに対応する Pega Systems ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Pega Systems の関連ユーザーの間で、リンク関係が確立されている必要があります。

Pega Systems で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Pega Systems で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Pega Systems テスト ユーザーの作成](#create-a-pega-systems-test-user)** - Pega Systems で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Pega Systems アプリケーションでシングル サインオンを構成します。

**Pega Systems で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Pega Systems** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

1. **[Pega Systems のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Pega Systems のドメインと URL] のシングル サインオン情報](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. **[識別子]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Pega Systems のドメインと URL] のシングル サインオン情報](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    **[リレー状態]** ボックスに、`https://<CUSTOMERNAME>.pegacloud.io/prweb/sso` のパターンで URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、およびリレー状態 URL でこれらの値を更新します。 このチュートリアルの後半で説明する Pega アプリケーションで、識別子と応答 URL の値を見つけることができます。 リレー状態については、[Pega Systems のクライアント サポート チーム](https://www.pega.com/contact-us)に連絡して値を取得してください。 

1. Pega Systems アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 これらの要求はお客様に固有であり、お客様の要件によって異なります。 次のオプションの要求は、アプリケーションで構成できる単なる例です。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 

    ![Configure single sign-on](./media/pegasystems-tutorial/tutorial_attribute.png)

1. **[Single sign-on]\(シングル サインオン\)** ダイアログの **[User Attributes]\(ユーザー属性\)** セクションで、上記の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | cn  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organization | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | 電話 | *********** |

    > [!NOTE]
    > これらは、お客様に固有な値です。 適切な値を指定してください。

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_general_400.png)
    
1. **Pega Systems** 側でシングル サインオンを構成するには、別のブラウザー ウィンドウで管理者アカウントを使って **Pega ポータル**を開きます。

1. **[Create]\(作成\)** -> **[SysAdmin]** -> **[Authentication Service]\(認証サービス\)** の順に選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
1. **[Create Aauthentication Service]\(認証サービスの作成\)** 画面で次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. [Type]\(種類\) で **[SAML 2.0]** を選択します

    b. **[Name]\(名前\)** ボックスに名前を入力します (例: Azure AD SSO)

    c. **[Short Description]\(簡単な説明\)** ボックスに、任意の説明を入力します  

    d. **[Create and open]\(作成して開く\)** をクリックします 
    
1. **[Identity Provider (IdP) information]\(ID プロバイダー (IdP) 情報\)** セクションで **[Import IdP metadata]\(IdP メタデータのインポート\)** をクリックし、Azure Portal からダウンロードしたメタデータ ファイルを参照します。 **[Submit]\(送信\)** をクリックして、メタデータを読み込みます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
1. 次のように、IdP データが設定されます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
1. **[Service Provider (SP) settings]\(サービス プロバイダー (SP) 設定\)** セクションで、以下の操作を行います。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. **[Entity Identification]\(エンティティの識別\)** の値をコピーし、Azure Portal の **[識別子]** ボックスに貼り付けます。

    b.  **[Assertion Consumer Service (ACS) location]\(Assertion Consumer Service (ACS) の場所\)** の値をコピーし、Azure Portal の **[応答 URL]** ボックスに貼り付けます。

    c. **[Disable request signing]\(要求署名を無効にする\)** をオンにします。

1. **[保存]**
    
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/pegasystems-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/pegasystems-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/pegasystems-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-pega-systems-test-user"></a>Pega Systems テスト ユーザーの作成

このセクションの目的は、Pega Systems で Britta Simon というユーザーを作成することです。 Pega Sysyems でユーザーを作成するには、[Pega Systems クライアント サポート チーム](https://www.pega.com/contact-us)と協力してください。


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Pega Systems へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Pega Systems に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Pega Systems]** を選択します。

    ![アプリケーションの一覧にある Pega Systems のリンク](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Pega Systems のタイルをクリックすると、Pega Systems アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

