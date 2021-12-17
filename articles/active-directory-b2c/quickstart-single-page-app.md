---
title: 'クイックスタート: シングルページ アプリ (SPA) のサインインの設定'
titleSuffix: Azure AD B2C
description: このクイックスタートでは、Azure Active Directory B2C を使用してアカウント サインインを提供するサンプル シングルページ アプリケーションを実行します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: a4ab049f739ff1ec940234cb6559c88547a47b20
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036685"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>クイックスタート: Azure Active Directory B2C を使用したシングルページ アプリのサインインの設定

Azure Active Directory B2C (Azure AD B2C) は、アプリケーション、ビジネス、顧客を保護するためのクラウド ID 管理を提供します。 Azure AD B2C に対応したアプリケーションは、オープンな標準プロトコルを使用し、ソーシャル アカウントやエンタープライズ アカウントで認証を行うことができます。 

このクイック スタートでは、シングル ページ アプリケーションにソーシャル ID プロバイダーを使ってサインインし、Azure AD B2C で保護された Web API を呼び出します。

<!--[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] -->

## <a name="prerequisites"></a>前提条件

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Facebook、Google、または Microsoft のソーシャル アカウント
- GitHub からのコード サンプル: [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa):

    [zip アーカイブをダウンロードする](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip)か、リポジトリを複製できます

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>アプリケーションの実行

1. Node.js のコマンド プロンプトから次のコマンドを実行してサーバーを起動します。

    ```console
    npm install
    npm update
    npm start
    ```

    *server.js* によって起動されたサーバーが、リッスンしているポートを表示します。

    ```console
    Listening on port 6420...
    ```

1. ブラウザーでアプリケーションの URL にアクセスします。 たとえば、「 `http://localhost:6420` 」のように入力します。

    ![ブラウザーに表示されたシングルページ アプリケーションのサンプル アプリ](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>自分のアカウントを使用してサインインする

1. **[サインイン]** を選択して、ユーザー体験を開始します。
1. Azure AD B2C には、サンプルの Web アプリケーション用に "Fabrikam" という名前の架空の会社のサインイン ページが用意されています。 ソーシャル ID プロバイダーを使用してサインアップするには、使用する ID プロバイダーのボタンを選択します。

    ![ID プロバイダー ボタンが表示されたサインインまたはサインアップ ページ](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    ユーザーは、ソーシャル アカウントの資格情報を使用して認証 (サインイン) し、アプリケーションがそのソーシャル アカウントから情報を読み取ることを承認します。 アクセスを許可することにより、アプリケーションはソーシャル アカウントからプロファイル情報 (名前やお住まいの都市など) を取得できるようになります。

1. ID プロバイダーのサインイン プロセスを完了します。

## <a name="access-a-protected-api-resource"></a>保護された API リソースにアクセスする

**[Call API]\(API の呼び出し\)** を選択すると、Web API から表示名が JSON オブジェクトとして返されます。

![Web API の応答が表示されているブラウザーのサンプル アプリケーション](./media/quickstart-single-page-app/call-api-spa.png)

このシングルページ アプリケーション サンプルは、保護された Web API リソースへの要求にアクセス トークンを追加します。

<!-- ## Clean up resources

You can use your Azure AD B2C tenant if you plan to try other Azure AD B2C quickstarts or tutorials. When no longer needed, you can [delete your Azure AD B2C tenant](faq.yml#how-do-i-delete-my-azure-ad-b2c-tenant-).-->

## <a name="next-steps"></a>次のステップ

<!---In this quickstart, you used a sample single-page application to:

- Sign in with a social identity provider
- Create an Azure AD B2C user account (created automatically at sign-in)
- Call a web API protected by Azure AD B2C -->

- [Azure portal で Azure Active Directory B2C テナント](tutorial-create-tenant.md)の作成を開始します。
