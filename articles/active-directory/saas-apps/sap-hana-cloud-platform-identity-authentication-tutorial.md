---
title: 'チュートリアル: Azure Active Directory と SAP Cloud Platform Identity Authentication の統合 | Microsoft Docs'
description: Azure Active Directory と SAP Cloud Platform Identity Authentication の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 266c9523f45294899e3cddbe782cbc54846eb119
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422309"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>チュートリアル: Azure Active Directory と SAP Cloud Platform Identity Authentication の統合

このチュートリアルでは、SAP Cloud Platform Identity Authentication と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Cloud Platform Identity Authentication は、Azure AD をメイン IdP として使用する SAP アプリケーションにアクセスするためのプロキシ IdP として使用されます。

SAP Cloud Platform Identity Authentication を Azure AD と統合すると、次の利点があります。

- Azure AD で、SAP アプリケーションにアクセスできるユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで SAP アプリケーションに自動的にサインインできるように設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」の記事をご覧ください。

## <a name="prerequisites"></a>前提条件

SAP Cloud Platform Identity Authentication と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション。
- SAP Cloud Platform Identity Authentication でのシングル サインオンが有効なサブスクリプション。

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使わないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使わないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手します](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SAP Cloud Platform Identity Authentication の追加
1. Azure AD シングル サインオンの構成とテスト

技術的な詳細の説明に入る前に、調べようとしている事柄の概念を理解する必要があります。 SAP Cloud Platform Identity Authentication と Active Directory フェデレーション サービスにより、SAP Cloud Platform Identity Authentication で保護された SAP アプリケーションおよびサービスに対し、(IdP としての) Azure AD によって保護されたアプリケーションまたはサービス全体で SSO を実装できます。

現時点では、SAP Cloud Platform Identity Authentication は、SAP アプリケーションへのプロキシ ID プロバイダーとして機能します。 さらに Azure Active Directory は、このセットアップにおける主要な ID プロバイダーとして機能します。 

次の図にこの関係を示します。

![Azure AD のテスト ユーザーの作成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

このセットアップにより、SAP Cloud Platform Identity Authentication テナントは、Azure Active Directory で信頼されたアプリケーションとして構成されます。 

この方法で保護するすべての SAP アプリケーションおよびサービスは、その後で SAP Cloud Platform Identity Authentication 管理コンソールで構成されます。

つまり、SAP アプリケーションおよびサービスへのアクセスの許可のための承認は、(Azure Active Directory ではなく) SAP Cloud Platform Identity Authentication で行われる必要があります。

Azure Active Directory Marketplace を通じて SAP Cloud Platform Identity Authentication をアプリケーションとして構成すれば、要求/SAML アサーションを個別に構成する必要がありません。

>[!NOTE] 
>現在、その両者で Web SSO のみがテストされています。 アプリ対API または API 対 API の通信に必要なフローは機能するものの、まだテストされていません。 そのテストは後のアクティビティ中に行われる予定です。
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>ギャラリーからの SAP Cloud Platform Identity Authentication の追加
Azure AD への SAP Cloud Platform Identity Authentication の統合を構成するには、SAP Cloud Platform Identity Authentication をギャラリーから一連のマネージド SaaS アプリに追加する必要があります。

**ギャラリーから SAP Cloud Platform Identity Authentication を追加するには、次の手順に従います。**

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに、「**SAP Cloud Platform Identity Authentication**」と入力します。 

1. 結果パネルから **[SAP Cloud Platform Identity Authentication]** を選択し、**[追加]** ボタンを選択します。

    ![結果リストの SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、SAP Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成し、テストします。 その構成とテストには、"Britta Simon" というテスト ユーザーを使用します。

シングル サインオンを機能させるには、対応する SAP Cloud Platform Identity Authentication ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Cloud Platform Identity Authentication の関連ユーザーとの間にリンクを確立する必要があります。

SAP Cloud Platform Identity Authentication で、**[Username]\(ユーザー名\)** の値に、Azure AD の **[ユーザー名]** と同じ値を割り当てます。 これで、2 人のユーザー間にリンクが確立されました。

SAP Cloud Platform Identity Authentication で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
1. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
1. [SAP Cloud Platform Identity Authentication テスト ユーザーの作成](#create-an-sap-cloud-platform-identity-authentication-test-user) - SAP Cloud Platform Identity Authentication で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. [シングル サインオンのテスト](#test-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Cloud Platform Identity Authentication アプリケーションでシングル サインオンを構成します。

**SAP Cloud Platform Identity Authentication で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログ ボックスの **[SAML ベースのサインオン]** で **[モード]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

1. **IDP** 開始モードでアプリケーションを構成する場合は、**[SAP Cloud Platform Identity Authentication のドメインと URL]** セクションで、以下の手順を実行します。  

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. **[識別子]** ボックスに、`<IAS-tenant-id>.accounts.ondemand.com` というパターンで URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com` の形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。 識別子の値がわからない場合は、[テナントの SAML 2.0 の構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)に関する SAP Cloud Platform Identity Authentication のドキュメントをお読みください。

1. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** を選択します。

    ![[SAP Cloud Platform Identity Authentication] のシングル サインオン情報](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    **[サインオン URL]** ボックスに、`{YOUR BUSINESS APPLICATION URL}` 形式で URL を入力します。

    > [!NOTE]
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 特定のビジネス アプリケーションのサインオン URL を使ってください。 わからないことがある場合は、[SAP Cloud Platform Identity Authentication クライアント サポート チーム](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)にお問い合わせください。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択します。 次に、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

1. SAP Cloud Platform Identity Authentication アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 次のスクリーンショットは、この形式の例です。 

    ![Configure single sign-on](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

1. SAP アプリケーションに **firstName** などの属性が必要な場合は、**[ユーザー属性]** セクションに **firstName** 属性を追加します。 このオプションは、**[SAML トークン属性]** ダイアログ ボックスの **[シングル サインオン]** ダイアログ ボックスにあります。

    a. **[属性の追加]** ダイアログ ボックスを開くには、**[属性の追加]** を選択します。 
    
    ![Configure single sign-on](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![Configure single sign-on](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、属性名「**firstName**」を入力します。
    
    c. **[値]** ボックスの一覧から、属性値 "**user.givenname**" を選択します。
    
    d. **[OK]** を選びます。

1. **[保存]** を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

1. **[SAP Cloud Platform Identity Authentication 構成]** セクションで、**[SAP Cloud Platform Identity Authentication の構成]** を選択して **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![SAP Cloud Platform Identity Authentication 構成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

1. アプリケーション用に構成された SSO を入手するために、SAP Cloud Platform Identity Authentication 管理コンソールにアクセスします。 URL は `https://<tenant-id>.accounts.ondemand.com/admin` というパターンです。 次に、「[Integration with Microsoft Azure AD (Microsoft Azure AD との統合)](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)」で SAP Cloud Platform Identity Authentication に関するドキュメントを読みます。 

1. Azure Portal で **[保存]** ボタンを選択します。

1. 以降は、もう 1 つの SAP アプリケーションに対して SSO を追加して有効にする場合にのみ行います。 「**ギャラリーからの SAP Cloud Platform Identity Authentication の追加**」セクションの手順を繰り返してください。

1. Azure Portal の **SAP Cloud Platform Identity Authentication** アプリケーション統合ページで、**[リンクされたサインオン]** を選択します。

    ![リンクされたサインオンの構成](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

1. 構成を保存します。

>[!NOTE] 
>新しいアプリケーションでは、前の SAP アプリケーションのシングル サインオン構成が再利用されます。 SAP Cloud Platform Identity Authentication 管理コンソールで、同じ会社の ID プロバイダーを使用していることを確認してください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンを選択します。

    ![Azure Active Directory のボタン](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **作成**を選択します。
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud Platform Identity Authentication テスト ユーザーの作成

SAP Cloud Platform Identity Authentication でユーザーを作成する必要はありません。 Azure AD ユーザー ストアに存在するユーザーは、SSO 機能を使用できます。

SAP Cloud Platform Identity Authentication は、ID フェデレーション オプションをサポートしています。 このオプションにより、アプリケーションは、企業の ID プロバイダーによって認証されたユーザーが SAP Cloud Platform Identity Authentication のユーザー ストアに存在するかどうかを確認できます。 

既定では、ID フェデレーション オプションは無効になっています。 ID フェデレーションが有効になっていると、SAP Cloud Platform Identity Authentication にインポートされているユーザーのみがアプリケーションにアクセスできます。 

SAP Cloud Platform Identity Authentication との ID フェデレーションを有効または無効にする方法の詳細については、[SAP Cloud Platform Identity Authentication のユーザー ストアとの ID フェデレーションの構成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)に関するページで、SAP Cloud Platform Identity Authentication との ID フェデレーションの有効化に関するセクションを参照してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Cloud Platform Identity Authentication へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を SAP Cloud Platform Identity Authentication に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。 次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[SAP Cloud Platform Identity Authentication]** を選択します。

    ![アプリケーションの一覧の [SAP Cloud Platform Identity Authentication] リンク](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンを選びます。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

1. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンを選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルの [SAP Cloud Platform Identity Authentication] タイルを選択すると、SAP Cloud Platform Identity Authentication アプリケーションに自動的にサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
