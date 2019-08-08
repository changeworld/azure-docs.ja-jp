---
title: Microsoft アカウント認証の構成 - Azure App Service
description: App Services アプリケーションに Microsoft アカウント認証を構成する方法について説明します。
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561534"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Microsoft アカウント ログインを使用するように App Service アプリケーションを構成する方法
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

このトピックでは、認証プロバイダーとして Microsoft アカウントを使用するように Azure App Services を構成する方法を示します。 

## <a name="register-microsoft-account"> </a>Microsoft アカウントにアプリを登録する
1. [Azure Portal]にログオンし、アプリケーションに移動します。 **URL** をコピーします。この URL は、後でアプリを Microsoft アカウントで構成するために使用します。
2. [ **[アプリの登録]** ](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) に移動し、要求された場合は Microsoft アカウントでサインインします。
3. **[アプリの追加]** をクリックし、アプリケーション名を入力して、 **[作成]** をクリックします。
4. **アプリケーション ID** をメモします。この情報は後で必要になります。 
5. [プラットフォーム] で **[プラットフォームの追加]** をクリックし、[Web] を選択します。
6. [リダイレクト URI] で、アプリケーションのエンドポイントを指定し、 **[保存]** をクリックします。 
   
   > [!NOTE]
   > リダイレクト URI は、アプリケーションの URL にパス */.auth/login/microsoftaccount/callback* を追加したものです。 たとえば、「 `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback` 」のように入力します。   
   > HTTPS スキームを使用していることを確認します。
   
7. [アプリケーション シークレット] で **[新しいパスワードを生成]** をクリックします。 表示される値をメモします。 ページから移動すると、このパスワードが再度表示されることはありません。

    > [!IMPORTANT]
    > パスワードは重要なセキュリティ資格情報です。 このパスワードを他のユーザーと共有したり、クライアント アプリケーション内で配信したりしないでください。
    
8. **[保存]**

## <a name="secrets"> </a>Microsoft アカウントの情報を App Service アプリケーションに追加する
1. [Azure Portal] に戻り、アプリケーションに移動して、 **[設定]**  >  **[認証/承認]** の順にクリックします。
2. [認証/承認] 機能が有効になっていない場合は、スイッチを **[オン]** に切り替えます。
3. **[Microsoft アカウント]** をクリックします。 前の手順で取得したアプリケーション ID とパスワードの値を貼り付けます。アプリケーションで必要なスコープを有効にします (省略可能)。 次に、 **[OK]** をクリックします
   
    ![][1]
   
    App Service は既定では認証を行いますが、サイトのコンテンツと API へのアクセス承認については制限を設けていません。 アプリケーション コードでユーザーを承認する必要があります。
4. (省略可能) Microsoft によって認証されたユーザーしかサイトにアクセスできないように制限するには、 **[要求が認証されない場合に実行するアクション]** を **[Microsoft アカウント]** に設定します。 この場合、要求はすべて認証される必要があり、認証されていない要求はすべて認証のために Microsoft アカウントにリダイレクトされます。
5. **[Save]** をクリックします。

これで、アプリケーションで認証に Microsoft アカウントを使用する準備ができました。

## <a name="related-content"> </a>関連コンテンツ
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure Portal]: https://portal.azure.com/
