---
title: Google 認証を構成する
description: App Services アプリまたは Azure Functions アプリ用の ID プロバイダーとして Google 認証を構成する方法について説明します。
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: f6bec32fa928e840569ed95c35a056db91ea9737
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077994"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Google ログインを使用するように App Service アプリまたは Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Google を使用するように Azure App Service または Azure Functions を構成する方法を示します。

このトピックの手順を完了するには、検証済みの電子メール アドレスを持つ Google アカウントが必要になります。 新しい Google アカウントを作成するには、[accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302) にアクセスしてください。

## <a name="register-your-application-with-google"></a><a name="register"> </a>Google にアプリケーションを登録する

1. 「[サーバー側アプリの Google サインイン](https://developers.google.com/identity/sign-in/web/server-side-flow)」で Google のドキュメントに従い、クライアント ID とクライアント シークレットを作成します。 コードを変更する必要はありません。 次の情報を使用してください。
    - **[Authorized JavaScript Origins]\(承認済みの JavaScript 生成元\)** には、`https://<app-name>.azurewebsites.net` を使用し、 *\<app-name>* にアプリの名前を指定します。
    - **[Authorized Redirect URI] (承認済みのリダイレクト URI)** には `https://<app-name>.azurewebsites.net/.auth/login/google/callback` を使用します。
1. [App ID]\(アプリ ID\) と [App Secret]\(アプリ シークレット\) の値をコピーします。

    > [!IMPORTANT]
    > アプリ シークレットは重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Google の情報をアプリケーションに追加する

1. [Azure portal] にサインインし、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. [ID プロバイダー] ドロップダウンで **[Google]** を選択します。 以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。

    シークレットは、`GOOGLE_PROVIDER_AUTHENTICATION_SECRET` という名前のスロット固定の[アプリケーション設定](./configure-common.md#configure-app-settings)として保存されます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。

1. これがアプリケーション用に構成された最初の ID プロバイダーである場合は、「**App Service 認証設定**」のセクションも表示されます。 それ以外の場合は、次の手順に進むことができます。
    
    これらのオプションは、アプリケーションが認証されていない要求にどのように応答するかを決定し、既定の選択によって、この新しいプロバイダーにログインするためのすべての要求がリダイレクトされます。 **[認証設定]** の横にある **[編集]** を選択して、この動作を今すぐカスタマイズするか、後でメインの **[認証]** 画面からこれらの設定を調整することができます。 これらのオプションの詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」を参照してください。

1. (オプション) **[次へ: スコープ]** をクリックし、アプリケーションで必要な任意のスコープを追加します。 これらは、ブラウザーベースのフローのログイン時に要求されます。
1. **[追加]** をクリックします。

これで、アプリケーションで認証に Google を使用する準備ができました。 **[認証]** 画面にプロバイダーが一覧表示されます。 そこから、このプロバイダーの構成を編集または削除できます。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure Portal]: https://portal.azure.com/

