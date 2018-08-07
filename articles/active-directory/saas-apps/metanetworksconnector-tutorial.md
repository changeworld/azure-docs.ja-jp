---
title: 'チュートリアル: Azure Active Directory と Meta Networks Connector の統合 | Microsoft Docs'
description: Azure Active Directory と Meta Networks Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e27ba7d5b245d8857f0c07bfe2923afe9d7e3a0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267533"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>チュートリアル: Azure Active Directory と Meta Networks Connector の統合

このチュートリアルでは、Meta Networks Connector と Azure Active Directory (Azure AD) を統合する方法について説明します。

Meta Networks Connector と Azure AD の統合には、次の利点があります。

- Meta Networks Connector にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Meta Networks Connector にサインオン (シングル サインオン) できるように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Meta Networks Connector と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Meta Networks Connector でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Meta Networks Connector の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-meta-networks-connector-from-the-gallery"></a>ギャラリーからの Meta Networks Connector の追加
Azure AD への Meta Networks Connector の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Meta Networks Connector を追加する必要があります。

**ギャラリーから Meta Networks Connector を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Meta Networks Connector**」と入力して、結果パネルから **Meta Networks Connector** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Meta Networks Connector で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Meta Networks Connector ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Meta Networks Connector の関連ユーザーの間で、リンク関係が確立されている必要があります。

Meta Networks Connector で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Meta Networks Connector のテスト ユーザーの作成](#create-a-meta-networks-connector-test-user)** - Meta Networks Connector で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Meta Networks Connector アプリケーションでシングル サインオンを構成します。

**Meta Networks Connector で Azure AD シングル サインオンを構成するには、次の手順を行います。**

1. Azure Portal の **Meta Networks Connector** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

3. **[Meta Networks Connector のドメインと URL]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    ![[Meta Networks Connector のドメインと URL] のシングル サインオン情報](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    a. **[識別子]** ボックスに、`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Meta Networks Connector のドメインと URL] のシングル サインオン情報](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    a. **[サインオン URL]** ボックスに、`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login` のパターンを使用して URL を入力します。

    b. **[リレー状態]** ボックスに、`https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

5. Meta Networks Connector アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットはその例です。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)

6. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、上の図に示すように SAML トークン属性を構成し、次の手順を実行します。

    | 属性名 | 属性値 | NAMESPACE|
    | ---------------| --------------- | -------- |
    | firstname | User.givenname | |
    | lastname | User.surname | |
    | emailaddress| User.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。

    e. **[OK]** をクリックします。

7. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)

8. **[Meta Networks Connector 構成]** セクションで、**[Meta Networks Connector の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)

9. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/metanetworksconnector-tutorial/tutorial_general_400.png)

10. ブラウザーで新しいタブを開き、Meta Networks Connector の管理者アカウントにログインします。

    > [!NOTE]
    > Meta Networks Connector は、セキュリティで保護されたシステムです。 したがって、ポータルにアクセスする前に、接続先側でパブリック IP アドレスをホワイトリストに登録する必要があります。 パブリック IP アドレスを取得するには、[ここ](https://whatismyipaddress.com/)で指定されているリンクに従います。 IP アドレスを [Meta Networks Connector クライアント サポート チーム](mailto:support@metanetworks.com)に送信し、IP アドレスをホワイトリストに登録します。

11. **[管理者]** に移動して **[設定]** を選択します。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/configure3.png)

12. **[Log Internet Traffic]\(インターネット トラフィックのログ記録\)** と **[Force VPN MFA]\(VPN MFA の強制\)** がオフに設定されていることを確認します。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/configure1.png)

13. **[管理者]** に移動して **[SAML]** を選択します。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/configure4.png)

14. **[DETAILS]\(詳細\)** タブで次の手順を実行します。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/configure2.png)

    a. **[SSO URL]** の値をコピーし、**[Meta Networks Connector ドメインと URL]** セクションの **[サインイン URL]** テキスト ボックスに貼り付けます。

    b. **[Recipient URL]\(受信者 URL\)** の値をコピーし、**[Meta Networks Connector ドメインと URL]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    c. **[Audience URI (SP Entity ID)]\(オーディエンス URI (SP エンティティ ID)\)** の値をコピーし、**[Meta Networks Connector ドメインと URL]** セクションの **[識別子 (エンティティ ID)]** テキスト ボックスに貼り付けます。

    d. SAML を有効にします

15. **[GENERAL]\(全般\)** タブで、次の手順を実行します。

    ![Configure single sign-on](./media/metanetworksconnector-tutorial/configure5.png)

    a. **[Identity Provider Single Sign-On URL]\(ID プロバイダーのシングル サインオン URL\)** に、Azure portal からコピーした **SAML シングル サインオン サービスの URL** の値を貼り付けます。

    b. **[Identity Provider Issuer]\(ID プロバイダー発行者\)** に、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書をメモ帳で開き、**[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    d. **[Just-in-Time Provisioning]\(ジャストイン タイム プロビジョニング\)** を有効にします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/metanetworksconnector-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/metanetworksconnector-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/metanetworksconnector-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/metanetworksconnector-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-a-meta-networks-connector-test-user"></a>Meta Networks Connector のテスト ユーザーを作成する

このセクションの目的は、Meta Networks Connector で Britta Simon というユーザーを作成することです。 Meta Networks Connector では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Meta Networks Connector ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Meta Networks Connector クライアント サポート チーム](mailto:support@metanetworks.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Meta Networks Connector へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Meta Networks Connector に Britta Simon を割り当てるには、次の手順を行います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[Meta Networks Connector]** を選択します。

    ![アプリケーションの一覧の Meta Networks Connector リンク](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Meta Networks Connector] タイルをクリックすると、自動的に Meta Networks Connector アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

