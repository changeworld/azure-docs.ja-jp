---
title: 'チュートリアル: Azure Active Directory と AwardSpring の統合 | Microsoft Docs'
description: Azure Active Directory と AwardSpring の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: f75c14989b46721e7043c06086cac02222f452a5
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431319"
---
# <a name="tutorial-azure-active-directory-integration-with-awardspring"></a>チュートリアル: Azure Active Directory と AwardSpring の統合

このチュートリアルでは、AwardSpring と Azure Active Directory (Azure AD) を統合する方法について説明します。

AwardSpring と Azure AD の統合には、次の利点があります。

- AwardSpring にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に AwardSpring にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

AwardSpring と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- AwardSpring のシングル サインオンに対応したサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの AwardSpring の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-awardspring-from-the-gallery"></a>ギャラリーからの AwardSpring の追加
Azure AD への AwardSpring の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に AwardSpring を追加する必要があります。

**ギャラリーから AwardSpring を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**AwardSpring**」と入力し、結果パネルで **[AwardSpring]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの AwardSpring](./media/awardspring-tutorial/tutorial_awardspring_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、AwardSpring で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する AwardSpring ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと AwardSpring の関連ユーザーの間で、リンク関係が確立されている必要があります。

AwardSpring で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[AwardSpring のテスト ユーザーの作成](#create-an-awardspring-test-user)** - AwardSpring で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、AwardSpring アプリケーションでシングル サインオンを構成します。

**AwardSpring で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **AwardSpring** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/awardspring-tutorial/tutorial_awardspring_samlbase.png)

1. **[AwardSpring Domain and URLs]\(AwardSpring のドメインと URL)** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[AwardSpring Domain and URLs]\(AwardSpring のドメインと URL) のシングル サインオン情報](./media/awardspring-tutorial/tutorial_awardspring_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.awardspring.com/SignIn/SamlMetaData` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.awardspring.com/SignIn/SamlAcs` のパターンを使用して URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[AwardSpring Domain and URLs]\(AwardSpring のドメインと URL) のシングル サインオン情報](./media/awardspring-tutorial/tutorial_awardspring_url1.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.awardspring.com/` のパターンを使用して URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[AwardSpring クライアント サポート チーム](mailto:support@awardspring.com)にお問い合わせください。 

1. AwardSpring アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure single sign-on](./media/awardspring-tutorial/tutorial_awardSpring_attribute.png)

1. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | ---------------| --------------- |    
    | 名 | User.givenname |
    | 姓 | User.surname |
    | 電子メール | User.mail |
    | ユーザー名 | user.userprinicipalname |
    | StudentID | < Student ID > |

    > [!NOTE]
    > StudentID 属性は、要求に渡す必要がある実際の学生 ID にマッピングされます。 この値を取得するには、[AwardSpring クライアント サポート チーム](mailto:support@awardspring.com)にお問い合わせください。

    a. 既存の属性を削除し、新しい属性を追加します。 **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/awardspring-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/awardspring-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    d. **[OK]** をクリックします。

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/awardspring-tutorial/tutorial_awardspring_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/awardspring-tutorial/tutorial_general_400.png)
    
1. **AwardSpring** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [AwardSpring サポート チーム](mailto:support@awardspring.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/awardspring-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/awardspring-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/awardspring-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/awardspring-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-an-awardspring-test-user"></a>AwardSpring テスト ユーザーの作成

このセクションの目的は、AwardSpring で Britta Simon というユーザーを作成することです。 AwardSpring では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 AwardSpring にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[AwardSpring サポート チーム](maito:support@awardspring.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に AwardSpring へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**AwardSpring に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[AwardSpring]** を選択します。

    ![アプリケーションの一覧の [AwardSpring] リンク](./media/awardspring-tutorial/tutorial_awardspring_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [AwardSpring] タイルをクリックすると、自動的に AwardSpring アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/awardspring-tutorial/tutorial_general_01.png
[2]: ./media/awardspring-tutorial/tutorial_general_02.png
[3]: ./media/awardspring-tutorial/tutorial_general_03.png
[4]: ./media/awardspring-tutorial/tutorial_general_04.png

[100]: ./media/awardspring-tutorial/tutorial_general_100.png

[200]: ./media/awardspring-tutorial/tutorial_general_200.png
[201]: ./media/awardspring-tutorial/tutorial_general_201.png
[202]: ./media/awardspring-tutorial/tutorial_general_202.png
[203]: ./media/awardspring-tutorial/tutorial_general_203.png

