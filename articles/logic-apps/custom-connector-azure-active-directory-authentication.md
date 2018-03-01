---
title: "Azure AD を使用してカスタム コネクタをセキュリティで保護する - Azure Logic Apps | Microsoft Docs"
description: "Azure Active Directory (Azure AD) を使用して、API とコネクタにセキュリティと認証を追加します。"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: dae76a4230a1cfbe8970bbb1015041fde7765f49
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2018
---
# <a name="add-security-to-your-api-and-connector-with-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) を使用した API とコネクタへのセキュリティの追加

API とカスタム コネクタ間での呼び出しをセキュリティで保護するには、Web API とコネクタに Azure AD Authentication を追加します。 このシナリオでは、2 つの Azure Active Directory (Azure AD) アプリを作成します。1 つは Web API をセキュリティで保護します。もう 1 つは、コネクタの登録をセキュリティで保護し、委任されたアクセスを追加します。 

このチュートリアルでは、例として Azure Resource Manager API を使用します。 Azure Resource Manager では、Azure に構築したソリューションのコンポーネント (データベース、仮想マシン、Web アプリなど) を管理できます。 Azure Resource Manager のカスタム コネクタは、ワークフローから Azure リソースを管理する際に役立ちます。 詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)をご利用いただけます。 また、[従量課金のサブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

* このチュートリアルでは、[Azure Resource Manager のサンプル OpenAPI ファイル](https://pwrappssamples.blob.core.windows.net/samples/AzureResourceManager.json)が必要です。

  > [!NOTE] 
  > サンプル OpenAPI ファイルでは、Azure Resource Manager のすべての操作が定義されているわけではありません。現時点では、[すべてのサブスクリプションを一覧表示する](https://docs.microsoft.com/rest/api/resources/subscriptions#Subscriptions_List)操作だけが含まれています。 この OpenAPI を編集することも、[OpenAPI オンライン エディター](http://editor.swagger.io/)を使用して別の OpenAPI ファイルを作成することもできます。
  >
  > このチュートリアルは、Azure AD Authentication を使用する必要がある RESTful API に適用できます。

## <a name="1-create-your-first-azure-ad-app-for-securing-your-web-api"></a>1.Web API をセキュリティで保護する 1 つ目の Azure AD アプリを作成する

1 つ目の Azure AD アプリでは、カスタム コネクタから API (この例では Azure Resource Manager API) を呼び出すときに認証を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 複数の Azure Active Directory テナントがある場合は、ユーザー名の下のディレクトリを確認して、適切なディレクトリにサインインしていることを確認します。 

   ![ディレクトリを確認する](./media/custom-connector-azure-active-directory-authentication/check-user-directory.png)

   > [!TIP]
   > ディレクトリを変更するには、ユーザー名を選択し、目的のディレクトリを選択します。

2. 現在のディレクトリを表示できるように、Azure のメイン メニューで **[Azure Active Directory]** を選択します。

   !["Azure Active Directory" を選択する](./media/custom-connector-azure-active-directory-authentication/azure-active-directory.png)

   > [!TIP]
   > Azure のメイン メニューに **[Azure Active Directory]** が表示されていない場合は、**[すべてのサービス]** を選択します。 **[フィルター]** ボックスに、フィルターとして「Azure Active Directory」と入力し、**[Azure Active Directory]** を選択します。

3. ディレクトリ メニューの **[管理]** で、**[アプリの登録]** を選択します。 登録済みのアプリの一覧で、**[+ 新しいアプリケーションの登録]** をクリックします。

   ![[アプリの登録]、[+ 新しいアプリケーションの登録] の順に選択する](./media/custom-connector-azure-active-directory-authentication/add-app-registrations.png)

4. **[作成]** で、下記の表に従って Azure AD アプリの詳細を入力し、**[作成]** をクリックします。 

   ![Azure AD アプリを作成する](./media/custom-connector-azure-active-directory-authentication/create-app1-registration.png)

   | Setting | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **名前** | *Web API アプリの名前* | Web API の Azure AD アプリの名前 | 
   | **アプリケーションの種類** | **Web アプリ/API** | アプリの種類 | 
   | **サインオン URL** | `https://login.windows.net` | | 
   |||| 

5. ディレクトリの **[アプリの登録]** の一覧に戻ったら、Azure AD アプリを選択します。

   ![一覧から Azure AD アプリを選択する](./media/custom-connector-azure-active-directory-authentication/app1-registration-created.png)

6. アプリの詳細ページが表示されたら、**アプリの "*アプリケーション ID*" をコピーし、安全な場所に保存する**必要があります。 この ID は後で必要になります。

   ![後で使用するために "アプリケーション ID" を保存する](./media/custom-connector-azure-active-directory-authentication/application-id-app1.png)

7. Azure AD アプリの応答 URL を指定します。 アプリの **[設定]** メニューで、**[応答 URL]** を選択します。 この URL を入力し、**[保存]** をクリックします。

   ![応答 URL](./media/custom-connector-azure-active-directory-authentication/add-reply-url1.png)

   | Setting | 推奨値 | [説明] | 
   | ------- | --------------- | ----------- | 
   | **応答 URL** | 独自の API の場合は、次の URL を入力します。 </br>`https://{your-web-app-root-URL}/.auth/login/aad/callback` | | 
   | **委任されたアクセス許可** | {not necessary} | | 
   | **クライアント キー** | {not necessary} | | 
   |||| 

   > [!TIP]
   > **[設定]** メニューがまだ表示されていない場合は、ここで **[設定]** を選択します。
   >
   > ![[設定] を選択する](./media/custom-connector-azure-active-directory-authentication/show-app1-settings-menu.png)

## <a name="2-create-your-second-azure-ad-app-for-your-custom-connector"></a>2.カスタム コネクタ用に 2 つ目の Azure AD アプリを作成する

2 つ目の Azure AD アプリでは、カスタム コネクタの登録をセキュリティで保護し、1 つ目の Azure AD アプリによって保護された Web API への委任されたアクセスを追加します。 

> [!IMPORTANT]
> 両方の Azure AD アプリが同じディレクトリに存在することを確認します。

1. **[アプリの登録]** の一覧に戻り、**[+ 新しいアプリケーションの登録]** をもう一度選択します。

   ![[アプリの登録]、[+ 新しいアプリケーションの登録] の順に選択する](./media/custom-connector-azure-active-directory-authentication/add-app-registrations2.png)

2. **[作成]** で、下記の表に従って 2 つ目の Azure AD アプリの詳細を入力し、**[作成]** をクリックします。 

   ![Azure AD アプリを作成する](./media/custom-connector-azure-active-directory-authentication/create-app2-registration.png)

   | Setting | 推奨値 | Description | 
   | ------- | --------------- | ----------- | 
   | **名前** | *コネクタ名* | コネクタの Azure AD アプリの名前 | 
   | **アプリケーションの種類** | **Web アプリ/API** | アプリの種類 | 
   | **サインオン URL** | `https://login.windows.net` | | 
   |||| 

3. ディレクトリの **[アプリの登録]** の一覧に戻ったら、2 つ目の Azure AD アプリを選択します。

   ![一覧から 2 つ目の Azure AD アプリを選択する](./media/custom-connector-azure-active-directory-authentication/app2-registration-created.png)

4. アプリの詳細ページが表示されたら、**このアプリの "*アプリケーション ID*" もコピーし、安全な場所に保存する**必要があります。 この ID は後で必要になります。

   ![後で使用するために "アプリケーション ID" を保存する](./media/custom-connector-azure-active-directory-authentication/application-id-app2.png)

5. Azure AD アプリの応答 URL を指定します。 アプリの **[設定]** メニューで、**[応答 URL]** を選択します。 この URL を入力し、**[保存]** をクリックします。

   | Setting | 推奨値 | 
   | ------- | --------------- | 
   | **応答 URL** | Azure Resource Manager のカスタム コネクタの場合、次の URL を入力します。`https://msmanaged-na.consent.azure-apim.net/redirect` | 
   ||| 

   > [!TIP]
   > **[設定]** メニューがまだ表示されていない場合は、ここで **[設定]** を選択します。
   >
   > ![[設定] を選択する](./media/custom-connector-azure-active-directory-authentication/show-app2-settings-menu.png)

6. **[設定]** メニューに戻り、**[必要なアクセス許可]** > **[追加]** を選択します。

   ![[必要なアクセス許可] > [追加]](./media/custom-connector-azure-active-directory-authentication/add-api-access1-select-permissions.png)

7. **[API アクセスの追加]** メニューが開いたら、**[API を選択します]** > 
**[Windows Azure Service Management API]** > **[選択]** を選択します。

   ![API を選択する](./media/custom-connector-azure-active-directory-authentication/add-api-access2-select-api.png)

8. **[API アクセスの追加]** メニューで、**[アクセス許可の選択]** を選択します。 **[委任されたアクセス許可]** で、**[Azure Service Management に対する組織ユーザーとしてのアクセス]** > **[選択]** を選択します。

   ![[委任されたアクセス許可] > [Azure Service Management に対する組織ユーザーとしてのアクセス] を選択する](./media/custom-connector-azure-active-directory-authentication/add-api-access3-select-permissions.png)

   他のオプションは次のとおりです。

   | オプション | 推奨値 | [説明] | 
   | ------ | --------------- | ----------- | 
   | **委任されたアクセス許可** | | Web API への委任されたアクセスのアクセス許可を選択します。 | 
   |||| 

9. **[API アクセスの追加]** メニューで、**[完了]** をクリックします。

   ![[API アクセスの追加] メニュー > [完了]](./media/custom-connector-azure-active-directory-authentication/add-api-access4-done.png)

10. 次に、コネクタの Azure AD アプリの "*クライアント キー*" (シークレット) を生成します。 

    1. **[設定]** メニューに戻り、**[キー]** を選択します。 
    キーの名前 (16 文字以下) を指定し、有効期限を選択して、**[保存]** をクリックします。

       ![クライアント キーを作成する](./media/custom-connector-azure-active-directory-authentication/add-key.png)

    2. 生成されたキーが表示されたら、**[キー]** ページを離れる前に、**このキーをコピーし、安全な場所に保存する**必要があります。
    
       ![キーを手動でコピーして保存する](./media/custom-connector-azure-active-directory-authentication/save-key.png)

11. キーを保存したら、**[設定]** メニューを安全に閉じることができます。

## <a name="3-add-azure-ad-authentication-to-your-web-api-app"></a>手順 3.Web API アプリに Azure AD Authentication を追加する

次に、1 つ目の Azure AD アプリで Web API の認証を有効にします。 詳細については、「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」をご覧ください。

1. [Azure Portal](https://portal.azure.com) で、「[Create custom connectors from Web APIs (Web API からカスタム コネクタを作成する)](../logic-apps/custom-connector-build-web-api-app-tutorial.md)」で公開した Web API アプリを見つけます。

2. **[設定]** で、**[認証/承認]** を選択します。 

   1. **[App Service 認証]** で **[オン]** を選択します。
   2. **[要求が認証されない場合に実行するアクション]** で、**[Azure Active Directory でのログイン]** を選択します。
   3. **[認証プロバイダー]** で、**[Azure Active Directory]** を選択します。 

   ![[認証/承認] を選択する](./media/custom-connector-azure-active-directory-authentication/web-api-app-authentication.png)

3. **[Azure Active Directory の設定]** ページで、次の操作を行います。

   1. **[管理モード]** で **[簡易]** を選択します。

   2. Web API アプリが認証に使用する Azure AD アプリを選択します。 
   **[既存の AD アプリを選択する]** > **[Azure AD アプリ]** を選択します。

   3. **[Azure AD アプリケーション]** で、Web API アプリとして以前に作成した Azure AD アプリを選択します。 
   
   4. **[認証/承認]** ページに戻るまで、**[OK]** をクリックします。

   例: 

   ![Web API アプリである Azure AD アプリを選択する](./media/custom-connector-azure-active-directory-authentication/web-api-app-select-azure-ad-app.png)

4. **[認証/承認]** ページで、**[保存]** をクリックします。

   ![認証設定を保存する](./media/custom-connector-azure-active-directory-authentication/web-api-app-azure-ad-app-save.png)

   Web API アプリで認証に Azure AD を使用できるようになりました。

## <a name="4-set-up-azure-ad-authentication-in-your-web-apis-openapi-file"></a>4.Web API の OpenAPI ファイルで Azure AD Authentication を設定する

Web API アプリの OpenAPI ファイルを開いて、`host` プロパティの下に `securityDefintions` オブジェクトと Azure AD Authentication を追加します。 `host` プロパティと `securityDefinitions` オブジェクトの例を次に示します。

``` json
// Your OpenAPI file header appears here...

"host": "{your-web-api-app-root-url}",
"schemes": [
    "https" // Make sure this is https!
],
"securityDefinitions": {
    "AAD": {
        "type": "oauth2",
        "flow": "accessCode",
        "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
        "tokenUrl": "https://login.windows.net/common/oauth2/token",
        "scopes": {}
    }
},

// Your OpenAPI document continues here...
```
これで、カスタム コネクタを作成して登録する準備ができました。

## <a name="next-steps"></a>次の手順

* [コネクタを登録する](../logic-apps/logic-apps-custom-connector-register.md)
* [カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md)
