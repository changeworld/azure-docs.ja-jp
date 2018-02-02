---
title: "Azure Stack 上の App Services のデプロイ ソースを構成する | Microsoft Docs"
description: "サービス管理者が Azure Stack 上の App Service のデプロイ ソース (Git、GitHub、BitBucket、DropBox、および OneDrive) を構成する方法"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: anwestg
ms.openlocfilehash: 3738ca8ce8dfe0e5375779e9273a3d10baf8861a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2018
---
# <a name="configure-deployment-sources"></a>デプロイ ソースを構成する
*適用先: Azure Stack 統合システムと Azure Stack Development Kit*


Azure Stack 上の App Service は、複数のソース管理プロバイダーからのオンデマンド デプロイをサポートします。 この機能を使用して、アプリケーション開発者はソース管理リポジトリから直接デプロイできます。 App Service をリポジトリに接続するように構成するには、まずクラウドの管理者が App Service on Azure Stack とソース管理プロバイダーの統合を構成しておく必要があります。  

ローカル Git の他に、次のソース管理プロバイダーがサポートされます。

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>App Service 管理でデプロイ ソースを表示する

1. サービス管理者として、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にログインします。
2. **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。![App Service リソース プロバイダー管理][1]
3. **[Source control configuration (ソース管理構成)]** をクリックします。  構成されているすべてのデプロイ ソースの一覧が表示されます。
    ![App Service リソース プロバイダー管理のソース管理構成][2]

## <a name="configure-github"></a>GitHub を構成する

このタスクを完了するには、GitHub アカウントが必要です。 個人のアカウントではなく、組織のアカウントを使用できます。

1. GitHub にログインして https://www.github.com/settings/developers に移動し、**[Register a new application]\(新しいアプリケーションの登録\)** をクリックします。
    ![GitHub - 新しいアプリケーションの登録][3]
2. **[アプリケーション名]** を入力します (例: App Service on Azure Stack)。
3. **[Homepage URL (ホームページの URL)]**を入力します。 ホームページの URL は、Azure Stack ポータルのアドレスである必要があります。 たとえば、https://portal.local.azurestack.external です。
4. **[アプリケーションの説明]**.を入力します。
5. **[Authorization callback URL (認証コールバック エンドポイント URL)]** を入力します。  Azure Stack の既定のデプロイでは、URL は https://portal.local.azurestack.external/tokenauthorize です。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。
    ![GitHub - 新しいアプリケーションの登録と値の入力][4]
6. **[Register application (アプリケーションを登録する)]**をクリックします。  アプリケーションの **[Client ID (クライアント ID)]** と **[Client Secret (クライアント シークレット)]** を示すページが表示されます。
    ![GitHub - 完了したアプリケーション登録][5]
7.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。
8.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
9. **[Source control configuration (ソース管理構成)]** をクリックします。
10. **[Client ID (クライアント ID)]** と**[Client Secret (クライアント シークレット)]** をコピーして、GitHub 用の対応する入力ボックスに貼り付けます。
11. **[Save]** をクリックします。

## <a name="configure-bitbucket"></a>BitBucket を構成する

このタスクを完了するには、BitBucket アカウントが必要です。 個人のアカウントではなく、組織のアカウントを使用できます。

1. BitBucket にログインし、アカウントの **[統合]** に移動します。
    ![BitBucket ダッシュボード - 統合][7]
2. [アクセス管理] で **[OAuth]** をクリックし、**[Add consumer]\(顧客の追加\)** をクリックします。
    ![BitBucket への OAuth 顧客の追加][8]
3. 顧客の **[名前]** を入力します (例: App Service on Azure Stack)。
4. アプリケーションの **[説明]** を入力します。
5. **[Callback URL (コールバック URL)]** を入力します。  Azure Stack の既定のデプロイでは、コールバック URL は https://portal.local.azurestack.external/TokenAuthorize です。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。  BitBucket の統合を成功させるには、URL の大文字と小文字をここに記載されているとおりにする必要があります。
6. **[URL]** を入力します。この URL は、Azure Stack ポータルの URL にする必要があります (例: https://portal.local.azurestack.external )。
7. 必要な **[Permissions]\(権限\)** を次のように選択します。
    - **[Repositories]\(リポジトリ\)**: *[Read]\(読み取り\)*
    - **[Webhooks]**: *[Read and write]\(読み取りと書き込み\)*
8. **[Save]** をクリックします。  新しいアプリケーションが **[OAuth consumers (OAuth コンシューマー)]**の下に **[Key (キー)]** と **[Secret (シークレット)]** と共に表示されます。
    ![BitBucket アプリケーション リスト][9]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **[Key (キー)]**をコピーして BitBucket 用の **[クライアント ID]** 入力ボックスに、**[Secret (シークレット)]** をコピーして BitBucket 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[Save]** をクリックします。


## <a name="configure-onedrive"></a>OneDrive を構成する

このタスクを完了するには、OneDrive アカウントにリンクされている Microsoft アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

> [!NOTE]
> 現時点では、OneDrive for Business アカウントはサポートされていません。

1. https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm に移動し、Microsoft アカウントを使用してログインします。
2. **[マイ アプリケーション]** で **[アプリを追加]** をクリックします。
![OneDrive アプリケーション][10]
3. 新しく登録するアプリケーションの **[名前]** を入力します。「**App Service on Azure Stack**」と入力し、**[アプリケーションの作成]** をクリックします。
4. 次の画面に、新しいアプリケーションのプロパティが表示されます。 **[アプリケーション ID]** をメモします。![OneDrive アプリケーションのプロパティ][11]
5. **[アプリケーション シークレット]** で **[新しいパスワードを生成]** をクリックします。 **新しく生成されたパスワード**をメモします。 これがお客様のアプリケーション シークレットになります。**[OK]** をクリックした後、この段階でもう一度取得することはできません。
6. **[プラットフォーム]** で **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
7. **[リダイレクト URI]** を入力します。  Azure Stack の既定のデプロイでは、リダイレクト URI は https://portal.local.azurestack.external/tokenauthorize の形式になっています。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。![OneDrive アプリケーション - Web プラットフォームの追加][12]
8. **[Microsoft Graph のアクセス許可]** - **[委任されたアクセス許可]** を設定します。
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive アプリケーション - Graph のアクセス許可][13]
9. **[Save]** をクリックします。
10.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。
11.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
12. **[Source control configuration (ソース管理構成)]** をクリックします。
13. **[アプリケーション ID]** をコピーして OneDrive 用の **[クライアント ID]** 入力ボックスに、**[パスワード]** をコピーして OneDrive 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
14. **[Save]** をクリックします。

## <a name="configure-dropbox"></a>DropBox を構成する

> [!NOTE]
> このタスクを完了するには、DropBox アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

1. https://www.dropbox.com/developers/apps に移動し、Dropbox アカウントを使用してログインします。
2. **[Create app]**をクリックします。

    ![Dropbox アプリケーション][14]

3. **[Dropbox API]** を選択します。
4. アクセス レベルを **[App folder]\(アプリ フォルダー\)** に設定します。
5. アプリケーションの **[名前]** を入力します。
![Dropbox アプリケーションの登録][15]
6. **[Create app (アプリの作成)]** をクリックします。  **[App key (アプリ キー)]** と **[App secret (アプリ シークレット)]** を含むアプリの設定を示すページが表示されます。
7. **[App folder name]\(アプリ フォルダー名\)** が **[App Service on Azure Stack]** に設定されていることを確認します。
8. **[OAuth 2 Redirect URI (OAuth 2 リダイレクト URI)]** を設定し、**[Add (追加)]** をクリックします。  Azure Stack の既定のデプロイでは、リダイレクト URI は https://portal.local.azurestack.external/tokenAuthorize です。別のドメインで実行している場合は、azurestack.local をそのドメインに置き換えてください。
![Dropbox アプリケーションの構成][16]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてログインします。
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **[Application Key (アプリケーション キー)]** をコピーして DropBox 用の **[クライアント ID]** 入力ボックスに、**[App secret (アプリ シークレット)]** をコピーして DropBox 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[Save]** をクリックします。


<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png

## <a name="next-steps"></a>次の手順

[継続的配置](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment)、[ローカルの Git デプロイ](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-local-git)、[クラウド フォルダーの同期](https://docs.microsoft.com/azure/app-service-web/app-service-deploy-content-sync)などでデプロイ ソースを使用できるようになりました。
