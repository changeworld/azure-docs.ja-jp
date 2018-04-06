---
title: Mobile Engagement REST API での認証
description: Azure Mobile Engagement REST API を使用して認証を行う方法について説明します
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 5979ded9afaa31054f835b5f16fe525809f5730d
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Mobile Engagement REST API での認証
> [!IMPORTANT]
> Azure Mobile Engagement は、2018 年 3 月 31 日に停止されます。 このページは、その後まもなく削除されます。
> 

## <a name="overview"></a>概要

このドキュメントでは、有効な Azure Active Directory (Azure AD) OAuth トークンを取得して Mobile Engagement REST API で認証を行う方法について説明します。

この手順では、有効な Azure サブスクリプションを持っており、いずれかの [開発者チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を使用して Mobile Engagement アプリを作成済みであることを前提としています。

## <a name="authentication"></a>認証

認証には、Microsoft Azure Active Directory ベースの OAuth トークンを使用します。 

API の要求を認証するには、すべての要求に Authorization ヘッダーが追加されている必要があります。 Authorization ヘッダーは次の形式です。

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory のトークンは 1 時間で有効期限が切れます。
> 
> 

トークンを入手するにはいくつかの方法があります。 API はクラウド サービスから呼び出されるので、API キーを使用します。 Azure の用語では、API キーはサービス プリンシパル パスワードと呼ばれます。 次の手順では、手動で設定する方法について説明します。

### <a name="one-time-setup-using-a-script"></a>1 回限りのセットアップ (スクリプトを使用)

PowerShell スクリプトを使用してセットアップを実行するには、次の手順を実行します。 PowerShell スクリプトではセットアップにかかる時間が最短になりますが、許容されるほとんどの既定値を使用します。 

あるいは、[手動セットアップ](mobile-engagement-api-authentication-manual.md)の手順に従って Azure Portal から直接行うこともできます。 Azure Portal から設定する場合は、さらに詳細に構成できます。

1. Azure PowerShell の最新バージョンを[ダウンロード](http://aka.ms/webpi-azps)して入手します。 ダウンロードの手順の詳細については、[この概要](/powershell/azure/overview)を参照してください。

2. PowerShell をインストールした後は、次のコマンドを使用して、**Azure モジュール** がインストールされていることを確認します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 使用可能なモジュールのリストで Azure PowerShell モジュールが使用可能であることを確認します。

        Get-Module –ListAvailable

    ![利用可能な Azure モジュール][1]

    b. 前のリストで Azure PowerShell モジュールが見つからない場合は、次を実行する必要があります。

        Import-Module Azure
3. 次のコマンドを実行して、PowerShell から Azure Resource Manager にサインインします。 Azure アカウントのユーザー名とパスワードを入力します。 

        Login-AzureRmAccount
4. 複数のサブスクリプションがある場合は、次の手順を実行します。

    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 すべてのサブスクリプションの一覧を取得します。 次に、使用するサブスクリプションの **SubscriptionId** をコピーします。 このサブスクリプションに Mobile Engagement アプリが含まれていることを確認します。 このアプリを使用して、API を操作します。 

        Get-AzureRmSubscription

    b. 次のコマンドを実行します。 **SubscriptionId** を指定して、使用するサブスクリプションを構成します。

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) スクリプトのテキストをローカル コンピューターにコピーします。 次に、PowerShell コマンドレット (例: `APIAuth.ps1`) として保存して、実行します。

         `.\APIAuth.ps1`.

6. **principalName** の入力を求められます。 Active Directory アプリケーションに使用する適切な名前を指定します (例: APIAuth)。 

7. スクリプトの実行が完了した後、次の 4 つの値が表示されます。 これらの値を必ずをコピーしてください。Active Directory でのプログラムによる認証に必要となります。 

   - **TenantId**
   - **SubscriptionId**
   - **ApplicationId**
   - **シークレット**

   `{TENANT_ID}` として TenantId を、`{CLIENT_ID}` として ApplicationId を、`{CLIENT_SECRET}` として Secret を使用します。

   > [!NOTE]
   > 既定のセキュリティ ポリシーにより、PowerShell スクリプトの実行がブロックされる可能性があります。 その場合は、次のコマンドを使用して、スクリプトの実行を許可する実行ポリシーを一時的に構成します。
   > 
   > Set-ExecutionPolicy RemoteSigned
8. 一連の PowerShell コマンドレットがどのように表示されるかを次に示します。
    ![PowerShell コマンドレット][3]
9. Azure Portal で [Active Directory] に移動し、**[アプリの登録]** を選択し、アプリを検索して存在することを確認します。
    ![アプリの検索][4]

### <a name="steps-to-get-a-valid-token"></a>有効なトークンを取得する手順

1. 次のパラメーターを指定して API を呼び出します。 **TENANT\_ID**、**CLIENT\_ID**、および **CLIENT\_SECRET** は必ず置き換えてください。
   
   * **要求 URL**: `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type ヘッダー**: `application/x-www-form-urlencoded`
   
   * **HTTP 要求の本文**: `grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    要求の例を次に示します。
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    応答の例を次に示します。
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     この例に含まれている POST パラメーターの URL エンコードの `resource` 値は、実際には `https://management.core.windows.net/` です。 URL エンコード `{CLIENT_SECRET}` にも注意してください。特殊文字が含まれていることがあります。

     > [!NOTE]
     > テストとしては、[Fiddler](http://www.telerik.com/fiddler) や [Chrome Postman extension](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) などの HTTP クライアント ツールを使用できます。 
     > 
     > 
2. すべての API 呼び出しに、次の Authorization 要求ヘッダーを含めます。
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    要求が 401 ステータス コードを返す場合は、応答本文を確認してください。 トークンの期限が切れていることを示している可能性があります。 その場合は、新しいトークンを取得します。

## <a name="use-the-apis"></a>API の使用
有効なトークンが手に入ったので、API 呼び出しを行う準備ができました。

1. 各 API 要求では、期限が切れていない有効なトークンを渡す必要があります。 期限が切れていないトークンは、前のセクションで取得しました。

2. アプリケーションを示す要求 URI にいくつかのパラメーターを組み込みます。 要求 URI は次のコードのようになります。
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    パラメーターを取得するには、アプリケーション名を選択します。 次に、**[ダッシュボード]** を選択します。 次のようなページに 3 つのパラメーターがすべて表示されます。
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** リソース グループ名は、新しく作成したのでない限り **MobileEngagement** です。 

> [!NOTE]
> API ルート アドレスは以前の API 用であるため、無視してください。
> 
> Azure Portal を使用してアプリを作成した場合は、アプリ名自体とは異なるアプリケーション リソース名を使用する必要があります。 Azure Portal でアプリを作成した場合は、アプリ名を使用する必要があります。 (アプリケーション リソース名と、新しいポータルで作成したアプリのアプリ名に違いはありません)。
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



