---
title: 認証
description: 認証のしくみについて説明します
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: how-to
ms.openlocfilehash: 01b2dbaa8ed318f08fd68660078ae6fb9923221d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112969769"
---
# <a name="configure-authentication"></a>認証を構成する。

Azure Remote Rendering には、[Azure Spatial Anchors (ASA)](../../spatial-anchors/concepts/authentication.md?tabs=csharp) と同じ認証メカニズムが使用されます。 REST API を正常に呼び出すには、クライアントで以下の内 *1 つ* を設定する必要があります。

* **AccountKey**: Azure portal の Remote Rendering アカウントの [キー] タブで取得できます。 アカウント キーは、開発、またはプロトタイプ作成でのみ推薦されます。
    ![アカウント ID](./media/azure-account-primary-key.png)

* **AccountDomain**: Azure portal の Remote Rendering アカウントの [概要] タブで取得できます。
    ![アカウントのドメイン](./media/azure-account-domain.png)

* **AuthenticationToken**: Azure AD トークンであり、[MSAL ライブラリ](../../active-directory/develop/msal-overview.md)を使用して取得できます。 ユーザー資格情報を受け入れ、これらの資格情報を使用してアクセス トークンを取得するには、複数の異なるフローが使用できます。

* **MRAccessToken**: MR トークンであり、Azure Mixed Reality Security Token Service (STS) から取得できます。 次のような REST 呼び出しを使用して、`https://sts.<accountDomain>` エンドポイントから取得されます。

    ```rest
    GET https://sts.southcentralus.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
    Host: sts.southcentralus.mixedreality.azure.com
    Connection: Keep-Alive

    HTTP/1.1 200 OK
    Date: Tue, 24 Mar 2020 09:09:00 GMT
    Content-Type: application/json; charset=utf-8
    Content-Length: 1153
    Accept: application/json
    MS-CV: 05JLqWeKFkWpbdY944yl7A.0
    {"AccessToken":"eyJhbGciOiJSUzI1<truncated>uLkO2FvA"}
    ```

    ここで Authorization ヘッダーは、`Bearer <Azure_AD_token>` または `Bearer <accoundId>:<accountKey>` のように書式設定されます。 セキュリティの観点では前者が推薦されます。 この REST 呼び出しで返されたトークンが、MR アクセス トークンです。

## <a name="authentication-for-deployed-applications"></a>デプロイされたアプリケーションの認証

開発時には、短時間でプロトタイプを作成するためにアカウント キーを使用することをお勧めします。 組み込みのアカウント キーを使用してアプリケーションを運用環境に出荷しないことをお勧めします。 推奨される方法は、ユーザーベースまたはサービスベースの Azure AD 認証方法を使用することです。

### <a name="azure-ad-user-authentication"></a>Azure AD ユーザー認証

Azure AD 認証については、[Azure Spatial Anchors のドキュメント](../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication)で説明しています。

手順に従って、Azure portal で、Azure Active Directory ユーザー認証の設定を行います。

1. Azure Active Directory にアプリケーションを登録します。 登録に際して、アプリケーションをマルチテナントにするかどうかを決める必要があります。 また、アプリケーションに対し許可されたリダイレクト URL を、[Authentication]\(認証\) ブレードで入力する必要があります。
:::image type="content" source="./media/azure-active-directory-app-setup.png" alt-text="認証のセットアップ":::

1. [API permissions]\(API 使用権限\) タブの **[mixedreality]** で、**mixedreality.signin** スコープに対する **委任権限** を請求します。
:::image type="content" source="./media/azure-active-directory-app-api-permissions.png" alt-text="API 使用権限":::

1. [Security]\(セキュリティ\) -> [Permissions]\(権限\) タブで管理者の同意を与えます。:::image type="content" source="./media/azure-active-directory-grant-admin-consent.png" alt-text="管理者の同意":::

1. 次に Azure Remote Rendering Resource に移動します。 [Access Control]\(アクセス制御\) パネルで、アプリケーションとユーザーを管理するための必要な[ロール](#azure-role-based-access-control)を割り当てます。これで、アプリケーションとユーザーから委任された、Azure Remote Rendering リソースに対するアクセス権を行使できるようになります。
:::image type="content" source="./media/azure-remote-rendering-add-role-assignment.png" alt-text="Add permissions":::
:::image type="content" source="./media/azure-remote-rendering-role-assignments.png" alt-text="ロールの割り当て":::

アプリケーションのコードで Azure AD ユーザー認証を実装する方法は、[「チュートリアル: Azure Remote Rendering とモデル ストレージのセキュリティ保護」のAzure Active Directory 認証に関するセクション](../tutorials/unity/security/security.md#azure-active-directory-azure-ad-authentication)をご覧ください。

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

サービスに付与するアクセス レベルを制御するには、ロールベースのアクセス権を付与するときに次のロールを使用します。

* **Remote Rendering 管理者**:ユーザーに、Azure Remote Rendering の変換、セッション管理、レンダリング、診断の機能を提供します。
* **Remote Rendering クライアント**:ユーザーに、Azure Remote Rendering のセッション管理、レンダリング、診断の機能を提供します。

## <a name="next-steps"></a>次のステップ

* [アカウントの作成](create-an-account.md)
* [認証に Azure フロントエンド API を使用する](frontend-apis.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
