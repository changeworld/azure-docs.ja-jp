---
title: 認証の管理 | Microsoft Azure Maps
description: Azure portal を使用して、Microsoft Azure Maps での認証を管理できます。
author: walsehgal
ms.author: v-musehg
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1f7f128898089292a8ccd92686af5d68fe328f3c
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766205"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) および共有キー認証をサポートするため、クライアント ID とキーが作成されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

Azure Maps アカウントの作成後、プライマリ キーおよびセカンダリ キーが生成されます。 サブスクリプション キーとして主キーを使用します。この名前は同じ意味で使用されることがあります。 セカンダリ キーは、キー変更のローリングなどのシナリオで使用できます。 どちらの方法でも、Azure Maps を呼び出すにはキーが必要です。 このプロセスは[共有キー認証](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication)と呼ばれます。 共有キーと Azure AD 認証の詳細については、「[Azure Maps による認証](https://aka.ms/amauth)」を参照してください。

認証の詳細は、Azure portal で確認できます。 自分のアカウントにアクセスして、 **[設定]** メニューで **[認証]** を選択します。

![認証の詳細](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="set-up-azure-ad-app-registration"></a>Azure AD アプリの登録を設定する

Azure Maps アカウントを作成したら、Azure AD テナントと Azure Maps リソース間のリンクを確立する必要があります。

1. ポータル メニューの **[Azure Active Directory]** を選択します。 登録の名前を指定します。 **[アプリの登録]** をクリックしてから、 **[新規登録]** をクリックします。 **[リダイレクト URI]** ボックスで、Web アプリのホームページを指定します。 たとえば、「 https://localhost/ 」のように入力します。 登録済みのアプリが既にある場合は、手順 2 に進みます。

    ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

    ![アプリの登録の詳細](./media/how-to-manage-authentication/app-create.png)

2. 委任された API アクセス許可を Azure Maps に割り当てるには、 **[アプリの登録]** の下にあるアプリケーションに移動して **[API のアクセス許可]** を選択します。 **[アクセス許可の追加]** を選択します。 **[API を選択する]** で「**Azure Maps**」を検索して選択します。

    ![アプリの API アクセス許可](./media/how-to-manage-authentication/app-permissions.png)

3. **[アクセス許可を選択]** を選択し、 **[ユーザーの権限借用]** のチェックボックスをオンにして、下部にある **[選択]** ボタンをクリックします。

    ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

4. 使用する認証方法に応じて、手順 a または b を完了します。

    1. アプリケーションで Azure Maps Web SDK とユーザー トークン認証を使用する場合は、アプリの登録の [マニフェスト] セクションで `oauth2AllowImplicitFlow` を true に設定し、これを有効にします。
    
       ![アプリ マニフェスト](./media/how-to-manage-authentication/app-manifest.png)

    2. アプリケーションでサーバー/アプリケーション認証を使用する場合は、アプリの登録の **[証明書とシークレット]** ブレードに移動し、パスワードを作成するか、公開キー証明書をアプリの登録にアップロードします。 パスワードを作成する場合は、後で使用できるように安全に保管してください。 Azure AD からトークンを取得するには、このパスワードを使用します。

       ![アプリ キー](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>ロールベースのアクセス制御 (RBAC) を Azure Maps に付与する

Azure AD テナントに Azure Maps アカウントを関連付けると、アクセス制御を許可できます。 アクセス制御を許可するには、1 つまたは複数の Azure Maps アクセス制御ロールにユーザー、グループ、またはアプリケーションを割り当てます。

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]** を選択し、 **[ロールの割り当て]** を選択します。

    ![RBAC の付与](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** ウィンドウの **[ロール]** の下で、 **[Azure Maps Date Reader (Preview)]\(Azure Maps Date Reader (プレビュー)\)** を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 ユーザーまたはアプリケーションを選択します。 **[保存]** を選択します。

    ![ロールの割り当ての追加](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>使用可能な Azure Maps RBAC ロールを表示する

Azure Maps に使用できるロールベースのアクセス制御 (RBAC) ロールを表示するには、 **[アクセス制御 (IAM)]** に移動し、 **[ロール]** を選択し、「**Azure Maps**」で始まるロールを検索します。 これらのロールは、アクセス権を付与することができるロールです。

![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC を表示する

RBAC は詳細なアクセス制御を提供します。

Azure Maps の RBAC が付与されているユーザーまたはアプリを表示するには、 **[アクセス制御 (IAM)]** に移動し、 **[ロールの割り当て]** を選択して、「**Azure Maps**」でフィルター処理します。

![RBAC が付与されたユーザーとアプリのビュー](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

アプリを登録して Azure Maps と関連付けると、アクセス トークンを要求することができます。

* アプリケーションで Azure Maps Web SDK とユーザー トークン認証を使用する場合、Azure Maps クライアント ID と Azure AD アプリ ID を使って HTML ページを構成する必要があります。

* アプリケーションでサーバー/アプリケーション認証を使用する場合は、Azure AD リソース ID `https://atlas.microsoft.com/`、Azure Maps クライアント ID、Azure AD アプリ ID、および Azure AD アプリ登録のパスワードまたは証明書を使って、Azure AD トークン エンドポイント `https://login.microsoftonline.com` にトークンを要求する必要があります。

| Azure 環境   | Azure AD トークン エンドポイント | Azure リソース ID |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。


## <a name="next-steps"></a>次のステップ

Azure AD の認証と Azure Maps Web SDK の詳細については、[Azure AD と Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) に関するページをご覧ください。

Azure Maps アカウントにおける API 使用状況のメトリックを表示する方法について確認します。
> [!div class="nextstepaction"] 
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure Active Directory (AAD) と Azure Maps を統合する方法を示すサンプルの一覧については、次を参照してください。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
