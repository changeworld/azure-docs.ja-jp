---
title: Facebook 認証を構成する
description: App Service または Azure Functions アプリの ID プロバイダーとして Facebook 認証を構成する方法について説明します。
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 03/29/2021
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: bc639eaea76b3309d6ed047e73c726040da19639
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078011"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Facebook ログインを使用するように App Service または Azure Functions アプリを構成する

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

この記事では、認証プロバイダーとして Facebook を使用するように Azure App Service または Azure Functions を構成する方法について説明します。

この記事の手順を完了するには、検証済みの電子メール アドレスを持つ Facebook アカウントと携帯電話番号が必要になります。 新しい Facebook アカウントを作成するには、 [facebook.com]にアクセスしてください。

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Facebook にアプリケーションを登録する

1. [Facebook Developers] の Web サイトに移動し、Facebook アカウントの資格情報でサインインします。

   Facebook for Developers アカウントを持っていない場合は、 **[スタートガイド]** を選択して登録の手順に従います。
1. **[マイアプリ]**  >  **[新しいアプリを追加]** を選択します。
1. **[表示名]** フィールド:
   1. アプリの一意の名前を入力します。
   1. **[連絡先メールアドレス]** を指定します。
   1. **[Create App ID]\(アプリ ID の作成\)** を選択します。
   1. セキュリティ チェックを完了します。

   新しい Facebook アプリ用の開発者ダッシュボードが開きます。
1. **[ダッシュボード]**  >  **[Facebook ログイン]**  >  **[設定]**  >  **[ウェブ]** を選択します。
1. 左側のナビゲーションで、 **[Facebook ログイン]** の下にある **[設定]** を選択します。
1. **[有効な OAuth リダイレクト URI]** フィールドに「`https://<app-name>.azurewebsites.net/.auth/login/facebook/callback`」を入力します。 `<app-name>` を、ご利用の Azure App Service アプリの名前に置き換えることを忘れないでください。
1. **[変更の保存]** を選択します。
1. 左側のウィンドウで、 **[設定]**  >  **[Basic]\(ベーシック\)** を選択します。 
1. **[app secret]** フィールドで、 **[表示]** を選択します。 **[アプリ ID]** と **[App Secret]** の値をコピーします。 後で App Service アプリを Azure 内で構成するときにこれらの値を使用します。

   > [!IMPORTANT]
   > アプリケーション シークレットは、重要なセキュリティ資格情報です。 このシークレットを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
   >

1. アプリケーションの登録に使用した Facebook アカウントがアプリケーションの管理者になります。 この時点では、管理者のみがこのアプリケーションにサインインできます。

   他の Facebook アカウントを認証するには、 **[アプリのレビュー]** を選択し、 **[\<your-app-name> をパブリックにする]** を有効にして、Facebook 認証を使用することで一般公衆がアクセスできるようにします。

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Facebook の情報をアプリケーションに追加する

1. [Azure portal] にサインインし、アプリに移動します。
1. 左側のメニューで **[認証]** を選択します。 **[ID プロバイダーの追加]** をクリックします。
1. [ID プロバイダー] ドロップダウンで **[Facebook]** を選択します。 以前に入手したアプリ ID とアプリ シークレットの値を貼り付けます。

    シークレットは、`FACEBOOK_PROVIDER_AUTHENTICATION_SECRET` という名前のスロット固定の[アプリケーション設定](./configure-common.md#configure-app-settings)として保存されます。 Azure Key Vault でシークレットを管理する場合は、[Key Vault 参照](./app-service-key-vault-references.md)を使用するように、この設定を後で更新することができます。

1. これがアプリケーション用に構成された最初の ID プロバイダーである場合は、「**App Service 認証設定**」のセクションも表示されます。 それ以外の場合は、次の手順に進むことができます。
    
    これらのオプションは、アプリケーションが認証されていない要求にどのように応答するかを決定し、既定の選択によって、この新しいプロバイダーにログインするためのすべての要求がリダイレクトされます。 **[認証設定]** の横にある **[編集]** を選択して、この動作を今すぐカスタマイズするか、後でメインの **[認証]** 画面からこれらの設定を調整することができます。 これらのオプションの詳細については、「[認証フロー](overview-authentication-authorization.md#authentication-flow)」を参照してください。

1. (オプション) **[次へ: スコープ]** をクリックし、アプリケーションで必要な任意のスコープを追加します。 これらは、ブラウザーベースのフローのログイン時に要求されます。
1. **[追加]** をクリックします。

これで、アプリで認証に Facebook を使用する準備ができました。 **[認証]** 画面にプロバイダーが一覧表示されます。 そこから、このプロバイダーの構成を編集または削除できます。

## <a name="next-steps"></a><a name="related-content"> </a>次の手順

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- URLs. -->
[Facebook Developers]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure Portal]: https://portal.azure.com/
