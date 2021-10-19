---
title: 'チュートリアル: Microsoft Graph を使用して Azure Static Web Apps のロールを割り当てる'
description: サーバーレス関数を使用して、Active Directory グループ メンバーシップに基づいてカスタム ユーザー ロールを割り当てる方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 10/08/2021
ms.author: cshoe
keywords: 静的 Web アプリの承認, ユーザー ロールの割り当て, カスタム ロール
ms.openlocfilehash: bf9089f7e645dd32bbfd5f30041f76f71a04a5ec
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858831"
---
# <a name="tutorial-assign-custom-roles-with-a-function-and-microsoft-graph"></a>チュートリアル: 関数と Microsoft Graph を使用してカスタム ロールを割り当てる

この記事では、関数を使用して [Microsoft Graph](https://developer.microsoft.com/graph) に対してクエリを実行し、Active Directory グループ メンバーシップに基づいてユーザーにカスタム ロールを割り当てる方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

- 静的 Web アプリをデプロイする。
- Azure Active Directory のアプリ登録を作成する。
- Azure Active Directory を使用してカスタム認証を設定する。
- ユーザーの Active Directory グループ メンバーシップに対するクエリを実行し、カスタム ロールの一覧を返す[サーバーレス関数](authentication-authorization.md?tabs=function#role-management)を構成します。

> [!NOTE]
> このチュートリアルでは、[関数を使用してロールを割り当てる](authentication-authorization.md?tabs=function#role-management)必要があります。 関数ベースのロール管理は、現在プレビュー段階です。

## <a name="prerequisites"></a>前提条件

- **アクティブな Azure アカウント:** お持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/)できます。
- Azure Active Directory アプリケーションを作成するのに十分なアクセス許可を持っている必要があります。

## <a name="create-a-github-repository"></a>GitHub リポジトリを作成する

1. 次の場所に移動して、新しいリポジトリを作成します。
    - [https://github.com/staticwebdev/roles-function/generate](https://github.com/login?return_to=/staticwebdev/roles-function/generate)

1. リポジトリに **my-custom-roles-app** という名前をつけます。

1. **[Create repository from template]\(テンプレートからリポジトリを作成する\)** を選択します。

## <a name="deploy-the-static-web-app-to-azure"></a>静的 Web アプリを Azure にデプロイする

1. 新しいブラウザー ウィンドウで [Azure portal](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

1. 左上隅の **[リソースの作成]** を選択します。

1. 検索ボックスに「**静的 Web アプリ**」と入力します。

1. **[Static Web App]\(静的 Web アプリ\)** を選択します。

1. **［作成］** を選択します

1. 次の情報を使用して Azure 静的 Web アプリを構成します。

    | **入力** | **値** | **注** |
    | ---------- | ---------- | ---------- |
    | _サブスクリプション_ | Azure サブスクリプションを選択します。 | |
    | _リソース グループ_ | **my custom-roles-app-group** という名前で新しいものを作成します | |
    | _名前_ | **my-custom-roles-app**  | |
    | _ホスティング プラン_ | **Standard** | 認証のカスタマイズと関数を使用したロールの割り当てには、Standard プランが必要です |
    | _リージョン_ | 最も近いリージョンを選択します | |
    | _展開の詳細_ | ソースとして **GitHub** を選択します | |

1. **[GitHub アカウントでサインイン]** ボタンを選択し、GitHub で認証を行います。

1. リポジトリを作成した "_組織_" の名前を選択します。

1. _[リポジトリ]_ ドロップダウンから **my-custom-roles-app** を選択します。

1. _[ブランチ]_ ドロップダウンから **main** を選択します。

1. _[ビルドの詳細]_ セクションで、このアプリの構成の詳細を追加します。

    | **入力** | **値** | **注** |
    | ---------- | ---------- | ---------- |
    | _ビルドのプリセット_ | **カスタム** | |
    | _App location (アプリの場所)_ | **frontend** | アプリが含まれているリポジトリ内のフォルダー |
    | _API の場所_ | **api** | API が含まれているリポジトリ内のフォルダー |
    | _Output location (出力場所)_ | | このアプリにはビルド出力はありません |

1. **[Review + create]\(レビュー + 作成\)** を選択します。 次に、 **[作成]** を選択して静的 Web アプリを作成し、最初のデプロイを開始します。

1. **[リソースに移動]** を選択して、新しい静的 Web アプリを開きます。

1. [概要] セクションで、アプリケーションの **URL** を見つけます。 この値をテキスト エディターにコピーします。Active Directory 認証を設定してアプリをテストするために、この URL が必要になります。

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

1. Azure portal で *[Azure Active Directory]* を検索し、そこに移動します。

1. メニュー バーで、 **[アプリの登録]** を選択します。

1. **[+ 新規登録]** を選択して、 *[アプリケーションの登録]* ページを開きます。

1. アプリケーションの名前を入力します。 たとえば、**MyStaticWebApp** など。

1. *[サポートされているアカウントの種類]* で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。

1. *[リダイレクト URI]* には **[Web]** を選択し、静的 Web アプリの Azure Active Directory ログイン[認証コールバック](authentication-custom.md#authentication-callbacks)を入力します。 たとえば、「 `<YOUR_SITE_URL>/.auth/login/aad/callback` 」のように入力します。

    `<YOUR_SITE_URL>` は、静的 Web アプリの URL に置き換えます。

    :::image type="content" source="media/assign-roles-microsoft-graph/create-app-registration.png" alt-text="アプリの登録を作成する":::

1. **[登録]** を選択します。

1. アプリの登録が作成されたら、 *[Essentials]\(要点\)* セクションの **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** をテキスト エディターにコピーします。 これらの値は、静的 Web アプリで Active Directory 認証を構成するために必要になります。

### <a name="enable-id-tokens"></a>ID トークンを有効にする

1. メニュー バーで *[認証]* を選択します。

1. *[暗黙的な許可およびハイブリッド フロー]* セクションで **[ID トークン (暗黙的およびハイブリッド フローに使用)]** を選択します。

    :::image type="content" source="media/assign-roles-microsoft-graph/enable-id-tokens.png" alt-text="ID トークンを有効にする":::
    
    この構成は、ユーザーを認証するために Static Web Apps で必要です。

1. **[保存]** を選択します。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

1. メニュー バーで *[証明書とシークレット]* を選択します。

1. *[クライアント シークレット]* セクションで、 **[+ 新しいクライアント シークレット]** を選択します。

1. クライアント シークレットの名前を入力します。 たとえば、**MyStaticWebApp** など。

1. *[有効期限]* フィールドは、既定の "_6 か月_" のままにします。

    > [!NOTE]
    > 有効期限が切れる前に、新しいシークレットを生成し、その値でアプリを更新して、シークレットをローテーションする必要があります。

1. **[追加]** を選択します。

1. 作成したクライアント シークレットの **値** をメモします。 この値は、静的 Web アプリで Active Directory 認証を構成するために必要になります。

    :::image type="content" source="media/assign-roles-microsoft-graph/create-client-secret.png" alt-text="クライアント シークレットの作成":::

## <a name="configure-active-directory-authentication"></a>Active Directory 認証を構成する

1. ブラウザーで、デプロイした静的 Web アプリが含まれた GitHub リポジトリを開きます。 アプリの構成ファイル (*frontend/staticwebapp.config.json*) に移動します。 これには次のセクションが含まれています。

    ```json
    "auth": {
      "rolesSource": "/api/GetRoles",
      "identityProviders": {
        "azureActiveDirectory": {
          "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
          "registration": {
            "openIdIssuer": "https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>",
            "clientIdSettingName": "AAD_CLIENT_ID",
            "clientSecretSettingName": "AAD_CLIENT_SECRET"
          },
          "login": {
            "loginParameters": [
              "resource=https://graph.microsoft.com"
            ]
          }
        }
      }
    },
    ```

    > [!NOTE]
    > Microsoft Graph のアクセス トークンを取得するには、`loginParameters` フィールドが `resource=https://graph.microsoft.com` を使用して構成されている必要があります。

1. ファイルを更新するために、 **[編集]** ボタンを選択します。

1. `<YOUR_AAD_TENANT_ID>` を Azure Active Directory のディレクトリ (テナント) ID に置き換えて、`https://login.microsoftonline.com/<YOUR_AAD_TENANT_ID>` の *openIdIssuer* 値を更新します。

1. **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択し、 **[Commit changes]\(変更をコミットする\)** を選択します。

1. GitHub Actions により、静的 Web アプリを更新するトリガーが実行されます。

1. Azure portal で静的 Web アプリのリソースに移動します。

1. メニュー バーで **[構成]** を選択します。

1. *[アプリケーションの設定]* セクションで、次の設定を追加します。

    | 名前 | 値 |
    |------|-------|
    | `AAD_CLIENT_ID` | *Azure Active Directory アプリケーション (クライアント) ID* |
    | `AAD_CLIENT_SECRET` | *Azure Active Directory アプリケーションのクライアント シークレット値* |

1. **[保存]** を選択します。

## <a name="verify-custom-roles"></a>カスタム ロールを検証する

サンプル アプリケーションには、ユーザーが定義済みグループに含まれているかどうかを判断するために Microsoft Graph に対してクエリを実行するサーバーレス関数 (*api/GetRoles/index.js*) が含まれています。 ユーザーのグループ メンバーシップに基づき、関数によってカスタム ロールがユーザーに割り当てられます。 アプリケーションは、これらのカスタム ロールに基づいて特定のルートを制限するように構成されます。

1. GitHub リポジトリで、*api/GetRoles/index.js* にある *GetRoles* 関数に移動します。 上部近くに、カスタム ユーザー ロールを Azure Active Directory グループにマップする `roleGroupMappings` オブジェクトがあります。

1. [編集] ボタンをクリックします。

1. Azure Active Directory テナントのグループ ID を使用してオブジェクトを更新します。

    たとえば、`6b0b2fff-53e9-4cff-914f-dd97a13bfbd6` と `b6059db5-9cef-4b27-9434-bb793aa31805` の ID のグループがある場合は、次のようにオブジェクトを更新します。

    ```js
    const roleGroupMappings = {
      'admin': '6b0b2fff-53e9-4cff-914f-dd97a13bfbd6',
      'reader': 'b6059db5-9cef-4b27-9434-bb793aa31805'
    };
    ```

    ユーザーが Azure Active Directory で正常に認証されるたびに *Getroles* 関数が呼び出されます。 この関数では、ユーザーのアクセス トークンを使用して、Microsoft Graph から Active Directory グループ メンバーシップに対するクエリが実行されます。 ユーザーが `roleGroupMappings` オブジェクトに定義されている任意のグループのメンバーである場合は、この関数によって、対応するカスタム ロールが返されます。
    
    上の例では、ユーザーが `b6059db5-9cef-4b27-9434-bb793aa31805` の ID を持つ Active Directory グループのメンバーである場合は、`reader` ロールが付与されます。

1. **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択し、 **[Commit changes]\(変更をコミットする\)** を選択します。

1. GitHub Actions により、静的 Web アプリを更新するトリガーが実行されます。

1. デプロイが完了したら、アプリの URL に移動して変更を確認できます。

1. Azure Active Directory を使用して、静的 Web アプリにログインします。

1. ログインすると、ID の Active Directory グループ メンバーシップに基づいて割り当てられているロール一覧がサンプル アプリに表示されます。 これらのロールに応じて、アプリ内の一部のルートへのアクセスが許可または禁止されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。

1. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。

1. このチュートリアルで使用したリソース グループ名を選択します。

1. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認証と権限承認](./authentication-authorization.md)
