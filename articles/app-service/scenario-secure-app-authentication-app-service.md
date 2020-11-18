---
title: チュートリアル - Azure App Service の Web アプリに認証を追加する | Azure
description: このチュートリアルでは、Azure App Service で実行されている Web アプリの認証と承認を有効にする方法について学習します。  Web アプリへのアクセスを組織内のユーザーに制限します。
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: a1b2809371a01b1c6fd822e5c4aaa197d0de3c1b
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428295"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>チュートリアル: Azure App Service で実行されている Web アプリに認証を追加する

Azure App Service で実行されている Web アプリの認証を有効にし、アクセスを組織内のユーザーに制限する方法について学習します。

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="ユーザーのサインイン" border="false":::

Azure App Service は組み込みの認証と承認のサポートを提供するので、Web アプリに最小限のコードを記述するだけで、またはコードをまったく記述せずに、ユーザーのサインインとデータへのアクセスを可能にできます。  App Service 認証および承認モジュールを使用する必要はありませんが、対象のアプリの認証と承認を簡略化するのに役立ちます。 この記事では、Azure Active Directory を ID プロバイダーとして使用して、App Service 認証および承認モジュールで Web アプリを保護する方法について説明します。

認証および承認モジュールは、Azure portal とアプリの設定を通じて有効化および構成されます。 SDK、特定の言語、またはアプリケーションのコードの変更は必要ありません。 次のようなさまざまな ID プロバイダーがサポートされています: Azure AD、Microsoft アカウント、Facebook、Google、Twitter。 認証および承認モジュールが有効になっている場合、すべての受信 HTTP 要求は、アプリ コードによって処理される前にこのモジュールを通過します。  詳細については、[Azure App Service での認証と承認](overview-authentication-authorization.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * Web アプリの認証を構成する
> * Web アプリへのアクセスを組織内のユーザーに制限する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>App Service で Web アプリを作成して公開する

このチュートリアルでは、Azure App Service にデプロイされた Web アプリが必要です。  既存の Web アプリを使用することも、[ASP.NET Core のクイックスタート](quickstart-dotnetcore.md)に従って新しい Web アプリを作成して App Service に公開することもできます。

既存の Web アプリを使用するか新しい Web アプリを作成するかにかかわらず、Web アプリ名と Web アプリのデプロイ先のリソース グループの名前をメモしてください。 これらの名前は、このチュートリアル全体を通して必要になります。 このチュートリアル全体を通して、プロシージャとスクリーンショットのサンプル名に *SecureWebApp* が含まれています。

## <a name="configure-authentication-and-authorization"></a>認証と承認を構成する

これで、App Service で実行されている Web アプリを準備できました。  次に、Web アプリの認証と承認を有効にします。 ID プロバイダーとして Azure Active Directory を使用します。 詳細については、[App Services アプリケーション用の Azure Active Directory 認証の構成](configure-authentication-provider-aad.md)に関するページを参照してください。

[[Azure portal]](https://portal.azure.com) メニューで **[リソース グループ]** を選択するか、または任意のページから *[リソース グループ]* を検索して選択します。

**[リソース グループ]** でリソース グループを検索して選択します。 **[概要]** で、アプリの管理ページを選択します。

:::image type="content" alt-text="App Service を選択する" source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

アプリの左側のメニューで **[認証/承認]** を選択し、 **[オン]** を選択して App Service 認証を有効にします。

**[要求が認証されない場合に実行するアクション]** で、 **[Azure Active Directory でのログイン]** を選択します。

**[認証プロバイダー]** の下の **[Azure Active Directory]** をクリックします。 **[簡易]** を選択し、既定の設定のままで新しい AD アプリを作成して、 **[OK]** を選択します。

:::image type="content" alt-text="簡易認証" source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

**[認証/承認]** ページで、 **[保存]** を選択します。

`Successfully saved the Auth Settings for <app-name> App` というメッセージを含む通知が表示されたら、ポータル ページを更新します。

これで、App Service の認証と承認によってアプリが保護されるようになりました。

## <a name="verify-limited-access-to-the-web-app"></a>Web アプリへの制限付きアクセスを確認する

App Service の認証および承認モジュールを有効にしたときに、Azure AD テナントにアプリの登録が作成されました。  アプリの登録には、Web アプリと同じ表示名が付けられています。 設定を確認するには、ポータル メニューから **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。  作成されたアプリの登録を選択します。  概要で、 **[Supported account types]\(サポートされているアカウントの種類\)** が **[所属する組織のみ]** に設定されていることを確認します。

:::image type="content" alt-text="アクセスを確認する" source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

対象のアプリへのアクセスが組織内のユーザーに制限されていることを確認するには、シークレット モードまたはプライベート モードでブラウザーを起動し、`https://<app-name>.azurewebsites.net` に移動します。  セキュリティで保護されたサインイン ページに移動します。これで、認証されていないユーザーにサイトへのアクセスが許可されていないことを確認できます。  サイトにアクセスするために、組織内のユーザーとしてサインインします。  また、新しいブラウザーを起動し、個人アカウントを使用してサインインして、組織外のユーザーにアクセス権がないことを確認することもできます。  

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了し、Web アプリまたは関連するリソースが不要になった場合は、[作成したリソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
>
> * Web アプリの認証を構成する
> * Web アプリへのアクセスを組織内のユーザーに制限する

> [!div class="nextstepaction"]
> [アプリ サービスからストレージにアクセスする](scenario-secure-app-access-storage.md)
