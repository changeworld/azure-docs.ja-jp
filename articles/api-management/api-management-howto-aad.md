---
title: Azure Active Directory を使用して開発者アカウントを承認する
titleSuffix: Azure API Management
description: API Management で Azure Active Directory を使用してユーザーを承認する方法について説明します。
services: api-management
documentationcenter: API Management
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/20/2021
ms.author: danlep
ms.openlocfilehash: fd45ad3a77ddfe9bea46c9c816b67be0c2cf1fac
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131579162"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Azure API Management で Azure Active Directory を使用して開発者アカウントを承認する

この記事では、次の方法について学習します。
> [!div class="checklist"]
> * Azure Active Directory (Azure AD) 内のユーザーに対して開発者ポータルへのアクセスを有効にします。
> * Azure AD ユーザーが含まれた外部グループを追加することでそれらのユーザーのグループを管理します。

## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します。
- Azure API Management インスタンスを[インポートして公開](import-and-publish.md)します。

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Azure AD を使用して開発者アカウントを承認する

1. [Azure portal](https://portal.azure.com) にサインインします。 
1. Select ![矢印アイコン。](./media/api-management-howto-aad/arrow.png).
1. **API Management サービス** を検索して選択します。
1. お使いの API Management サービス インスタンスを選択します。
1. **[開発者ポータル]** で **[ID]** を選択します。
1. 上部から **[+ 追加]** を選択して、右側に **[ID プロバイダーの追加]** ウィンドウを開きます。
1. **[種類]** の下で、ドロップダウン メニューから **[Azure Active Directory]** を選択します。
    * 選択すると、他の必要な情報を入力できます。 
    * そのような情報として、 **[クライアント ID]** や **[クライアント シークレット]** があります。 
    * これらのコントロールに関する情報は、記事の後半をご覧ください。
1. 後で使用するために **リダイレクト URL** を保存します。
    
    :::image type="content" source="media/api-management-howto-aad/api-management-with-aad001.png" alt-text="Azure portal で ID プロバイダーを追加する":::

    > [!NOTE]
    > 2 つのリダイレクト URL があります。<br/>
    > * **リダイレクト URL** は、API Management の最新の開発者ポータルを指し示します。
    > * **リダイレクト URL (非推奨のポータル)** は、API Management の非推奨の開発者ポータルを指し示します。
    >
    > 最新の開発者ポータルのリダイレクト URL を使用することをお勧めします。
   
1. ブラウザーの新しいタブで、Azure portal を開きます。 
1. Active Directory にアプリを登録するために、[[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) に移動します。
1. **[新規登録]** を選択します。 **[アプリケーションの登録]** ページで、次のように値を設定します。
    
    * **[名前]** をわかりやすい名前に設定します。 例: *developer-portal*
    * **[サポートされているアカウントの種類]** を **[この組織のディレクトリ内のアカウントのみ]** に設定します。 
    * **[リダイレクト URI]** を手順 9 で保存した値に設定します。 
    * **[登録]** を選択します。 

1.  アプリケーションを登録したら、 **[概要]** ページから **[アプリケーション (クライアント) ID]** をコピーします。 
1. API Management インスタンスが含まれているブラウザー タブに切り替えます。 
1. **[ID プロバイダーの追加]** ウィンドウで、 **[クライアント ID]** ボックスに **[アプリケーション (クライアント) ID]** の値を貼り付けます。
1. [アプリの登録] が含まれているブラウザー タブに切り替えます。
1. 適切なアプリの登録を選択します。
1. サイド メニューの **[管理]** セクションで、 **[証明書とシークレット]** を選択します。 
1. **[証明書とシークレット]** ページの **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択します。 
    * **[Description]\(説明\)** を入力します。
    * **[有効期限]** のオプションを選択します。
    * **[追加]** をクリックします。 
1. ページから離れる前に、クライアント **シークレット値** をコピーします。 この情報は後で必要になります。 
1. サイド メニューの **[管理]** で、 **[認証]** を選択します。
1. **[暗黙的な許可およびハイブリッド フロー]** セクションで、 **[ID トークン]** チェックボックスを選択します。
1. API Management インスタンスが含まれているブラウザー タブに切り替えます。 
1. シークレットを **[ID プロバイダーの追加]** ウィンドウの **[クライアント シークレット]** フィールドに貼り付けます。

    > [!IMPORTANT]
    > キーの有効期限が切れる前に、**クライアント シークレット** を更新します。 

1. **[ID プロバイダーの追加]** ウィンドウの **[許可されているテナント]** フィールドで、API Management サービス インスタンスの API へのアクセス許可を付与する Azure AD インスタンスのドメインを指定します。 
    * 複数のドメインを指定する場合は、改行文字、スペース、またはコンマで区切ります。

    > [!NOTE]
    > **[許可されているテナント]** セクションには、複数のドメインを指定できます。 グローバル管理では、ユーザーが元のアプリの登録ドメインとは異なるドメインからサインインする前に、アプリケーションにディレクトリ データへのアクセス許可を付与する必要があります。 アクセス許可を付与するために、全体管理者は次の操作を行う必要があります。
    > 1. `https://<URL of your developer portal>/aadadminconsent` に移動します (例: `https://contoso.portal.azure-api.net/aadadminconsent`)。
    > 1. アクセス許可を付与する Azure AD テナントのドメイン名を入力します。
    > 1. **[Submit]\(送信\)** をクリックします。 

1.  必要な構成を指定したら、 **[追加]** を選択します。

変更が保存された後、指定した Azure AD インスタンス内のユーザーは、[Azure AD アカウントを使用して開発者ポータルにサインイン](#log_in_to_dev_portal)できます。

## <a name="add-an-external-azure-ad-group"></a>外部 Azure AD グループを追加する

Azure AD テナント内のユーザーに対するアクセス許可を有効にしたら、次のことが可能になります。
* Azure AD グループを API Management に追加する。 
* Azure AD グループを使用して製品の可視性を制御する。

次の手順に従って、次のものを付与します。
* Microsoft Graph API と Azure Active Directory Graph API の `Directory.Read.All` アプリケーション アクセス許可。
* Microsoft Graph の `User.Read` の委任されたアクセス許可 

1. 次の PowerShell スクリプトの最初の 3 行を使用する環境に合わせて更新して実行します。 
   ```powershell
   $subId = "Your Azure subscription ID" #e.g. "1fb8fadf-03a3-4253-8993-65391f432d3a"
   $tenantId = "Your Azure AD Tenant or Organization ID" #e.g. 0e054eb4-e5d0-43b8-ba1e-d7b5156f6da8"
   $appObjectID = "Application Object ID that has been registered in AAD" #e.g. "2215b54a-df84-453f-b4db-ae079c0d2619"
   #Login and Set the Subscription
   az login
   az account set --subscription $subId
   #Assign the following permissions: Microsoft Graph Delegated Permission: User.Read, Microsoft Graph Application Permission: Directory.ReadAll,  Azure Active Directory Graph Application Permission: Directory.ReadAll (legacy)
   az rest --method PATCH --uri "https://graph.microsoft.com/v1.0/$($tenantId)/applications/$($appObjectID)" --body "{'requiredResourceAccess':[{'resourceAccess': [{'id': 'e1fe6dd8-ba31-4d61-89e7-88639da4683d','type': 'Scope'},{'id': '7ab1d382-f21e-4acd-a863-ba3e13f7da61','type': 'Role'}],'resourceAppId': '00000003-0000-0000-c000-000000000000'},{'resourceAccess': [{'id': '5778995a-e1bf-45b8-affa-663a9f3f4d04','type': 'Role'}], 'resourceAppId': '00000002-0000-0000-c000-000000000000'}]}"
   ```
2. ログアウトし、Azure portal にもう一度ログインします。
3. [前のセクション](#authorize-developer-accounts-by-using-azure-ad)で登録したアプリケーションのアプリ登録ページに移動します。 
4. **[API アクセス許可]** をクリックします。 手順 1. で PowerShell スクリプトによって付与されたアクセス許可が表示されます。 
5. **[Grant admin consent for {tenantname}]\({テナント名} に対する管理者の同意を与える\)** を選択して、このディレクトリ内のすべてのユーザーに対するアクセスを許可します。 

これで、外部の Azure AD グループを、API Management インスタンスの **[グループ]** タブから追加できるようになります。

1. サイド メニューの **[開発者ポータル]** で **[グループ]** を選択します。
2. **[Azure AD グループの追加]** ボタンを選択します。

   ![[A A D グループの追加] ボタン](./media/api-management-howto-aad/api-management-with-aad008.png)
1. ドロップダウン リストから **[テナント]** を選択します。 
2. 追加するグループを検索して、選択します。
3. **[選択]** ボタンを押します。

外部 Azure AD グループを追加した後、次のようにしてそのプロパティを確認および構成できます。 
1. **[グループ]** タブからグループの名前を選択します。 
2. グループの **[名前]** と **[説明]** の情報を編集します。
 
これで、構成された Azure AD インスタンスのユーザーが次のことができるようになります。
* 開発者ポータルにサインインする。 
* 可視性があるすべてのグループを表示し、サブスクライブする。

> [!NOTE]
> 「[Microsoft ID プラットフォームでのアクセス許可と同意](../active-directory/develop/v2-permissions-and-consent.md#permission-types)」の記事で、**委任された** アクセス許可と **アプリケーション** のアクセス許可の種類の違いについて詳細情報をご覧ください。

## <a name="developer-portal-add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"></a> 開発者ポータル: Azure AD アカウント認証の追加

開発者ポータルでは、既定の開発者ポータル コンテンツのサインイン ページに含まれる **サインイン ボタン: OAuth** ウィジェットを使用して Azure AD でサインインできます。

新しいユーザーが Azure AD を使用してサインインするときに新しいアカウントが自動的に作成されますが、サインアップ ページに同じウィジェットを追加することを検討できます。 **サインアップ フォーム:OAuth** ウィジェットでは、OAuth によるサインアップに使用されるフォームが示されます。

> [!IMPORTANT]
> Azure AD の変更を有効にするには、[ポータルを再発行](api-management-howto-developer-portal-customize.md#publish)する必要があります。

## <a name="legacy-developer-portal-how-to-sign-in-with-azure-ad"></a>従来の開発者ポータル: Azure AD を使用してサインインする方法

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

前のセクションで構成した Azure AD アカウントを使用して開発者ポータルにサインインするには、次の操作を行います。

1. Active Directory のアプリケーション構成のサインイン URL を使用して新しいブラウザー ウィンドウを開きます。 
2. **[Azure Active Directory]** を選択します。

   ![サインイン ページ][api-management-dev-portal-signin]

1. Azure AD 内のいずれかのユーザーの資格情報を入力します。
2. **[サインイン]** をクリックします。

   ![ユーザー名とパスワードを使用してサインインする][api-management-aad-signin]

1. 登録フォームが表示される場合、必要な追加情報をすべて入力します。 
2. **[サインアップ]** を選択します。

   ![登録フォームの [サインアップ] ボタン][api-management-complete-registration]

ユーザーは、API Management サービス インスタンスの開発者ポータルにサインインしました。

![登録が完了した後の開発者ポータル][api-management-registration-complete]

## <a name="next-steps"></a>次の手順

- [Azure AD で OAuth 2.0 認可を使用して、API Management で Web API バックエンドを保護する](./api-management-howto-protect-backend-with-aad.md)方法を確認します
- [Azure Active Directory と OAuth2.0](../active-directory/develop/authentication-vs-authorization.md) の詳細を確認します。
- API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
- バックエンド サービスを保護するその他の方法については、「[相互証明書認証](./api-management-howto-mutual-certificates.md)」を参照してください。
- [API Management サービス インスタンスの作成](./get-started-create-service-instance.md)。
- [Azure API Management での最初の API の管理](./import-and-publish.md)。

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
