---
title: 'チュートリアル: Azure Active Directory と SAP HANA の統合 | Microsoft Docs'
description: Azure Active Directory と SAP HANA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: e498b0ca4b9efe09c2fe2f2bfcdcb3cc68b9c2c4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430255"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>チュートリアル: Azure Active Directory と SAP HANA の統合

このチュートリアルでは、SAP HANA と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP HANA と Azure AD の統合には、次の利点があります。

- SAP HANA にアクセスするユーザーを Azure AD で管理できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP HANA にサインインできるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SAP HANA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン (SSO) が有効な SAP HANA サブスクリプション
- 任意のパブリック IaaS、オンプレミス、Azure VM、または Azure 内の SAP Large Instances で実行している HANA インスタンス
- HANA インスタンスにインストールされた XSA 管理 Web インターフェイスと HANA Studio

> [!NOTE]
> このチュートリアルの手順をテストする場合、SAP HANA の運用環境を使わないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD 試用環境がまだない場合は、[Azure AD の 1 か月無料試用版](https://azure.microsoft.com/pricing/free-trial/)を入手します。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAP HANA の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="add-sap-hana-from-the-gallery"></a>ギャラリーからの SAP HANA の追加
Azure AD への SAP HANA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP HANA を追加します。

**ギャラリーから SAP HANA を追加するには、次の手順を実行します。**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SAP HANA**」と入力します。 結果パネルから **[SAP HANA]** を選択します。 最後に **[追加]** ボタンを選択してアプリケーションを追加します。 

    ![新しいアプリケーション](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP HANA で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP HANA ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP HANA の関連ユーザーの間で、リンクが確立されている必要があります。

SAP HANA で、**ユーザー名**の値に、Azure AD の**ユーザー名**と同じ値を指定します。 この手順で、2 人のユーザー間にリンクが確立します。

SAP HANA で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了します。

1. [Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
1. [SAP HANA テスト ユーザーの作成](#creating-a-sap-hana-test-user) - SAP HANA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#testing-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP HANA アプリケーションでシングル サインオンを構成します。

**SAP HANA で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **SAP HANA** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログ ボックスの **[SAML ベースのサインオン]** で、**[モード]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

1. **[SAP HANA のドメインと URL]** セクションで、次の手順を実行します。

    ![[SAP HANA のドメインと URL] のシングル サインオン情報](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. **[識別子]** ボックスに「`HA100`」と入力します。 

    b. **[応答 URL]** ボックスに、`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP HANA クライアント サポート チーム](https://cloudplatform.sap.com/contact.html)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択します。 次に、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >証明書がアクティブでない場合、Azure AD で **[新しい証明書をアクティブにする]** チェック ボックスをオンにしてアクティブにします。 

1. SAP HANA アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 次のスクリーンショットは、この形式の例です。 

    ここでは、**[ユーザー ID]** を **user.mail** の **ExtractMailPrefix()** 関数とマップしてあります。 これにより、ユーザーのメール アドレスのプレフィックス値が提供され、これは一意のユーザー ID です。 このユーザー ID は、すべての正常な応答で SAP HANA アプリケーションに送信されます。

    ![Configure single sign-on](./media/saphana-tutorial/attribute.png)

1. **[シングル サインオン]** ダイアログ ボックスの **[ユーザー属性]** セクションで、次の手順を実行します。

    a. **[ユーザー識別子]** ドロップダウン リストで、**[ExtractMailPrefix]** を選択します。
    
    b. **[メール]** ドロップダウン リストで **[user.mail]** を選択します。

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/saphana-tutorial/tutorial_general_400.png)
    
1. SAP HANA 側でシングル サインオンを構成するには、それぞれの HTTPS エンドポイントにアクセスして **HANA XSA Web Console** にログインします。

    > [!NOTE]
    > 既定の構成では、この URL で要求はサインイン画面にリダイレクトされ、認証済みの SAP HANA データベース ユーザーの資格情報を要求されます。 サインインするユーザーには、SAML 管理タスクを実行するアクセス許可が必要です。

1. XSA Web インターフェイスで、**SAML Identity Provider** に移動します。 ここから画面の下部にある **+** ボタンを選択し、**[Add Identity Provider Info]\(ID プロバイダー情報の追加\)** ウィンドウを表示します。 その後、次の手順を実行します。

    ![ID プロバイダーの追加](./media/saphana-tutorial/sap1.png)

    a. **[Add Identity Provider Info]\(ID プロバイダーの情報の追加\)** ウィンドウで、Azure Portal からダウンロードしたメタデータ XML の内容を、**[Metadata]\(メタデータ\)** ボックスに貼り付けます。

    ![ID プロバイダーの設定の追加](./media/saphana-tutorial/sap2.png)

    b. XML ドキュメントの内容が有効な場合、解析プロセスで、**[General data]\(全般的なデータ\)** 画面領域の **[Subject, Entity ID, and Issuer]\(件名、エンティティ ID、発行者\)** に必要な情報が抽出されます。 **[Base URL and SingleSignOn URL (*)]\(ベース URL と SingleSignOn URL (*)\)** のフィールドなど、**[Destination]\(接続先\)** 画面領域に URL フィールドに必要な情報も抽出されます。

    ![ID プロバイダーの設定の追加](./media/saphana-tutorial/sap3.png)

    c. **[General Data]\(全般データ\)** 画面領域の **[Name]\(名前\)** ボックスに、新しい SAML SSO ID プロバイダーの名前を入力します。

    > [!NOTE]
    > SAML IDP の名前は必須です。また、一意にする必要があります。 使用する SAP HANA XS アプリケーションの認証方法として SAML を選択すると表示される使用可能な SAML IDP の一覧に、この名前が表示されます。 たとえば、XS Artifact Administration の **[Authentication]\(認証\)** 画面領域で確認できます。

1. **[Save]\(保存\)** を選択して SAML ID プロバイダーの詳細を保存し、既知の SAML IDP の一覧に新しい SAML IDP を追加します。

    ![[保存] ボタン](./media/saphana-tutorial/sap4.png)

1. HANA Studio の **[Configuration]\(構成\)** タブのシステム プロパティで、**saml** を使用して設定を絞り込みます。 次に、**[assertion_timeout]** を **[10 sec]\(10 秒\)** から **[120 sec]\(120 秒\)** に調整します。

    ![assertion_timeout の設定](./media/saphana-tutorial/sap7.png)

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能について詳しくは、[Azure AD の組み込みドキュメント](https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。

    ![Azure Active Directory のボタン](./media/saphana-tutorial/create_aaduser_01.png) 

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動します。 次に、**[すべてのユーザー]** を選択します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/saphana-tutorial/create_aaduser_02.png) 

1. **[ユーザー]** ダイアログ ボックスを開くには、ダイアログ ボックスの上部にある **[追加]** を選択します。
 
    ![[追加] ボタン](./media/saphana-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。
 
    ![[ユーザー] ダイアログ ボックス](./media/saphana-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**メール アドレス**を入力します。

    c. **[パスワードを表示]** を選び、パスワードを書き留めます。

    d. **作成**を選択します。
 
### <a name="create-a-sap-hana-test-user"></a>SAP HANA テスト ユーザーの作成

SAP HANA への Azure AD ユーザーのサインインを有効にするには、SAP HANA でプロビジョニングする必要があります。
SAP HANA では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

ユーザーを手動で作成する必要がある場合は、次の手順を実行します。

>[!NOTE]
>ユーザーが使う外部認証を変更することができます。 Kerberos などの外部システムを使用して認証できます。 外部 ID について詳しくは、[ドメイン管理者](https://cloudplatform.sap.com/contact.html)に問い合わせてください。

1. 管理者として [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) を開き、SAML SSO の DB-User を有効にします。

    ![ユーザーの作成](./media/saphana-tutorial/sap5.png)

1. **[SAML]** の左側にある非表示のチェック ボックスをオンにし、[**Configure]\(構成\)** リンクを選択します。

1. **[Add]\(追加\)** を選択して SAML IDP を追加します。  適切な SAML IDP を選択してから **[OK]** を選択します。

1. **[External Identity]\(外部 ID\)** を追加するか (この例では BrittaSimon)、**[Any]\(任意\)** を選択します。 **[OK]** をクリックします。

    >[!Note]
    >**[Any]\(任意\)** チェック ボックスがオフの場合、HANA のユーザー名は UPN でドメイン サフィックスの前のユーザーの名前と正確に一致している必要があります (つまり、BrittaSimon@contoso.com は HANA では BrittaSimon になります)。

1. テストの場合は、すべての **XS** ロールをユーザーに割り当てます。

    ![ロールの割り当て](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > ユース ケースに適切なアクセス許可のみを付与する必要があります。

1. ユーザーを保存します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP HANA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SAP HANA に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure ポータルで、アプリケーション ビューを開きます。 ディレクトリ ビューに移動し、**[エンタープライズ アプリケーション]** に移動します。 **[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SAP HANA]** を選択します。

    ![ユーザーの割り当て](./media/saphana-tutorial/tutorial_saphana_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202] 

1. **[追加]** ボタンを選びます。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[ユーザー]** 一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで SAP HANA のタイルを選択すると、自動的に SAP HANA アプリケーションにサインインします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png

