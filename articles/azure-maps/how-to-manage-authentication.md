---
title: Azure Maps での認証の管理 | Microsoft Docs
description: Azure portal を使用して、Azure Maps での認証を管理することができます。
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 68c3c8ac39f5803e01ee1038ec85ddb96ac80b30
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242687"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) または共有キー認証のいずれかをサポートするため、クライアント ID とキーが作成されます。

認証の詳細は、Azure portal で **[設定]** の下の **[認証]** ページに移動することで確認できます。 自分のアカウントに移動します。 次に、メニューで **[認証]** を選択します。


## <a name="view-authentication-details"></a>認証の詳細を表示する

認証の詳細を表示するには、[設定] メニューの **[認証]** オプションに移動します。

![認証の表示](./media/how-to-manage-authentication/how-to-view-auth.png)

 認証と Azure Maps の詳細については、「[Authentication with Azure Maps (Azure Maps での認証)](https://aka.ms/amauth)」をご覧ください。


## <a name="configure-azure-ad-app-registration"></a>Azure AD アプリの登録を構成する

Azure Maps アカウントが作成されたら、お使いの Azure AD テナントと Azure Maps の Azure リソースとの間にリンク関係が必要です。 

1. Azure AD ブレードに移動し、名前とサインイン URL ("https://localhost/" など、Web アプリ / API のホーム ページ) を指定して、アプリの登録を作成します。 登録済みのアプリが既にある場合は、手順 2 に進みます。

    ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

    ![アプリの登録](./media/how-to-manage-authentication/app-create.png)

2. [アプリの登録] の下にあるアプリケーションに移動して **[設定]** をクリックし、委任された API アクセス許可を Azure Maps に割り当てます。  **[必要なアクセス許可]** を選択し、**[追加]** を選択します。 **[API を選択します]** で「Azure Maps」を検索して選択し、**[選択]** をクリックします。

    ![アプリの API アクセス許可](./media/how-to-manage-authentication/app-permissions.png)

3. 最後に、[アクセス許可を選択します] で [Access Azure Maps]\(Azure Maps へのアクセス\) を選んで、[選択] をクリックします。

    ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

4. 認証の実装に応じて、手順 a または b を行います。

    1. アプリケーションで Azure Maps Web SDK とユーザー トークン認証を使用する予定の場合は、アプリの登録の詳細ページの [マニフェスト] セクションで `oauthEnableImplicitFlow` を true に設定し、これを有効にする必要があります。
    
       ![アプリ マニフェスト](./media/how-to-manage-authentication/app-manifest.png)

    2. アプリケーションでサーバー/アプリケーション認証を使用する場合は、アプリの登録内の **[キー]** ブレードに移動し、パスワードを作成するか、公開キー証明書をアプリの登録にアップロードします。 パスワードを作成する場合、パスワードを一旦**保存**してから後のためにコピーし、安全に保管します。これは Azure AD からトークンを取得するために使用します。

       ![アプリ キー](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Azure Maps に RBAC を付与する

Azure Maps アカウントがお使いの Azure AD テナントに関連付けられたら、使用可能な Azure Maps のアクセス制御ロールにユーザーまたはアプリケーションを割り当てることで、アクセス制御を付与することができます。

1. **[アクセス制御]** オプションに移動し、**[ロールの割り当て]** をクリックして、**[ロール割り当ての追加]** をクリックします。

    ![RBAC の付与](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. [ロールの割り当て] ポップアップ ウィンドウで、**[ロール]** に [Azure Maps Date Reader (プレビュー)] を、**[アクセスの割り当て先]** に[Azure AD のユーザー、グループ、サービス プリンシパル] を選択し、**[選択]** ドロップダウンからユーザーまたはアプリケーションを選んで**保存**します。

    ![ロールの割り当ての追加](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>使用可能な Azure Maps RBAC ロールを表示する

アクセスを許可を付与できる Azure Maps の使用可能なロール ベース アクセス制御ロールを表示するには、**[アクセス制御 (IAM)]** オプションに移動し、**[ロール]** をクリックして、「**Azure Maps**」で始まるロールを検索します。

![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Azure Maps のロールベースのアクセス制御 (RBAC) を表示する

Azure AD では、ロールベースのアクセス制御 (RBAC) を通じたきめ細かなアクセス制御が可能です。 

Azure Maps の RBAC が付与されているユーザーまたはアプリを表示するには、**[アクセス制御 (IAM)]** オプションに移動し、**[ロールの割り当て]** を選択して、「**Azure Maps**」でフィルター処理します。 使用可能なすべてのロールが下に表示されます。

![RBAC の表示](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

アプリが登録され、Azure Maps に関連付けられたら、アクセス トークンを要求することができます。

* アプリケーションで Azure Maps Web SDK (Web) とユーザー トークン認証を使用する予定の場合、Azure Maps クライアント ID と Azure AD アプリ ID を使って html ページを構成する必要があります。

* サーバー/アプリケーションの認証を使用するアプリケーションの場合、Azure AD ログイン エンドポイント `https://login.microsoftonline.com` から、Azure AD リソース ID `https://atlas.microsoft.com/`、Azure Maps クライアント ID、Azure AD アプリ ID、および Azure AD アプリ登録のパスワードまたは証明書を使ってトークンを要求します。

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。


## <a name="next-steps"></a>次の手順

* Azure AD の認証と Azure Maps Web SDK の詳細については、[Azure AD と Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) に関するページをご覧ください。