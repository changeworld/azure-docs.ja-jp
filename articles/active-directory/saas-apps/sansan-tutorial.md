---
title: 'チュートリアル: Azure Active Directory と Sansan の統合 | Microsoft Docs'
description: Azure Active Directory と Sansan の間でシングル サインオンを構成する方法について確認します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: cc070f7c4cb201e68c93b0b1337982325df74663
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051263"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>チュートリアル: Azure Active Directory と Sansan の統合

このチュートリアルでは、Sansan と Azure Active Directory (Azure AD) を統合する方法について説明します。

Sansan と Azure AD の統合には、次の利点があります。

- Sansan にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Sansan にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Sansan と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Sansan でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Sansan を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sansan-from-the-gallery"></a>ギャラリーから Sansan を追加する
Azure AD への Sansan の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Sansan を追加する必要があります。

**ギャラリーから Sansan を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「**Sansan**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/tutorial_sansan_search.png)

5. 結果ウィンドウで **[Sansan]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Sansan で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Sansan ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Sansan の関連ユーザーの間で、リンク関係が確立されている必要があります。

Sansan で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Sansan で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Sansan のテスト ユーザーの作成](#creating-a-sansan-test-user)** - Sansan で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Sansan アプリケーションでシングル サインオンを構成します。

**Sansan で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Sansan** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. **[Sansan のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/sansan-tutorial/tutorial_sansan_url.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。 
    
    | 環境 | URL |
    |:--- |:--- |
    | PC Web |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | ネイティブ モバイル アプリ |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | モバイル ブラウザーの設定 |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオンURLで、これらの値を更新してください。 この値を取得するには、[Sansan クライアント サポート チーム](https://www.sansan.com/form/contact)に問い合わせてください。 
     
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/sansan-tutorial/tutorial_general_400.png)

6. Sansan アプリケーションは、 複数の環境（PC web、 ネイティブ モバイル アプリ、モバイル ブラウザーの設定）をサポートするために、複数の**識別子**と**返信用 URL** が必要です。これは、PowerShell スクリプトを使用して　構成することができます。 細かい手順について、下記に説明します。

7. PowerShell スクリプトを　使用して、Sansan アプリケーションに、複数の**識別子**と**返信用 URL** を構成するには、次の手順を実行します：

    ![シングル サインオン obj を構成します](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. 次に、**Sansan** アプリケションの**プロパティ** ページに移動し、**[コピー]** ボタンを使用して **[オブジェクト ID]** をコピーし、メモ帳に貼り付けます。

    b. Azure Portal から　コピーした**オブジェクト ID** は、チュートリアルの後半で使用される PowerShell スクリプトに、**ServicePrincipalObjectId** とし使用されます。 

    c. 次に、管理者特権での Windows PowerShell コマンド プロンプトを開きます。
    
    >[!NOTE] 
    > Azure AD モジュールをインストールする必要があります (`Install-Module -Name AzureAD` コマンドを使用してください)。 NuGet モジュールまたは新しい Azure Active Directory V2 PowerShell モジュールをインストールするように求められたら「Y」と入力し、ENTER キーを押します。

    d. `Connect-AzureAD` を実行してグローバル管理者ユーザー アカウントでサインインします。

    e. 次のスクリプトを使用して、アプリケーションの複数の URL を更新します。

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. PowerShell スクリプトが正常に完了した後、スクリプトの結果は、下記に示すようになります。URL の値は更新されますが、　それらは、Azure Portal には反映されません。 

    ![シングル サインオン スクリプトの構成](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. **[Sansan 構成]** セクションで、**[Sansan の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. **Sansan** 側にシングル サインオンを構成するには、ダウンロードした**証明書**、**サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** を [Sansan サポート チーム](https://www.sansan.com/form/contact)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

>[!NOTE]
>PC のブラウザーの設定は、PC Web だけでなく、モバイル アプリおよびモバイル ブラウザーでも機能します。 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/sansan-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sansan-test-user"></a>Sansan のテスト ユーザーの作成

このセクションでは、SanSan に、 Britta Simon というユーザーを作成します。 SanSan アプリケーションでは、SSO を実行する前に、ユーザーをアプリケーションにプロビジョニングする必要があります。 

>[!NOTE]
>ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、[Sansan のサポート チーム](https://www.sansan.com/form/contact)に問い合わせてください。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Sansan へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Sansan に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Sansan]** を選択します。

    ![Configure single sign-on](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Sansan] タイルをクリックすると、Sansan アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

