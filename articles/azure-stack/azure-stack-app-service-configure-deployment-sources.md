---
title: Azure Stack 上の App Services のデプロイ ソースを構成する | Microsoft Docs
description: サービス管理者が Azure Stack 上の App Services のデプロイ ソース (Git、GitHub、BitBucket、DropBox、および OneDrive) を構成する方法
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: b81b9b34843974ceacf41b08a40a52418ea46d22
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547551"
---
# <a name="configure-deployment-sources"></a>デプロイ ソースを構成する

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

App Service on Azure Stack では、複数のソース管理プロバイダーからのオンデマンド デプロイがサポートされます。 この機能を使用して、アプリケーション開発者はソース管理リポジトリから直接デプロイできます。 App Service をリポジトリに接続するように構成するには、まず、クラウド オペレーターが App Service on Azure Stack とソース管理プロバイダーの統合を構成する必要があります。  

ローカル Git の他に、次のソース管理プロバイダーがサポートされます。

* GitHub
* BitBucket
* OneDrive
* DropBox

## <a name="view-deployment-sources-in-app-service-administration"></a>App Service 管理でデプロイ ソースを表示する

1. Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてサインインします。
2. **[All Services (すべてのサービス)]** を参照し、**[App Service]** を選択します。
    ![App Service リソース プロバイダー管理][1]
3. **[Source control configuration (ソース管理構成)]** をクリックします。 構成されているすべてのデプロイ ソースの一覧が表示されます。
    ![App Service リソース プロバイダー管理のソース管理構成][2]

## <a name="configure-github"></a>GitHub を構成する

このタスクを完了するには、GitHub アカウントが必要です。 個人のアカウントではなく、組織のアカウントを使用できます。

1. GitHub にサインインして https://www.github.com/settings/developers に移動し、**[Register a new application]\(新しいアプリケーションの登録\)** をクリックします。
    ![GitHub - 新しいアプリケーションの登録][3]
2. **[アプリケーション名]** を入力します (例: **App Service on Azure Stack**)。
3. **[Homepage URL (ホームページの URL)]** を入力します。 ホームページの URL は、Azure Stack ポータルのアドレスである必要があります。 たとえば、「 https://portal.local.azurestack.external 」のように入力します。
4. **[アプリケーションの説明]**.を入力します。
5. **[Authorization callback URL (認証コールバック エンドポイント URL)]** を入力します。 既定の Azure Stack デプロイでは、URL は https://portal.local.azurestack.external/TokenAuthorize という形式になります。 別のドメインで実行する場合は、local.azurestack.external のドメイン名に置き換えます。
6. **[Register application (アプリケーションを登録する)]** をクリックします。 アプリケーションの **[クライアント ID]** と **[クライアント シークレット]** を示すページが表示されます。
    ![GitHub - 完了したアプリケーション登録][5]
7.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてサインインします。
8.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin]\(App Service リソース プロバイダー管理\)** を選択します。
9. **[Source control configuration (ソース管理構成)]** をクリックします。
10. **クライアント ID** と**クライアント シークレット**をコピーして、GitHub 用の対応する入力ボックスに貼り付けます。
11. **[Save]** をクリックします。

## <a name="configure-bitbucket"></a>BitBucket を構成する

このタスクを完了するには、BitBucket アカウントが必要です。 個人のアカウントではなく、組織のアカウントを使用できます。

1. BitBucket にサインインし、アカウントの下の **[Integrations (統合)]** に移動します。
    ![BitBucket ダッシュボード - 統合][7]
2. [アクセス管理] で **[OAuth]** をクリックし、**[Add consumer]\(顧客の追加\)** をクリックします。
    ![BitBucket への OAuth 顧客の追加][8]
3. 顧客の **[名前]** を入力します (例: **App Service on Azure Stack**)。
4. アプリケーションの **[説明]** を入力します。
5. **[Callback URL (コールバック URL)]** を入力します。 既定の Azure Stack デプロイでは、コールバック URL は https://portal.local.azurestack.external/TokenAuthorize という形式になります。 別のドメインで実行する場合は、azurestack.local のドメイン名に置き換えます。 BitBucket の統合を成功させるには、URL の大文字と小文字をここに示されているとおりにする必要があります。
6. **[URL]** を入力します。 この URL は、Azure Stack ポータルの URL にする必要があります (例: https://portal.local.azurestack.external)。
7. 必要な **[Permissions]\(権限\)** を次のように選択します。
    - **リポジトリ**: *読み取り*
    - **Webhooks**: *読み取りと書き込み*
8. **[Save]** をクリックします。 これで、この新しいアプリケーションが **[OAuth consumers]\(OAuth コンシューマー\)** の下に **[キー]** と **[シークレット]** と共に表示されます。
    ![BitBucket アプリケーション リスト][9]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてサインインします。
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **キー**をコピーして BitBucket 用の **[クライアント ID]** 入力ボックスに、**シークレット**をコピーして BitBucket 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[Save]** をクリックします。

## <a name="configure-onedrive"></a>OneDrive を構成する

このタスクを完了するには、OneDrive アカウントにリンクされている Microsoft アカウントが必要です。  個人のアカウントではなく、組織のアカウントを使用できます。

> [!NOTE]
> 現時点では、OneDrive for Business アカウントはサポートされていません。

1. https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm に移動し、Microsoft アカウントを使用してサインインします。
2. **[マイ アプリケーション]** で **[アプリを追加]** をクリックします。
![OneDrive アプリケーション][10]
3. 新しく登録するアプリケーションの **[名前]** を入力します。「**App Service on Azure Stack**」と入力し、**[アプリケーションの作成]** をクリックします。
4. 次の画面に、新しいアプリケーションのプロパティが表示されます。 **[アプリケーション ID]** をどこか一時的な場所に保存します。
![OneDrive アプリケーションのプロパティ][11]
5. **[アプリケーション シークレット]** で **[新しいパスワードを生成]** をクリックします。 **新しく生成されたパスワード**をメモします。 これがお客様のアプリケーション シークレットになります。**[OK]** をクリックした後で、もう一度取得することはできません。
6. **[プラットフォーム]** で **[プラットフォームの追加]** をクリックし、**[Web]** を選択します。
7. **[リダイレクト URI]** を入力します。 既定の Azure Stack デプロイでは、リダイレクト URI は https://portal.local.azurestack.external/TokenAuthorize という形式になります。 別のドメインで実行する場合は、azurestack.local のドメイン名に置き換えます。
![OneDrive アプリケーション - Web プラットフォームの追加][12]
8. **[Microsoft Graph のアクセス許可]** - **[委任されたアクセス許可]** を設定します。
    - **Files.ReadWrite.AppFolder**
    - **User.Read**  
      ![OneDrive アプリケーション - Graph のアクセス許可][13]
9. **[Save]** をクリックします。
10.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてサインインします。
11.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
12. **[Source control configuration (ソース管理構成)]** をクリックします。
13. **アプリケーション ID** をコピーして OneDrive 用の **[クライアント ID]** 入力ボックスに、**パスワード**をコピーして OneDrive 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
14. **[Save]** をクリックします。

## <a name="configure-dropbox"></a>DropBox を構成する

> [!NOTE]
> このタスクを完了するには、DropBox アカウントが必要です。 個人のアカウントではなく、組織のアカウントを使用できます。

1. https://www.dropbox.com/developers/apps に移動し、DropBox アカウントの資格情報を使用してサインインします。
2. **[Create app]** をクリックします。

    ![Dropbox アプリケーション][14]

3. **[Dropbox API]** を選択します。
4. アクセス レベルを **[App folder]\(アプリ フォルダー\)** に設定します。
5. アプリケーションの **[名前]** を入力します。
![Dropbox アプリケーションの登録][15]
6. **[Create app (アプリの作成)]** をクリックします。 **[アプリ キー]** と **[アプリケーション シークレット]** を含む、アプリの設定を一覧表示するページが表示されます。
7. **[App folder name]\(アプリ フォルダー名\)** が **[App Service on Azure Stack]** に設定されていることを確認します。
8. **[OAuth 2 Redirect URI\(OAuth 2 リダイレクト URI\])** を設定し、**[追加]** をクリックします。 既定の Azure Stack デプロイでは、リダイレクト URI は https://portal.local.azurestack.external/TokenAuthorize という形式になります。 別のドメインで実行する場合は、azurestack.local のドメインに置き換えます。
![Dropbox アプリケーションの構成][16]
9.  新しいブラウザー タブまたはウィンドウで、Azure Stack 管理ポータル (https://adminportal.local.azurestack.external) にサービス管理者としてサインインします。
10.  **[リソースプロバイダー]** に移動し、**[App Service Resource Provider Admin (App Service リソース プロバイダー管理)]** を選択します。
11. **[Source control configuration (ソース管理構成)]** をクリックします。
12. **アプリケーション キー**をコピーして DropBox 用の **[クライアント ID]** 入力ボックスに、**アプリ シークレット**をコピーして DropBox 用の **[クライアント シークレット]** 入力ボックスに貼り付けます。
13. **[Save]** をクリックします。

## <a name="next-steps"></a>次の手順

[継続的配置](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)、[ローカルの Git デプロイ](https://docs.microsoft.com/azure/app-service/deploy-local-git)、[クラウド フォルダーの同期](https://docs.microsoft.com/azure/app-service-web/deploy-content-sync)などでデプロイ ソースを使用できるようになりました。

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
