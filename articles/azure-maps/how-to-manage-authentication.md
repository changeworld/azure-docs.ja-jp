---
title: 認証の管理 | Microsoft Azure Maps
description: Azure portal を使用して、Microsoft Azure Maps での認証を管理します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: ef88f9794fcba8c702ee793044d5482912ec3d89
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210006"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) 認証および共有キー認証をサポートするためにクライアント ID とキーが作成されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 [共有キー認証を使用して](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Azure Maps を呼び出す場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 2 次キーは、キー変更のローリングなどのシナリオで使用できます。 詳細については、「[Azure Maps による認証](https://aka.ms/amauth)」をご覧ください。

認証の詳細は、Azure portal で確認できます。 Azure portal の自分のアカウントで、 **[設定]** メニューの **[認証]** を選択します。

![認証の詳細](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Azure AD アプリを登録して構成する

1. Azure portal の Azure サービスの一覧で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。  

    ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

1. アプリを既に登録している場合は、次の手順に進みます。 アプリを登録していない場合は、 **[名前]** を入力し、 **[Support account type] (サポートされているアカウントの種類)** を選択して、 **[登録]** を選択します。  

    ![アプリの登録の詳細](./media/how-to-manage-authentication/app-create.png)

1. 委任された API アクセス許可を Azure Maps に割り当てるには、アプリケーションに移動します。 次に、 **[アプリの登録]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[所属する組織で使用している API]** で、「**Azure Maps**」を検索して選択します。

    ![アプリの API アクセス許可の追加](./media/how-to-manage-authentication/app-permissions.png)

1. **[Access Azure Maps] (Azure Maps へのアクセス)** の横にあるチェック ボックスをオンにしてから、 **[アクセス許可の追加]** を選択します。

    ![アプリの API アクセス許可の選択](./media/how-to-manage-authentication/select-app-permissions.png)

1. 使用する認証方法に応じて、次のいずれかの手順を実行します。 

    * お使いのアプリケーション上で Azure Maps Web SDK によるユーザートークン認証を使用する場合は、`oauth2AllowImplicitFlow` を有効にします。 これを有効にするには、アプリ登録の **[マニフェスト]** セクションで `oauth2AllowImplicitFlow` を [true] に設定します。 
    
       ![アプリ マニフェスト](./media/how-to-manage-authentication/app-manifest.png)

    * お使いのアプリケーション上でサーバー認証またはアプリケーション認証を使用する場合は、アプリの登録ページで、 **[証明書とシークレット]** に移動します。 次に、公開キー証明書をアップロードするか、 **[新しいクライアント シークレット]** を選択してパスワードを作成します。 
    
       ![クライアント シークレットの作成](./media/how-to-manage-authentication/app-keys.png)

        パスワードを作成する場合は、 **[追加]** を選択した後に、パスワードをコピーして安全に保管します。 Azure AD からトークンを取得するには、このパスワードを使用します。

       ![クライアント シークレットの追加](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>ロールベースのアクセス制御を Azure Maps に付与する

Azure AD テナントに Azure Maps アカウントを関連付けると、アクセス制御を許可できます。 *ロールベースのアクセス制御* (RBAC) を付与するには、1 つまたは複数の Azure Maps アクセス制御ロールにユーザー、グループ、またはアプリケーションを割り当てます。 

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    ![RBAC の付与](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. **[ロールの割り当て]** タブの **[ロール]** で、 **[Azure Maps データ閲覧者 (プレビュー)]** を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 ユーザーまたはアプリケーションを選択します。 次に、 **[保存]** を選択します。

    ![ロールの割り当ての追加](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>使用可能な Azure Maps RBAC ロールを表示する

Azure Maps に使用できる RBAC ロールを表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。 これらの Azure Maps ロールが、アクセス権を付与できるロールです。

![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Azure Maps RBAC を表示する

RBAC は詳細なアクセス制御を提供します。

Azure Maps の RBAC が付与されているユーザーやアプリを表示するには、 **[アクセス制御 (IAM)]** に移動します。 そこで、 **[ロールの割り当て]** を選択してから、「**Azure Maps**」でフィルター処理します。

![RBAC が付与されているユーザーやアプリの表示](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

アプリを登録して Azure Maps と関連付けると、アクセス トークンを要求することができます。

お使いのアプリケーション上で Azure Maps Web SDK によるユーザートークン認証を使用する場合は、Azure Maps クライアント ID と Azure AD アプリ ID を使って HTML ページを構成する必要があります。

お使いのアプリケーション上でサーバー認証またはアプリケーション認証を使用する場合は、Azure AD トークン エンドポイント `https://login.microsoftonline.com` からトークンを要求します。 要求では、次の詳細を使用します。 

* Azure リソース ID `https://atlas.microsoft.com/`
* Azure Maps クライアント ID
* Azure AD アプリ ID
* Azure AD アプリの登録パスワードまたは証明書

| Azure 環境   | Azure AD トークン エンドポイント | Azure リソース ID |
| --------------------|-------------------------|-------------------|
| Azure パブリック クラウド        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government クラウド   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)」をご覧ください。


## <a name="next-steps"></a>次のステップ

詳細については、[Azure AD と Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) に関する記事をご覧ください。

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"] 
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
