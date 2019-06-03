---
title: チュートリアル:Azure Active Directory と monday.com の統合 | Microsoft Docs
description: Azure Active Directory と monday.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc142bf02a44ea85861f4cc648fd7ee8602c7520
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896882"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>チュートリアル:Azure Active Directory と monday.com の統合

このチュートリアルでは、monday.com と Azure Active Directory (Azure AD) を統合する方法について説明します。

monday.com と Azure AD の統合により、次の利点が得られます。

* Azure AD を使用して、monday.com にアクセスするユーザーを管理できます。
* ユーザーは、自分の Azure AD アカウントで monday.com に自動的にサインイン (シングル サインオン) できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

monday.com と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* シングル サインオンが有効な monday.com のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストし、monday.com を Azure AD と統合します。

monday.com では、次の機能をサポートしています。

* **SP が起点となるシングル サインオン**
* **IDP が起点となるシングル サインオン**
* **Just-in-time のユーザー プロビジョニング**

## <a name="add-mondaycom-in-the-azure-portal"></a>Azure portal での monday.com の追加

monday.com を Azure AD と統合するには、管理対象 SaaS アプリのリストに monday.com を追加する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] オプション](common/select-azuread.png)

1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] オプション](common/add-new-app.png)

1. 検索ボックスに「**monday.com**」と入力します。 検索結果で **[monday.com]** を選択し、 **[追加]** を選択します。

    ![結果一覧の monday.com](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、monday.com で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと monday.com 内の関連ユーザーとの間にリンク関係を確立する必要があります。

monday.com で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

| タスク | 説明 |
| --- | --- |
| **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** | ユーザーがこの機能を使用できるようにします。 |
| **[monday.com のシングル サインオンの構成](#configure-mondaycom-single-sign-on)** | アプリケーションでシングル サインオン設定を構成します。 |
| **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** | Britta Simon という名前のユーザーの Azure AD シングル サインオンをテストします。 |
| **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** | Britta Simon が Azure AD シングル サインオンを使用できるようにします。 |
| **[monday.com のテスト ユーザーの作成](#create-a-mondaycom-test-user)** | monday.com で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。 |
| **[シングル サインオンのテスト](#test-single-sign-on)** | 構成が機能することを確認します。 |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal において monday.com で Azure AD シングル サインオンを構成します。

1. [Azure portal](https://portal.azure.com/) の **monday.com** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン オプションを構成する](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ウィンドウで、 **[SAML]** または **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウで、**編集** (鉛筆アイコン) を選択して **[基本的な SAML 構成]** ウィンドウを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、サービス プロバイダー メタデータ ファイルがあり、*IDP 開始モード*を構成する場合、次の手順を完了します。

    1. **[メタデータ ファイルをアップロードします]** を選択します。

       ![[メタデータ ファイルをアップロードします] オプション](common/upload-metadata.png)

    1. メタデータ ファイルを選択するには、フォルダー アイコンを選択し、 **[アップロード]** を選択します。

       ![メタデータ ファイルを選択し、[アップロード] ボタンを選択する](common/browse-upload-metadata.png)

    1. メタデータ ファイルが正常にアップロードされると、 **[基本的な SAML 構成]** ウィンドウで **[識別子]** と **[応答 URL]** の値が自動的に入力されます。

       ![[基本的な SAML 構成] ウィンドウの IDP 値](common/idp-intiated.png)

       > [!Note]
       > **[識別子]** と **[応答 URL]** の値が自動的に入力されない場合は、手動で値を入力します。

1. *[SP 開始モード]* でアプリケーションを構成するには:

    1. **[追加の URL を設定します]** を選択します。
    
    1. **[サインオン URL]** ボックスに、https:\//\<自身のドメイン>.monday.comのパターンで URL を入力します。 [monday.com client support team](mailto:support@monday.com) に問い合わせて、サインオン URL を取得します。

        ![[追加の URL を設定します] オプション](common/metadata-upload-additional-signon.png)

1. monday.com アプリケーションは、SAML アサーションが特定の形式であることを予期します。 このアプリケーションには、次の要求を構成します。 これらの属性値を管理するには、 **[SAML でシングル サインオンをセットアップします]** ウィンドウで **[編集]** を選択して **[ユーザー属性]** ウィンドウを開きます。

    ![[ユーザー属性] ウィンドウ](common/edit-attribute.png)

1. **[ユーザーの要求]** の下で、 **[編集]** を選択して要求を編集します。 要求を追加するには、 **[新しい要求の追加]** を選択し、上の図に示すように、SAML トークン属性を構成します。 その後、次の手順を完了します。 

    1. **[新しい要求の追加]** を選択します。

        ![[ユーザーの要求] ウィンドウの [新しい要求の追加] オプション](common/new-save-attribute.png)

    1. **[ユーザー要求の管理]** ウィンドウで、次の値を設定します。
        
       1. **[名前]** ボックスに、ユーザー要求の行に表示される属性名を入力します。

       1. **[名前空間]** は空白のままにします。

       1. **[ソース]** で **[属性]** を選択します。

       1. **[ソース属性]** の一覧から、ユーザー要求の行に示される属性値を選択します。

       1. **[OK]** を選択し、 **[保存]** を選択します。

       ![ユーザー要求の管理](common/new-attribute-details.png)

1. **[SAML でシングル サインオンをセットアップします]** ウィンドウの **[SAML 署名証明書]** で、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択します。 要件に基づいてダウンロードオプションを選択します。 コンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) ダウンロード オプション](common/certificatebase64.png)

1. **[monday.com のセットアップ]** セクションで、要件に基づいて次の URL をコピーします。

    * ログイン URL
    * Azure AD 識別子
    * ログアウト URL

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>monday.com のシングル サインオンの構成

monday.com 側でシングル サインオンを構成するには、ダウンロードした証明書 (Base64) ファイルと、Azure portal からコピーした関連した URL を [monday.com サポート チーム](mailto:support@monday.com)に送信します。 monday.com サポート チームは、送られてきた情報を使用して、SAML シングル サインオン接続が両方の側で正しく設定されているかどうかを確認します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure ポータルで Britta Simon というテスト ユーザーを作成します。

1. Azure portal 内で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。

    ![[ユーザー] と [すべてのユーザー] オプション](common/users.png)

1. **[ 新規ユーザー]** を選択します。

    ![[新しいユーザー] オプション](common/new-user.png)

1. **[ユーザー]** ウィンドウで、次の手順を実行します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**brittasimon\@\<企業のドメイン>.\<extension>** 」と入力します。 たとえば、「**brittasimon\@contoso.com**」です。

    1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示された値を書き留めます。

    1. **作成** を選択します。

    ![[ユーザー] ウィンドウ](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon が Azure シングル サインオンを使用できるように、monday.com へのアクセス権をこのユーザーに与えます。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]**  >  **[monday.com]** の順に選択します。

    ![[エンタープライズ アプリケーション] ウィンドウ](common/enterprise-applications.png)

1. アプリケーションの一覧で **[monday.com]** を選択します。

    ![アプリケーションの一覧の monday.com](common/all-applications.png)

1. メニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] オプション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ウィンドウで、ユーザーの一覧から **[Britta Simon]** を選択します。 **[選択]** を選択します。

1. SAML アサーションでロール値が必要な場合は、 **[ロールの選択]** ウィンドウで、ユーザーに関連したロールを一覧から選択します。 **[選択]** を選択します。

1. **[割り当ての追加]** ウィンドウで **[割り当て]** を選択します。

### <a name="create-a-mondaycom-test-user"></a>monday.com のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを monday.com アプリケーションで作成します。 monday.com では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 monday.com にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

シングル サインオンをセットアップした後、マイ アプリ ポータルで **monday.com** を選択すると、monday.com に自動的にサインインされます。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
