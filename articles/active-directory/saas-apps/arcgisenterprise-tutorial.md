---
title: 'チュートリアル: Azure Active Directory と ArcGIS Enterprise の統合 | Microsoft Docs'
description: Azure Active Directory と ArcGIS Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ca5bf7ae49cf120c0566419ccadeff92433c6467
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819282"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>チュートリアル: Azure Active Directory と ArcGIS Enterprise の統合

このチュートリアルでは、ArcGIS Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

ArcGIS Enterprise と Azure AD の統合には、次の利点があります。

- Azure AD で誰が ArcGIS Enterprise にアクセスできるかを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に ArcGIS Enterprise にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ArcGIS Enterprise と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ArcGIS Enterprise でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ArcGIS Enterprise の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>ギャラリーからの ArcGIS Enterprise の追加

Azure AD への ArcGIS Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ArcGIS Enterprise を追加する必要があります。

**ギャラリーから ArcGIS Enterprise を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**ArcGIS Enterprise**」と入力し、結果パネルで **[ArcGIS Enterprise]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ArcGIS Enterprise で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ArcGIS Enterprise ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ArcGIS Enterprise の関連ユーザーの間で、リンク関係が確立されている必要があります。

ArcGIS Enterprise で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ArcGIS Enterprise のテスト ユーザーの作成](#create-an-arcgis-enterprise-test-user)** - ArcGIS Enterprise で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ArcGIS Enterprise アプリケーションでシングル サインオンを構成します。

**ArcGIS Enterprise で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ArcGIS Enterprise** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. **[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\)** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順に従います。

    ![[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\) のシングル サインオン情報](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. **[識別子]** ボックスに、`<EXTERNAL_DNS_NAME>.portal` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2` のパターンを使用して URL を入力します。

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\) のシングル サインオン情報](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    **[サインオン URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin` のパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[ArcGIS Enterprise クライアント サポート チーム](mailto:nshampur@esri.com)に問い合わせてください。 識別子の値は、このチュートリアルで後から説明する **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで取得します。

5. **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[App Federation Metadata Url]\(アプリケーション フェデレーション メタデータ URL\)** をコピーし、メモ帳に貼り付けます。

    ![証明書のダウンロードのリンク](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. 別の Web ブラウザーのウィンドウで、ArcGIS Enterprise の企業サイトに管理者としてログインします。

8. **[組織] > [設定の編集]** を選択します。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure1.png)

9. **[セキュリティ]** タブを選びます。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure2.png)

10. **[Enterprise Logins via SAML]\(SAML 経由でのエンタープライズ ログイン\)** セクションまで下へスクロールして **[SET ENTERPRISE LOGIN]\(エンタープライズ ログインの設定\)** を選択します。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure3.png)

11. **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで、次の手順に従います。

    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure4.png)

    a. **[名前]** テキスト ボックスに、**Azure Active Directory Test** のような名前を指定します。

    b. **[URL]** ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    c. **[詳細設定の表示]** をクリックし、**[エンティティ ID]** の値をコピーして、Azure Portal の **[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\)** セクションの **[識別子]** テキスト ボックスに貼り付けます。
    
    ![ArcGIS Enterprise の構成](./media/arcgisenterprise-tutorial/configure5.png)

    d. **[ID プロバイダーの更新]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="create-an-arcgis-enterprise-test-user"></a>ArcGIS Enterprise テスト ユーザーの作成

このセクションの目的は、ArcGIS Enterprise で Britta Simon というユーザーを作成することです。 ArcGIS Enterprise では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ArcGIS Enterprise にアクセスしようとすると、ユーザーがまだ存在しない場合は新しいユーザーが作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[ArcGIS Enterprise のサポート チーム](mailto:nshampur@esri.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ArcGIS Enterprise へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200]

**Britta Simon を ArcGIS Enterprise に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. アプリケーションの一覧で、**[ArcGIS Enterprise]\(ArcGIS Enterprise\)** を選択します。

    ![アプリケーションの一覧の ArcGIS Enterprise のリンク](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ArcGIS Enterprise]\(ArcGIS Enterprise\) タイルをクリックすると、自動的に ArcGIS Enterprise アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png