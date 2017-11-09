---
title: "Azure AD B2C シングル ページ アプリケーションの試用 | Microsoft Docs"
description: "テスト用の Azure AD B2C 環境を使用して、サインイン、サインアップ、プロファイルの編集、パスワードのリセットを体験してみます"
services: active-directory-b2c
documentationcenter: 
author: saraford
manager: krassk
editor: PatAltimore
ms.assetid: 5a8a46af-28bb-4b70-a7f0-01a5240d0255
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 10/31/2017
ms.author: saraford
ms.openlocfilehash: 22da1ae317ba685d32f93d3331cf794b568891ec
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2017
---
# <a name="test-drive-a-single-page-application-configured-with-azure-ad-b2c"></a>Azure AD B2C で構成されているシングル ページ アプリケーションの試用

## <a name="about-this-sample"></a>このサンプルについて

Azure Active Directory B2C は、アプリケーション、ビジネス、顧客を保護するためのクラウド ID 管理を提供します。  このクイック スタートでは、サンプルのシングル ページ アプリケーションを使用して、以下の操作のデモンストレーションを行います。

* **サインアップまたはサインイン** ポリシーを使用した、ソーシャル ID プロバイダー アカウントまたはメール アドレスを使用したローカル アカウントの作成またはサインイン。 
* **API の呼び出し**による、Azure AD B2C のセキュリティで保護されたリソースからの表示名の取得。

## <a name="prerequisites"></a>前提条件

* 次のワークロードを使って、[Visual Studio 2017](https://www.visualstudio.com/downloads/) をインストールします。
    - **ASP.NET と Web 開発**

* [Node.js](https://nodejs.org/en/download/)

* Facebook、Google、Microsoft、または Twitter のソーシャル アカウント。 ソーシャル アカウントを持っていない場合は、有効なメール アドレスが必要です。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="download-the-sample"></a>サンプルのダウンロード

GitHub から[サンプル アプリケーションをダウンロードまたは複製](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp)します。

## <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

このサンプルを Node.js コマンド プロンプトから実行するには、次のようにします。 

```
cd active-directory-b2c-javascript-msal-singlepageapp
npm install && npm update
node server.js
```

コンソール ウィンドウには、コンピューターで実行されている Web アプリケーションのポート番号が表示されます。

```
Listening on port 6420...
```

Web ブラウザーで `http://localhost:6420` を開き、Web アプリケーションにアクセスします。


![ブラウザーでのサンプル アプリ](media/active-directory-b2c-quickstarts-spa/sample-app-spa.png)

## <a name="create-an-account"></a>アカウントの作成

**[Login]\(ログイン\)** ボタンをクリックして、Azure AD B2C の**サインアップまたはサインイン** ワークフローを開始します。 アカウントの作成時に、既存のソーシャル ID プロバイダー アカウントまたは電子メール アカウントを使用することができます。

### <a name="sign-up-using-a-social-identity-provider"></a>ソーシャル ID プロバイダーを使用してサインアップする

ソーシャル ID プロバイダーを使用してサインアップするには、使用する ID プロバイダーのボタンをクリックします。 メール アドレスを使用する場合は、「[メール アドレスを使用してサインアップする](#sign-up-using-an-email-address)」セクションに進んでください。

![サインインまたはサインアップ用のプロバイダー](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-spa.png)

ソーシャル アカウントの資格情報を使用して認証 (サインイン) し、アプリケーションがソーシャル アカウントから情報を読み取ることを承認する必要があります。 アクセスを許可することにより、アプリケーションはソーシャル アカウントからプロファイル情報 (名前やお住まいの都市など) を取得できるようになります。 

![ソーシャル アカウントを使用して認証と承認を行う](media/active-directory-b2c-quickstarts-spa/twitter-authenticate-authorize-spa.png)

新しいアカウントのプロファイルの詳細には、ソーシャル アカウントからの情報があらかじめ設定されています。 

![新しいアカウントのサインアップ用プロファイルの詳細](media/active-directory-b2c-quickstarts-spa/new-account-sign-up-profile-details-spa.png)

[Display Name]\(表示名\)、[Job Title]\(役職\)、[City]\(都市) フィールドを更新し、**[Continue]\(続行\)** をクリックします。  入力した値は、Azure AD B2C ユーザー アカウントのプロファイルに使用されます。

ID プロバイダーを使用する新しい Azure AD B2C ユーザー アカウントが正常に作成されました。 

次の手順: 「[リソースの呼び出し](#call-a-resource)」セクション。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

認証の提供にソーシャル アカウントを使用しない場合は、有効なメール アドレスを使用して Azure AD B2C ユーザー アカウントを作成できます。 Azure AD B2C ローカル ユーザー アカウントは、ID プロバイダーとして Azure Active Directory を使用します。 メール アドレスを使用するには、**[Don't have an account? Sign up now]\(アカウントをお持ちでない場合は今すぐサインアップ\)** リンクをクリックします。

![電子メールを使用してサインインまたはサインアップする](media/active-directory-b2c-quickstarts-spa/sign-in-or-sign-up-email-spa.png)

有効なメール アドレスを入力し、**[Send verification code]\(確認コードの送信\)** をクリックします。 Azure AD B2C から確認コードを受信するには、有効なメール アドレスが必要です。 

電子メールで受け取った確認コードを入力し、**[Verify code]\(コードの確認\)** をクリックします。

プロファイル情報を追加し、**[Create]\(作成\)** をクリックします。

![電子メールを使用した新しいアカウントでサインアップする](media/active-directory-b2c-quickstarts-spa/sign-up-new-account-profile-email-web.png)

新しい Azure AD B2C ローカル ユーザー アカウントが正常に作成されました。

## <a name="call-a-resource"></a>リソースの呼び出し

サインイン後、**[Call Web API]\(Web API の呼び出し\)** ボタンをクリックすると、Web API 呼び出しから表示名が JSON オブジェクトとして返されます。 

![Web API の応答](media/active-directory-b2c-quickstarts-spa/call-api-spa.png)

## <a name="next-steps"></a>次のステップ

次のステップでは、独自の Azure AD B2C テナントを作成し、そのテナントを使用して実行するようにサンプルを構成します。 

> [!div class="nextstepaction"]
> [Azure Portal で Azure Active Directory B2C テナントを作成する](active-directory-b2c-get-started.md)