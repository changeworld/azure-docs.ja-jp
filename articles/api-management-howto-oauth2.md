<properties pageTitle="How to authorize developer accounts using OAuth 2.0 in Azure API Management" metaKeywords="" description="Learn how to authorize users using OAuth 2.0 in API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to authorize developer accounts using OAuth 2.0 in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Azure API Management の OAuth 2.0 を使用して開発者アカウントを認証する方法

API Management には、ユーザーが OAuth 2.0 認証を使用して開発者ポータルにアクセスするための機能が備わっています。このガイドは、OAuth 2.0 認証を使用するように API Management サービス インスタンスを構成する方法を説明しています。

> OAuth 2.0 について詳しくは、[][]<http://oauth.net/2/></a> で仕様を参照してください。

## このトピックの内容

-   [前提条件][前提条件]
-   [API Management で OAuth 2.0 認証サーバーを構成する][API Management で OAuth 2.0 認証サーバーを構成する]
-   [OAuth 2.0 ユーザー認証を使用するように API を構成する][OAuth 2.0 ユーザー認証を使用するように API を構成する]
-   [開発者ポータルで OAuth 2.0 ユーザー認証をテストする][開発者ポータルで OAuth 2.0 ユーザー認証をテストする]

## <a name="prerequisites"> </a>前提条件

このガイドでは、開発者アカウントに OAuth 2.0 認証を使用するように API Management サービス インスタンスを構成する方法を説明していますが、OAuth 2.0 プロバイダーを構成する方法は説明していません。手順は似ていて、API Management サービス インスタンスでの OAuth 2.0 の構成に使用される情報は同じですが、各 OAuth 2.0 プロバイダーの構成は異なっています。このトピックは、Azure Active Directory を OAuth 2.0 プロバイダーとして使用する例を示しています。

> Azure Active Directory を使用して OAuth 2.0 を構成する方法について詳しくは、[WebApp-GraphAPI-DotNet][WebApp-GraphAPI-DotNet] のサンプルを参照してください。

## <a name="step1"> </a>API Management で OAuth 2.0 認証サーバーを構成する

最初に、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

![API Management console][API Management console]

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

左側の **[API Management]** メニューで **[セキュリティ]** をクリックし、**\[OAuth 2.0]** をクリックしてから、**[認証サーバーの追加 (Add authorization server)]** をクリックします。

![OAuth 2.0][OAuth 2.0]

**[認証サーバーの追加 (Add authorization server)]** をクリックした後に、新しい認証サーバーのフォームが表示されます。

![新しいサーバー][新しいサーバー]

**[名前]** フィールドと **[説明]** フィールドに、名前とオプションの説明を入力します。

> これらのフィールドは、OAuth 2.0 認証サーバーを現在の API Management サービス インスタンス内で識別するために使用されるもので、それらの値が OAuth 2.0 サーバーによって自動入力されることはありません。

**[クライアント登録ページ URL (Client registration page URL)]** を入力します。このページでは、ユーザーがアカウントを作成して管理できます。このページは使用される OAuth 2.0 プロバイダーによって異なります。

フォームの次のセクションには、**[認証コード付与タイプ (Authorization code grant types)]**、**[認証エンドポイント URL (Authorization endpoint URL)]**、および **[認証要求方式 (Authorization request method)]** 設定が含まれます。

![新しいサーバー][1]

必要なタイプにチェックマークを入れて、**[認証コード付与タイプ (Authorization code grant types)]** を指定します。**[認証コード]** が既定で指定されています。

**[認証エンドポイント URL (Authorization endpoint URL)]** を入力します。Azure Active Directory では、この URL は以下の URL のようになります。ここで、`<client_id>` は、使用するアプリケーションを OAuth 2.0 サーバーが識別するためのクライアント ID に置き換わります。

    https://login.windows.net/<client_id>/oauth2/authorize

**[認証要求方式 (Authorization request method)]** は、認証要求が OAuth 2.0 サーバーに送信される方法を指定します。既定では **[GET]** が選択されています。

次のセクションでは、**[トークン エンドポイント URL (Token endpoint URL)]**、**[クライアント認証方式(Client authentication methods)]**、**[アクセス トークン送信方式 (Access token sending method)]**、および **[既定のスコープ (Default scope)]** を指定します。

![新しいサーバー][2]

Azure Active Directory OAuth 2.0 サーバーでは、**[トークン エンドポイント URL (Token endpoint URL)]** の形式が以下のようになります。ここで、`<APPID>` の形式は `yourapp.onmicrosoft.com` です。

    https://login.windows.net/<APPID>/oauth2/token

既定の設定は、**[クライアント認証方式(Client authentication methods)]** が **[基本 (Basic)]**、**[アクセス トークン送信方式 (Access token sending method)]** が **[認証ヘッダー (Authorization header)]** です。これらの値は、**[既定のスコープ (Default scope)]** と共に、フォームのこのセクションで構成されます。

**[クライアントの資格情報]** セクションには **[クライアント ID]** と **[クライアント シークレット]** が含まれます。これらは OAuth 2.0 サーバーの作成と構成のプロセスで取得されます。**[クライアント ID]** と **[クライアント シークレット]** が指定された後に、**[認証コード]** の **redirect\_uri** が生成されます。この URI は、OAuth 2.0 サーバー構成で応答 URL を構成するために使用されます。

![新しいサーバー][3]

**[認証コード付与タイプ (Authorization code grant types)]** が **[リソース所有者パスワード (Resource owner password)]** に設定された場合、**[リソース所有者パスワードの資格情報 (Resource owner password credentials)]** セクションがそれらの資格情報の指定に使用されます。そうでない場合は、そのセクションを空白のままにすることができます。

![新しいサーバー][4]

このフォームが完了したら、**[保存]** をクリックして API Management OAuth 2.0 認証サーバーの構成を保存します。サーバーの構成が保存された後、次のセクションで説明されているように、この構成を使用するように API を構成できます。

## <a name="step2"> </a>OAuth 2.0 ユーザー認証を使用するように API を構成する

左側の **[API Management]** メニューで **[API]** をクリックし、必要な API の名前をクリックし、**[セキュリティ]** をクリックしてから、**\[OAuth 2.0]** のボックスにチェックマークを入れます。

![ユーザー認証][ユーザー認証]

必要な **[認証サーバー (Authorization server)]** をドロップダウン リストで選択して、**[保存]** をクリックします。

![ユーザー認証][5]

## <a name="step3"> </a>開発者ポータルで OAuth 2.0 ユーザー認証をテストする

OAuth 2.0 認証サーバーを構成して、そのサーバーを使用するように API を構成した後、開発者ポータルに移動して API を呼び出すことにより、そのサーバーをテストできます。右上のメニューで、**\[開発者ポータル]** をクリックします。

![開発者ポータル][開発者ポータル]

上部のメニューで **[API]** をクリックし、**\[Echo API]** を選択します。

![Echo API][Echo API]

> アカウントに対して構成されている (またはアカウントから見える) API が 1 つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。

**[GET Resource]** 操作を選択し、**[コンソールを開く]** をクリックして、ドロップダウンで **[認証コード]** を選択します。

![Open console][Open console]

**[認証コード]** が選択されると、OAuth 2.0 プロバイダーのサインイン フォームがあるポップアップ ウィンドウが表示されます。この例では、サインイン フォームは Azure Active Directory によって提供されています。

> ポップアップが無効になっている場合は、それを有効にするように伝えるプロンプトがブラウザーによって出されます。ポップアップを有効にした後に、再び **[認証コード]** を選択すると、サインイン フォームが表示されます。

![サインイン][サインイン]

サインインした後、**[要求ヘッダー (Request headers)]** には、要求を認証するための `Authorization : Bearer` ヘッダーが取り込まれます。

![要求ヘッダー トークン][要求ヘッダー トークン]

これで、残りのパラメーター用に必要な値を構成して、要求を送信できます。

  []: http://oauth.net/2/
  [前提条件]: #prerequisites
  [API Management で OAuth 2.0 認証サーバーを構成する]: #step1
  [OAuth 2.0 ユーザー認証を使用するように API を構成する]: #step2
  [開発者ポータルで OAuth 2.0 ユーザー認証をテストする]: #step3
  [WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
  [API Management console]: ./media/api-management-howto-oauth2/api-management-management-console.png
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [OAuth 2.0]: ./media/api-management-howto-oauth2/api-management-oauth2.png
  [新しいサーバー]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
  [1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
  [2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
  [3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
  [4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
  [ユーザー認証]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
  [5]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
  [開発者ポータル]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
  [Echo API]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png
  [Open console]: ./media/api-management-howto-oauth2/api-management-open-console.png
  [サインイン]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
  [要求ヘッダー トークン]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
