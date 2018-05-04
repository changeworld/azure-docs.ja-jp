---
title: Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行されたロール要求の構成 | Microsoft Docs
description: Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行されたロール要求を構成する方法について説明します
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5520781c9f687e5e32ad1d8c38922a456bb5e3ce
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Azure Active Directory のエンタープライズ アプリケーションの SAML トークンで発行されたロール要求の構成

この機能により、ユーザーは、Azure AD を使用したアプリの承認時に受信される応答トークン内の、'ロール' 要求の要求の種類をカスタマイズできます。

## <a name="prerequisites"></a>前提条件
- ディレクトリのセットアップを済ませた Azure AD サブスクリプション
- シングル サインオンが有効なサブスクリプション
- アプリケーションで SSO を構成する必要があります

## <a name="when-to-use-this-feature"></a>この機能を使用する状況

アプリケーションが、SAML 応答でカスタム ロールが渡されることを予期している場合は、この機能を使用する必要があります。 この機能で、Azure AD からアプリケーションに渡すロールを必要な数だけ作成できます。

## <a name="steps-to-use-this-feature"></a>この機能を使用するための手順

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスにアプリケーションの名前を入力し、結果ウィンドウからアプリケーションを選択して、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストのアプリケーション](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. アプリケーションが追加されたら、**[プロパティ]** ページに移動して、**オブジェクト ID** をコピーします

    ![[プロパティ] ページ](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 テナントのグローバル管理者/共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    b. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    複数のディレクトリを使用している場合は、次のパターンに従う必要があります。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. 取得したサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl キーを押しながら F キーを押して、一覧に示されたすべての ServicePrincipals からアプリケーションを検索することもできます。 プロパティ ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用して対応するサービス プリンシパルを取得します。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    d. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。

    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. ここで、アプリケーションの新しいロールを生成する必要があります。 [ここ](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y)から Azure AD ロール ジェネレーターをダウンロードできます。

    f. Azure AD ジェネレーターを開き、以下の手順を実行します。

    ![Azure AD ジェネレーター](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    **ロール名**、**ロールの説明**、および**ロールの値**を入力します。 **[追加]** をクリックしてロールを追加します
    
    必要なロールをすべて追加したら、**[生成]** をクリックします
    
    **[コンテンツのコピー]** をクリックしてコンテンツをコピーします

    > [!NOTE] 
    > 生成されたロール内で、**msiam_access** ユーザー ロールがあることと、ID が一致していることを確認してください。

    g. Graph Explorer に戻ります。 メソッドを **GET** から **PATCH** に変更します。 コピーした値で appRoles プロパティを更新して、必要な appRoles を持つようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックします。

    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > appRoles オブジェクトの例を次に示します。 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. より多くのロールでサービス プリンシパルを修正したら、対応する役割にユーザーを割り当てることができます。 これは、ポータルに移動し、対応するアプリに移動することで実行できます。 次に、上部の **[ユーザーとグループ]** タブをクリックします。 このプロセスにより、すべてのユーザーまたはグループが一覧表示されます。

    ![シングル サインオンの構成の追加](./media/active-directory-enterprise-app-role-management/userrole.png)

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 ユーザーにロールを割り当てるには、特定のユーザー/グループを選択し、ページの下部にある **[割り当て]** ボタンをクリックします。

    ![シングル サインオンの構成の追加](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. それをクリックすると、それぞれのサービス プリンシパルに対して定義されている異なるロールからロールを選択するポップアップが表示されます。

    c. 必要なロールを選択して、送信をクリックします。

8. ユーザーにロールを割り当てた後は、**[属性]** テーブルを更新して、**ロール**要求のカスタマイズしたマッピングを定義する必要があります。

9. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、図に示すように SAML トークン属性を構成し、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | -------------- | ----------------|    
    | ロール名      | user.assignedrole |

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![シングル サインオンの構成の追加](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![シングル サインオン属性の構成](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[値]** 一覧から、その行に対して表示される値を入力します。

    d. **[名前空間]** は空白のままにします。
    
    e. **[OK]** をクリックします。

10. IDP で開始したシングル サインオンでアプリケーションをテストするには、アクセス パネル (https://myapps.microsoft.com) にログインし、アプリケーション タイルをクリックします。 SAML トークンには、指定した要求の名前を持つ、そのユーザーに割り当てられているすべてのロールが表示されるはずです。

## <a name="update-existing-role"></a>既存のロールの更新

1. 既存のロールを更新するには、以下の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

    b. テナントのグローバル管理者/共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。
    
    c. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    複数のディレクトリを使用している場合は、次のパターンに従う必要があります。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. 取得したサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl キーを押しながら F キーを押して、一覧に示されたすべての ServicePrincipals からアプリケーションを検索することもできます。 プロパティ ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用して対応するサービス プリンシパルを取得します。
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`
    
    e. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. 既存のロールを更新するには、以下の手順に従ってください。

    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * メソッドを **GET** から **PATCH** に変更します。

    * アプリケーションから既存のロールをコピーし、それらを**要求本文**に貼り付けます。
    
    * 組織の要件に従って**ロールの説明**、**ロールの値**、および**ロールの表示名**を置き換えて、ロールの値を更新します。
    
    * 必要なロールをすべて更新したら、**[クエリの実行]** をクリックします。
        
## <a name="delete-existing-role"></a>既存のロールを削除する

1. 既存のロールを削除するには、以下の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

    b. テナントのグローバル管理者/共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    c. バージョンを**ベータ版**に変更し、次のクエリを使用してテナントからサービス プリンシパルの一覧を取得します。
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    複数のディレクトリを使用している場合は、次のパターンに従う必要があります。`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. 取得したサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl キーを押しながら F キーを押して、一覧に示されたすべての ServicePrincipals からアプリケーションを検索することもできます。 プロパティ ページからコピーした **オブジェクト ID** を検索し、次のクエリを使用して対応するサービス プリンシパルを取得します。
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`
    
    e. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. 既存のロールを削除するには、以下の手順に従ってください。

    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    メソッドを **GET** から **PATCH** に変更します。

    アプリケーションから既存のロールをコピーし、それらを**要求本文**に貼り付けます。
    
    削除したいロールの **IsEnabled** の値を **false** に設定します

    **[クエリの実行]** をクリックします。
    
    > [!NOTE] 
    > 生成されたロール内で、**msiam_access** ユーザー ロールがあることと、ID が一致していることを確認してください。
    
    g. 上記のプロセスを行った後は、メソッドを **PATCH** として維持し、残っているロールのコンテンツを**要求本文**内に貼り付けて、**[クエリの実行]** をクリックします。
    
    ![Graph Explorer のダイアログ ボックス](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. クエリの実行後、ロールは削除されます。
    
    > [!NOTE]
    > ロールは、最初に無効にしてから削除する必要があります。 

## <a name="next-steps"></a>次の手順

追加の手順については、[アプリのマニュアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)を参照してください。

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
