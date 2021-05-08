---
title: Twitter 認証を構成する
description: App Services アプリまたは Azure Functions アプリ用の ID プロバイダーとして Twitter 認証を構成する方法について説明します。
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: 6ecce954991d9f3901c54a6f87fc803b32469862
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077977"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-twitter-login"></a>Twitter ログインを使用するように App Service アプリまたは Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、認証プロバイダーとして Twitter を使用するように Azure App Service または Azure Functions を構成する方法を示します。

この記事の手順を完了するには、電子メール アドレスと電話番号を検証済みの Twitter アカウントが必要になります。 新しい Twitter アカウントを作成するには、[twitter.com] にアクセスしてください。

## <a name="register-your-application-with-twitter"></a><a name="register"> </a>Twitter にアプリケーションを登録する

1. [Azure portal] にサインインし、お使いのアプリケーションに移動します。 **[URL]** をコピーします。 これは Twitter アプリの構成で使用します。
1. [Twitter Developers] の Web サイトに移動し、Twitter アカウント資格情報でサインインし、 **[Create an app]\(アプリの作成\)** を選択します。
1. 新しいアプリの **[App name]\(アプリ名\)** と **[Application description]\(アプリケーションの説明\)** を入力します。 **[Website URL]\(Web サイトの URL\)** フィールドにアプリケーションの **URL** を貼り付けます。 **[Callback URLs]\(コールバック URL\)** セクションで、App Service アプリの HTTPS URL を入力し、パス `/.auth/login/twitter/callback` を追加します。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/twitter/callback` 」のように入力します。
1. ページの下部にある **[Tell us how this app will be used]\(このアプリの用途の説明\)** に、100 文字以上入力し、 **[Create]\(作成\)** を選択します。 ポップアップでもう一度 **[Create]\(作成\)** をクリックします。 アプリケーションの詳細が表示されます。
1. **[Keys and Access Tokens]** タブをクリックします。

   これらの値を書き留めておきます。
   - API キー
   - API シークレット キー

   > [!IMPORTANT]
   > API シークレット キーは重要なセキュリティ資格情報です。 このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。

## <a name="add-twitter-information-to-your-application"></a><a name="secrets"> </a>アプリケーションに Twitter 情報を追加する

1. [Azure portal] にサインインし、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. [ID プロバイダー] ドロップダウンで **[Twitter]** を選択します。 前に取得した `API key` 値と `API secret key` 値を貼り付けます。

    シークレットは、`TWITTER_PROVIDER_AUTHENTICATION_SECRET` という名前のスロット固定の[アプリケーション設定](./configure-common.md#configure-app-settings)として保存されます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。

1. これがアプリケーション用に構成された最初の ID プロバイダーである場合は、「**App Service 認証設定**」のセクションも表示されます。 それ以外の場合は、次の手順に進むことができます。
    
    これらのオプションは、アプリケーションが認証されていない要求にどのように応答するかを決定し、既定の選択によって、この新しいプロバイダーにログインするためのすべての要求がリダイレクトされます。 **[認証設定]** の横にある **[編集]** を選択して、この動作を今すぐカスタマイズするか、後でメインの **[認証]** 画面からこれらの設定を調整することができます。 これらのオプションの詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」を参照してください。

1. **[追加]** をクリックします。

これで、アプリケーションで認証に Twitter を使用する準備ができました。 **[認証]** 画面にプロバイダーが一覧表示されます。 そこから、このプロバイダーの構成を編集または削除できます。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->

[Twitter Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Azure Portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
