---
title: "Azure Active Directory B2C: カスタム ポリシーを使用して Azure AD B2C ユーザー インターフェイス (UI) を動的にカスタマイズする"
description: "実行時に動的に変化する HTML/CSS コンテンツで複数のブランド エクスペリエンスをサポートする方法"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: joroja
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 09/20/2017
ms.author: yoelh
ms.openlocfilehash: cbce9b72c25c90dde526ff229f77a85ce9478efa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-the-ui-with-dynamic-content-using-custom-policies"></a>Azure Active Directory B2C: カスタム ポリシーを使用して動的コンテンツを含む UI を構成する
Azure AD B2C カスタム ポリシーを使用すると、クエリ文字列でパラメーターによる送信が可能です。 そのパラメーターは HTML エンドポイントに渡され、ページのコンテンツを動的に変更できます。 たとえば、Web/モバイル アプリケーションから渡すパラメーターに基づいて、B2C サインアップまたはサインインの背景イメージを変更できます。 

## <a name="prerequisites"></a>前提条件
この記事では、カスタム ポリシーを使用して、**動的コンテンツ**を含む Azure AD B2C ユーザー インターフェイスをカスタマイズする方法に重点を置いて説明します。 カスタム ポリシーでの UI カスタマイズを開始するには、[カスタム ポリシーでの UI カスタマイズ](active-directory-b2c-ui-customization-custom.md)に関する記事をご覧ください。 

>[!NOTE]
>
>  「[カスタム ポリシーでの UI カスタマイズの構成](active-directory-b2c-ui-customization-custom.md)」では、カスタム ポリシーを使用した Azure AD B2C UI カスタマイズの基本について説明します。
> * ページのユーザー インターフェイス (UI) カスタマイズ機能
> * サンプル コンテンツを使用してページの UI カスタマイズ機能をテストするためのツール。
> * ページの種類ごとの基本的な UI 要素。
> * この機能を実行するときのベスト プラクティス。

## <a name="adding-a-link-to-html5css-templates-to-your-user-journey"></a>HTML5/CSS テンプレートへのリンクをユーザー体験に追加する

カスタム ポリシーでは、コンテンツ定義が、指定した UI の手順に使用される HTML5 ページの URI を定義します  (例: サインインまたはサインアップ ページ)。 基本ポリシーは、HTML5 ファイルの URI を指定することで、既定の外観を定義します (その CSS を参照します)。 拡張機能ポリシーで、その外観を変更するには、HTML5 ファイルの LoadUri をオーバーライドします。 そのため、コンテンツ定義には、必要に応じて HTML5/CSS ファイルを作成することで定義される、外部コンテンツへの URL が含まれます。 

`ContentDefinitions` セクションには一連の`ContentDefinition` XML 要素が含まれます。 `ContentDefinition` 要素の ID 属性は、コンテンツ定義に関連するページの種類を指定します。 したがって、カスタム HTML5/CSS テンプレートが使用されるコンテキストが指定されます。 次の表に、IEF エンジンで検出されたコンテンツ定義 ID のセットと、それに関連するページの種類を示します。

| コンテンツ定義 ID | 既定の HTML5 テンプレート| Description | 
|-----------------------|--------|-------------|
| *api.error* | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **エラー ページ**。 このページは、例外またはエラーが発生した場合に表示されます。 |
| *api.idpselections* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **ID プロバイダーの選択ページ**。 このページには、サインイン時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、通常、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.idpselections.signup* | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **サインアップのための ID プロバイダーの選択**。 このページには、サインアップ時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.localaccountpasswordreset* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **パスワードを忘れた場合**。 このページには、ユーザーがパスワードのリセットを開始するために入力する必要があるフォームが含まれます。  |
| *api.localaccountsignin* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインイン ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのサインイン フォームが含まれます。 このフォームには、テキスト入力ボックスとパスワード入力ボックスを含めることができます。 |
| *api.localaccountsignup* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ローカル アカウントのサインアップ ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントのサインアップに使用されるサインアップ フォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなど、さまざまな入力コントロールを含めることができます。 |
| *api.phonefactor* | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多要素認証ページ**。 このページでは、ユーザーがサインアップまたはサインインするときに、(文字または音声を使用して) 電話番号を確認することができます。 |
| *api.selfasserted* | [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **ソーシャル アカウントのサインアップ ページ**。 このページには、ソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、ユーザーが入力する必要のあるサインアップ フォームが含まれます。 このページは、パスワード入力フィールドを除いて、上記のソーシャル アカウントのサインアップ ページに似ています。 |
| *api.selfasserted.profileupdate* | [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **プロファイルの更新ページ**。 このページには、ユーザーがプロファイルの更新に使用できるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、ソーシャル アカウントのサインアップ ページに似ています。 |
| *api.signuporsignin* | [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統合されたサインアップ ページまたはサインイン ページ**。 このページは、ユーザーのサインアップとサインインの両方を処理します。 ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかを使用できます。  |

## <a name="serving-dynamic-content"></a>動的コンテンツを配信する
「[カスタム ポリシーでの UI カスタマイズの構成](active-directory-b2c-ui-customization-custom.md)」では、HTML5 ファイルを Azure Blob Storage にアップロードしました。 こうした HTML5 ファイルは静的で、各要求で同じ HTML コンテンツをレンダリングします。 この記事では、クエリ文字列パラメーターを受け入れて、適宜応答できる ASP.Net Web アプリを使用します。 このチュートリアルでは次を行います。
* HTML5 テンプレートをホストする ASP.NET Core Web アプリケーションを作成する 
* カスタム HTML5 テンプレート _unified.cshtml_ を追加する 
* Web アプリを Azure サービス アプリに発行する 
* Web アプリに対して CORS を設定する
* `LoadUri` 要素をオーバーライドして、HTML5 ファイルを指定する

## <a name="step-1-create-an-aspnet-web-app"></a>手順 1: ASP.NET Web アプリを作成する

1.  Visual Studio で、 **[ファイル]、[新規作成]、[プロジェクト]** の順にクリックして、プロジェクトを作成します。
2.  **[新しいプロジェクト]** ダイアログで、**[Visual C#]、[Web]、[ASP.NET Core Web アプリケーション (.NET Core)]** の順にクリックします。
3.  アプリケーションに Contoso.AADB2C.UI などの名前を付けて、**[OK]** を選択します。

    ![新しい Visual Studio プロジェクトの作成](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project1.png)

4.  **Web アプリケーション** テンプレートを選択します
5.  認証が **[認証なし]** に設定されていることを確認します

    ![Web API テンプレートの選択](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-create-project2.png)

6.  **[OK]** をクリックして、プロジェクトを作成します

## <a name="step-2-create-mvc-view"></a>手順 2: MVC ビューを作成する
### <a name="step-21-download-b2c-built-in-html5-template"></a>手順 2.1: B2C 組み込み HTML5 テンプレートをダウンロードする
カスタム HTML5 テンプレートは、B2C 組み込み HTML5 テンプレートに基づいています。 [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) をダウンロードするか、[スターター パック](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates/wingtip)からダウンロードできます。 この HTML5 ファイルを、統合されたサインアップ ページまたはサインイン ページに使用します。

### <a name="step-22-add-mvc-view"></a>手順 2.2: MVC ビューを追加する
1. [ビュー] > [ホーム] フォルダーを右クリックし、**[追加] > [新しいアイテム]** の順に選択します。
 ![新しい MVC アイテムの追加](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view1.png)
2. **[新しいアイテムの追加 - Contoso.AADB2C.UI]** ダイアログで、**[Web] > [ASP] > [NET]** の順に選択します
3. **[MVC ビュー ページ]** をタップします
4. **[名前]** ボックスで、名前を _unified.cshtml_ に変更します。
5. **[追加]** 
  ![[Add MVC view]\(MVC ビューの追加\)](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-view2.png) の順にクリックします
6.  このファイルがまだ開いていない場合は、ファイルをダブルクリックして開きます。 _unified.cshtml_ のコンテンツをクリアします。
7. ここではデモのため、レイアウト ページへの参照を削除します。 次のコード スニペットを _unified.cshtml_ に追加します

    ```C#
    @{
        Layout = null;
    }
    ```

8. AAD B2C 組み込み HTML5 テンプレートからダウンロードした _unified.cshtml_ ファイルを開きます。
9. `@` 記号を見つけて、`@@` に置き換えます
10. ファイルのコンテンツをコピーして、レイアウト定義の下に貼り付けます。 コードのようになります。![HTML5 を追加した後の unified.cshtml ファイル](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-edit-view1.png)
### <a name="step-23-change-the-background-image"></a>手順 2.3: 背景イメージを変更する
10. ID `background_background_image` の `<img>` 要素を見つけ、`src` を _https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1_、または任意の背景イメージに指定します。
![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-static-background.png)
### <a name="step-24-add-you-view-to-mvc-controller"></a>手順 2.4: MVC コントローラーにビューを追加する
**Controllers\HomeController.cs** を開いて、次のメソッドを追加します。 
```C
public IActionResult unified()
{
    return View();
}
```
このコードは、メソッドがビュー テンプレート ファイルを使用して、ブラウザーへの応答をレンダリングする必要があることを指定します。 ビュー テンプレート ファイルの名前は明示的には指定しないため、MVC では、[ビュー] > [ホーム] フォルダーの_unified.cshtml_ ビュー ファイルを使用するように既定で設定されます。

_unified_ メソッドを追加すると、コードは次のようになります。![コントローラーに変更してビューをレンダリング](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-controller-view.png)

Web アプリをデバッグし、_unified_ ページにアクセスできることを確認します。 たとえば、 `http://localhost:<Port number>/Home/unified`

### <a name="step-25-publish-to-azure"></a>手順 2.5: Azure に発行する
1.  **ソリューション エクスプローラー**で **Contoso.AADB2C.UI** プロジェクトを右クリックし、**[発行]** を選択します。

    ![Microsoft Azure App Service への発行](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish1.png)

2.  **[Microsoft Azure App Service]** が選択されていることを確認し、**[発行]** をクリックします。

    ![新しい Microsoft Azure App Service の作成](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish2.png)

    **[App Service の作成]** ダイアログ ボックスでは、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースを作成できます。

> [!NOTE]
>
>発行方法の詳細については、「[Azure に ASP.NET Web アプリを作成する](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-get-started-dotnet#publish-to-azure)」を参照してください

3.  **[Web アプリ名]** に一意のアプリ名 (有効な文字は `a-z`、`0-9`、および `-`) を入力します。 Web アプリの URL は`http://<app_name>.azurewebsites.NET`です。`<app_name>` には Web アプリの名前を指定します。 自動的に生成される名前をそのまま使用してもかまいません。この名前は一意になっています。

    **[作成]** をクリックして、Azure リソースの作成を開始します。

    ![App Service プロパティの入力](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-publish3.png)

4.  ウィザードの完了後に、Azure に ASP.NET Web アプリを発行してから、既定のブラウザーでアプリを起動し、_unified_ ページの URL をコピーします  (例: _https://<app_name>.azurewebsites.net/home/unified_)

## <a name="step-3-configure-cors-in-azure-app-service"></a>手順 3: Azure App Service で CORS を構成する
1. ブラウザーで [Azure Portal](https://portal.azure.com/) に移動します

2. **[App Services]**をクリックした後、API アプリの名前をクリックします。

    ![Select API app in portal](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS1.png)

3. **[設定]** セクションで下にスクロールし、**[API]** セクションを見つけたら **[CORS]** をクリックします。

    ![CORS 設定の選択](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS2.png)

4. テキスト ボックスに、JavaScript の呼び出し元として許可する URL を入力します。
または、アスタリスク (*) を入力して、元のドメインをすべて受け入れるように指定します。

5. [ **Save**] をクリックします。

    ![[保存] をクリックします。](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-CORS3.png)

[保存] をクリックすると、API アプリは、指定した URL からの JavaScript 呼び出しを受け入れるようになります。 

## <a name="step-4-html5-template-validation"></a>手順 4: HTML5 テンプレートの検証
HTML5 テンプレートを使用する準備はできています。  ただし、どの `ContentDefinition` でも使用できません。 `ContentDefinition` をカスタム ポリシーに追加する前に、次を行う必要があります。
* コンテンツが HTML5 に準拠しており、アクセス可能であることを確認する。
* コンテンツ サーバーで CORS が有効になっていることを確認する。

>[!NOTE]
>
>コンテンツをホストしているサイトで CORS が有効になっていることを確認し、CORS 要求をテストするには、サイト http://test-cors.org/ を使用します。 

* 配信されるコンテンツは **HTTPS** により安全です。
* すべてのリンクと CSS コンテンツおよびイメージで、https://yourdomain/content などの**絶対 URL** を使用します。

## <a name="step-5-configure-your-content-definition"></a>手順 5: コンテンツ定義を構成する
`ContentDefinition` を構成するには
1.  ポリシーの基本ファイルを開きます (例: TrustFrameworkBase.xml)。
2.  `<ContentDefinitions>` 要素を見つけて、`<ContentDefinitions>` ノードのコンテンツ全体をコピーします。
3.  拡張ファイル (例: TrustFrameworkExtensions.xml) を開き、`<BuildingBlocks>` 要素を見つけます。 要素が存在しない場合は追加します。
4.  コピーした `<ContentDefinitions>` ノードのコンテンツ全体を `<BuildingBlocks>` 要素の子として貼り付けます。 
5.  コピーしたばかりの XML で、`Id="api.signuporsignin"` を含む `<ContentDefinition>` ノードを見つけます。
6.  `LoadUri` の値を _~/tenant/default/unified_ から _https://app_name.azurewebsites.net/home/unified_ に変更します。カスタム ポリシーは次のようになります。![コンテンツ定義](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-content-definition.png)

## <a name="step-6-upload-the-policy-to-your-tenant"></a>手順 6: ポリシーをテナントにアップロードする
1.  [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキストに切り替え](active-directory-b2c-navigate-to-b2c-context.md)、**[Azure AD B2C]** を開きます
2.  **[Identity Experience Framework]** を選択します。
3.  **[すべてのポリシー]** を開きます。
4.  **[ポリシーのアップロード]** を選択します。
5.  **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。
6.  TrustFrameworkExtensions.xml を**アップロード**し、検証に失敗しないことを確認します。

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>手順 7: [今すぐ実行] を使用してカスタム ポリシーをテストする
1.  **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** に移動します。

    >[!NOTE]
    >
    >    **[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 
    >    アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。
    >

2.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。
3.  以前に作成した背景でカスタム HTML5 を表示できます![サインアップまたはサインイン ポリシー](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo1.png)

## <a name="step-8-adding-dynamic-content"></a>手順 8: 動的コンテンツを追加する
このセクションでは、_campaignId_ という名前のクエリ文字列パラメーターに基づいて背景を変更します。 パラメーターは、証明書利用者アプリケーション (Web/モバイル アプリ) によって AAD B2C に送信されます。 使用しているポリシーが、このパラメーターを読み取り、値を HTML5 テンプレートに送信します 


### <a name="step-81-adding-content-definition-parameter"></a>手順 8.1: コンテンツ定義パラメーターを追加する

`ContentDefinitionParameters` 要素を追加するには:
1.  ポリシーの SignUpOrSignin ファイル (SignUpOrSignin.xml など) を開きます。
2.  `<UserJourneyBehaviors>` ノードを見つけます 
4.  `<UserJourneyBehaviors>` 内の次の XML スニペットを追加します 

    ```XML
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    </ContentDefinitionParameters>
    ```

### <a name="step-82-change-your-code-to-accept-query-string-parameter-and-replace-the-background-image-accordingly"></a>手順 8.2: クエリ文字列パラメーターを受け入れるようにコードを変更し、適宜背景イメージを置き換える
このセクションでは、campaignId パラメーターを受け入れるように HomeController _unified_ メソッドを変更します。 このメソッドはその値を確認して、適宜 `ViewData["background"]` 変数を設定します。

1. **Controllers\HomeController.cs** を開いて、次のコード スニペットで `unified` メソッドを変更します。

    ```C#
    public IActionResult unified(string campaignId)
    {
        // If campaign ID is Hawaii, show Hawaii background
        if (campaignId != null && campaignId.ToLower() == "hawaii")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19889_en_1";
        }
        // If campaign ID is Tokyo, show Tokyo background
        else if (campaignId != null && campaignId.ToLower() == "tokyo")
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/19666_en_1";
        }
        // Default background
        else
        {
            ViewData["background"] = "https://kbdevstorage1.blob.core.windows.net/asset-blobs/18983_en_1";
        }

        return View();
    }

    ```

2. ID `background_background_image` の `<img>` 要素を見つけて、`src` を `@ViewData["background"]` に指定します。

    ![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-add-dynamic-background.png)

### <a name="83-upload-the-changes-and-publish-your-policy"></a>8.3: 変更をアップロードして、ポリシーを発行する
1. Visual Studio プロジェクトを Azure サービス アプリに発行します
2. SignUpOrSignIn.xml ポリシーを AAD B2C にアップロードします
3.  アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開きます。 **[今すぐ実行]** を選択します。 前の同じ背景イメージが表示されます。
4. ブラウザーのアドレス バーから URL をコピーします
5. _campaignId_ クエリ文字列パラメーターを URI に追加します。 たとえば、次の図に示すように、`&campaignId=hawaii` を追加します。![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-campaignId-param.png)
6. **Enter** キーを押すと、ページにハワイの背景が表示されます![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo2.png)
7. ここで、値を *Tokyo* に変更して **Enter** キーを押すと、 ページに東京の背景が表示されます![ページ背景の変更](media/active-directory-b2c-ui-customization-custom-dynamic/aadb2c-ief-ui-customization-demo3.png)

## <a name="step-9-change-the-rest-of-the-user-journey"></a>手順 9: 残りのユーザー体験を変更する
サインイン ページに移動し、**[Sign-up now]\(今すぐサインアップ\)** リンクをクリックすると、定義した背景ではなく、既定の背景が表示されます。 このように動作するのは、サインアップまたはサインイン ページのみ変更したが、残りの自己アサート コンテンツ定義も変更する必要があるためです。 これを行うには、次の手順を参照してください。
* 手順 2:
    * **selfasserted** ファイルをダウンロードします。
    * ファイル コンテンツをコピーします。
    * 新しいビュー **selfasserted** を作成します。
    * **selfasserted** を **ホーム** コントローラーに追加します。
* 手順 4: 
    * 拡張機能ポリシーで、`Id="api.selfasserted"`、`Id="api.localaccountsignup"`、および `Id="api.localaccountpasswordreset"` を含む `<ContentDefinition>` ノードを見つけます
    *  `LoadUri` 属性を selfasserted Uri に設定します。
* 手順 8.2: クエリ文字列パラメーターを受け入れるようにコードを変更します 
* TrustFrameworkExtensions.xml ポリシーをアップロードし、検証に失敗しないことを確認します。
* ポリシーのテストを実行し、**[Sign-up now]\(今すぐサインアップ\)** をクリックして、結果を確認します

## <a name="optional-download-the-complete-policy-files-and-code"></a>完全なポリシー ファイルとコードをダウンロードする (省略可能)
* これらのサンプル ファイルを使う代わりに、カスタム ポリシーの概要チュートリアルの完了後に独自のカスタム ポリシーを使ってシナリオを構築することをお勧めします。  [参照用のサンプル ポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-ui-customization)からダウンロードできます




