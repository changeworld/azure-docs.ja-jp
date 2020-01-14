---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Citrix NetScaler の統合 (ヘッダー ベースの認証) | Microsoft Docs
description: Azure Active Directory と Citrix NetScaler の間でシングル サインオンを構成する方法について学習します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c442ca731ecb10f977c19b86cb32caded36659
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-header-based-authentication"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Citrix NetScaler の統合 (ヘッダー ベースの認証)

このチュートリアルでは、Citrix NetScaler と Azure Active Directory (Azure AD) を統合する方法について学習します。 Azure AD と Citrix NetScaler を統合すると、次のことができます。

* Citrix NetScaler にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Citrix NetScaler に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Citrix NetScaler でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Citrix NetScaler では、**SP** によって開始される SSO がサポートされます

* Citrix NetScaler では、**Just In Time** ユーザー プロビジョニングがサポートされます

- [ヘッダー ベースの認証用に Citrix NetScaler のシングル サインオンを構成する](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Kerberos ベースの認証用に Citrix NetScaler のシングル サインオンを構成する](citrix-netscaler-tutorial.md)


## <a name="adding-citrix-netscaler-from-the-gallery"></a>ギャラリーからの Citrix NetScaler の追加

Azure AD への Citrix NetScaler の統合を構成するには、ギャラリーから管理対象 SaaS アプリのリストに Citrix NetScaler を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Citrix NetScaler**」と入力します。
1. 結果のパネルから **[Citrix NetScaler]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Citrix NetScaler での Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Citrix NetScaler での Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Citrix NetScaler の関連ユーザーの間で、リンク関係を確立する必要があります。

Citrix NetScaler での Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Citrix NetScaler の SSO の構成](#configure-citrix-netscaler-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Citrix NetScaler テスト ユーザーの作成](#create-citrix-netscaler-test-user)** - Citrix NetScaler 上に B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Citrix NetScaler** アプリケーション統合ページ上で、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<<Your FQDN>>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得する場合は、[Citrix NetScaler クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

    > [!NOTE]
    > SSO を動作させるには、パブリック サイトからこれらの URL にアクセスできる必要があります。 Netscaler 側でファイアウォールまたは他のセキュリティ設定を有効にし、Azure AD で構成済みの ACS URL にトークンをポストできるようにする必要があります。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[アプリのフェデレーション メタデータ URL]** を検索し、URL をコピーしてメモ帳に保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. Citrix NetScaler アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックし、属性マッピングを変更します。

    ![image](common/edit-attribute.png)

1. その他に、Citrix NetScaler アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 [ユーザー属性] ダイアログの [ユーザー要求] セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    1. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    1. **[名前空間]** は空白のままにします。

    1. [ソース] として **[属性]** を選択します。

    1. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    1. **[OK]** をクリックします。

    1. **[保存]** をクリックします。


1. **[Citrix NetScaler のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Citrix NetScaler へのアクセスを許可することで、Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーション リストで、 **[Citrix NetScaler]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-citrix-netscaler-sso"></a>Citrix NetScaler の SSO の構成

- [ヘッダー ベースの認証用に Citrix NetScaler のシングル サインオンを構成する](#configure-citrix-netscaler-single-sign-on-for-header-based-authentication)

- [Kerberos ベースの認証用に Citrix NetScaler のシングル サインオンを構成する](citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Web サーバーの公開 

1. **仮想サーバー**を作成します。

    a. **[Traffic Management]\(トラフィック管理\) > [Load Balancing]\(負荷分散\) > [Services]\(サービス\)** に移動します。
    
    b. **[追加]** をクリックします。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/web01.png)

    c. 以下のアプリケーションを実行している Web サーバーの詳細を指定します。
    * **サービス名**
    * **サーバー IP と既存のサーバー**
    * **プロトコル**
    * **[ポート]**

     ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>ロード バランサーの構成

1. ロード バランサーを構成するには、次の手順を実行します。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/load01.png)

    a. **[Traffic Management]\(トラフィック管理\) > [Load Balancing]\(負荷分散\) > [Virtual Servers]\(仮想サーバー\)** に移動します。

    b. **[追加]** をクリックします。

    c. 次の詳細を指定します。

    * **Name**
    * **プロトコル**
    * **IP アドレス**
    * **[ポート]**
    * **[OK]** をクリックします

### <a name="bind-virtual-server"></a>仮想サーバーのバインド

前に作成した仮想サーバーにロード バランサーをバインドします。

![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>証明書のバインド

このサービスは SSL として公開される予定のため、サーバー証明書をバインドして、アプリケーションをテストします。

![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML プロファイル

### <a name="create-authentication-policy"></a>認証ポリシーの作成

1. **[Security]\(セキュリティ\) > [AAA - Application Traffic]\(AAA - アプリケーション トラフィック\) > [Policies]\(ポリシー\) > [Authentication]\(認証\) > [Authentication Policies]\(認証ポリシー\)** に移動します。

2. **[Add]\(追加\)** をクリックして、[Details]\(詳細\) を指定します。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/policy01.png)

    a. **[Authentication Policy]\(認証ポリシー\)** の名前。

    b. [Expression]\(式\): **true**。

    c. [Action type]\(アクションの種類\) を **[SAML]** にします。

    d. [Action]\(アクション\) = **[Add]\(追加\)** をクリックします ([Create Authentication SAML Server]\(認証 SAML サーバーの作成\) ウィザードに従います)。
    
    e. **[Authentication Policy]\(認証ポリシー\)** で、[Create]\(作成\) をクリックします。

### <a name="create-authentication-saml-server"></a>認証 SAML サーバーの作成

1. 次の手順に従います。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/server01.png)

    a. **[Name]\(名前\)** を指定します。

    b. メタデータをインポートします (上記からコピーした Azure SAML UI から、フェデレーション メタデータ URL を指定します)。
    
    c. **[Issuer Name]\(発行者名\)** を指定します。

    d. **[Create]\(作成\)** をクリックします。

### <a name="create-authentication-virtual-server"></a>認証仮想サーバーの作成

1.  **[Security]\(セキュリティ\) > [AAA - Application Traffic]\(AAA - アプリケーション トラフィック\) >> [Authentication Virtual Servers]\(認証仮想サーバー\)** に移動します。

2.  **[Add]\(追加\)** をクリックし、次の手順を実行します。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/server02.png)

    a.  **[名前]** を入力します。

    b.  **[Non-Addressable]\(アドレス指定不可\)** を選択します。

    c.  [Protocol]\(プロトコル\) を **[SSL]** にします。

    d.  **[OK]** をクリックします。

    e.  **[続行]** をクリックします。

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Azure AD を使用するための認証仮想サーバーの構成

認証仮想サーバーの 2 つのセクションを変更する必要があります。

1.  **[Advanced Authentication Policies]\(高度な認証ポリシー\)**

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/virtual01.png)

    a. 以前に作成した **[Authentication Policy]\(認証ポリシー\)** を選択します。

    b. **[Bind]\(バインド\)** をクリックします。

      ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/virtual02.png)

2. **[Form Based Virtual Servers]\(フォームに基づく仮想サーバー\)**

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/virtual03.png)

    a.  UI によって強制的に適用されるため、 **[FQDN]** を指定する必要があります。

    b.  Azure AD Authentication によって保護する **[Virtual Server Load Balancer]\(仮想サーバー ロード バランサー\)** を選択します。

    c.  **[Bind]\(バインド\)** をクリックします。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >[Authentication Virtual Server Configuration]\(認証用仮想サーバーの構成\) ページでも、必ず **[Done]\(完了\)** をクリックします。

3. 変更を確認します。 アプリケーションの URL にアクセスします。 以前の認証されていないアクセスではなく、テナントのログイン ページが表示されます。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-header-based-authentication"></a>ヘッダー ベースの認証用に Citrix NetScaler のシングル サインオンを構成する

### <a name="citrix-adc-configuration"></a>Citrix ADC の構成

### <a name="create-an-rewrite-action"></a>書き換えアクションの作成

1. **[AppExpert] > [Rewrite]\(書き換え\) > [Rewrite Actions]\(書き換えアクション\)** に移動します。
 
    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header01.png)

2.  **[追加]** をクリックします。

    a.  **[Name]\(名前\)** を指定します

    b.  [Type]\(種類\) = **INSERT_HTTP_HEADER**。

    c.  **[HEADER NAME]\(ヘッダー名\)** (この例では SecretID) を指定します。

    d.  式 **aaa.USER.ATTRIBUTE(`mySecretID`)** を指定します。この **mySecretID** は Citrix ADC に送信される Azure AD SAML 要求です。

    e.  **Create** をクリックしてください。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header02.png)
 
### <a name="create-a-rewrite-policy"></a>書き換えポリシーを作成します。

1.  **[AppExpert] > [Rewrite]\(書き換え\) > [Rewrite Policies]\(書き換えポリシー\)** に移動します。
 
    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header03.png)

2.  **[追加]** をクリックします。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header04.png)

    a.  **[Name]\(名前\)** を指定します。

    b.  以前に作成した**アクション**を選択します。

    c. [Expression]\(式\) に **true** を指定します。

    d.  **[Create]\(作成\)** をクリックします。

### <a name="bind-rewrite-policy-to-virtual-servers"></a>書き換えポリシーを仮想サーバーにバインドする

1. GUI を使用して書き換えポリシーを特定の仮想サーバーにバインドするには、次の手順を実行します。

2. **[Traffic Management]\(トラフィック管理\) > [Load Balancing]\(負荷分散\) > [Virtual Servers]\(仮想サーバー\)** に移動します。

3. 仮想サーバーの詳細ペインの一覧で、書き換えポリシーをバインドする **[Virtual Server]\(仮想サーバー\)** を選択し、 **[Open]\(開く\)** をクリックします。

4. [Configure Virtual Server (Load Balancing)]\(仮想サーバーの構成 (負荷分散)\) ダイアログ ボックスで、 **[Policies]\(ポリシー\)** タブを選択します。NetScaler 上で構成されているすべてのポリシーが一覧に表示されます。
 
    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header06.png)

5.  この仮想サーバーにバインドするポリシー名の横にある**チェック ボックス**をオンにします。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header07.png)

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header08.png)
 
6.  **[OK]** をクリックします。 ポリシーが正常に構成されたことを示すメッセージがステータス バーに表示されます。

### <a name="modify-saml-server-to-extract-attributes-from-claim"></a>要求から属性を抽出するように SAML サーバーを変更する

1.  **[Security]\(セキュリティ\) > [AAA - Application Traffic]\(AAA - アプリケーション トラフィック\) > [Policies]\(ポリシー\) > [Authentication]\(認証\) > [Advanced Policies]\(高度なポリシー\) > [Actions]\(アクション\) > [Servers]\(サーバー\)** に移動します。

2.  アプリケーションの適切な **[Authentication SAML Server]\(認証 SAML サーバー\)** を選択します。
 
    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header09.png)

3. [Attribute]\(属性\) セクションに、"," を使用して抽出する [SAML Attributes]\(SAML 属性\) を入力します。 この場合、**mySecretID** という属性を指定します。
 
    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header10.png)

4. [Applications]\(アプリケーション\) へのアクセスを確認します。

    ![Citrix NetScaler の構成](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-citrix-netscaler-test-user"></a>Citrix NetScaler テスト ユーザーを作成する

このセクションでは、B.Simon というユーザーを Citrix NetScaler に作成します。 Citrix NetScaler では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Citrix NetScaler にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Citrix NetScaler クライアント サポート チーム](https://www.citrix.com/contact/technical-support.html)に問い合わせる必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix NetScaler] タイルをクリックすると、SSO を設定した Citrix NetScaler に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Citrix NetScaler を試す](https://aad.portal.azure.com/)

- [Kerberos ベースの認証用に Citrix NetScaler のシングル サインオンを構成する](citrix-netscaler-tutorial.md)
