---
title: Azure 関数アプリに Media Services アカウントへのアクセス権を付与する
description: ブロードキャスト スタジオ用に "On Air" (放送中) サインを作成するとします。 Media Services API を使用して、Media Services ライブ イベントがいつ実行されているかを判断することはできますが、埋め込みデバイスから呼び出すのは難しい場合があります。 代わりに、Azure Functions を使用して埋め込みデバイスの HTTP API を公開できます。 これにより、Azure Functions が Media Services を呼び出して、ライブイベントの状態を取得できます。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: inhenkel
ms.openlocfilehash: dc05d6488978004eebee68b901214ab71f0fffd4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656647"
---
# <a name="tutorial-give-an-azure-function-app-access-to-a-media-services-account"></a>チュートリアル: Azure 関数アプリに Media Services アカウントへのアクセス権を付与する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

たとえば、自分の Web サイトまたはアプリケーションの訪問者に、ブロードキャスト スタジオで "On Air" (放送中) であることを知らせたいとします。 Media Services API を使用して、Media Services ライブ イベントがいつ実行されているかを判断することはできますが、埋め込みデバイスから呼び出すのは難しい場合があります。 代わりに、Azure Functions を使用して埋め込みデバイスの HTTP API を公開できます。 これにより、Azure Functions が Media Services を呼び出して、ライブイベントの状態を取得できます。

:::image type="content" source="media/diagrams/managed-identities-scenario-function-app-access-media-services-account.svg" alt-text="関数アプリに Media Services アカウントへのアクセスを付与するマネージド ID":::

このチュートリアルでは、2020-05-01 Media Services API を使用します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

この記事のいずれかのコマンドを使用するには、まず、使用するサブスクリプションにサインインする必要があります。

 [!INCLUDE [Sign in to Azure with the CLI](./includes/task-sign-in-azure-cli.md)]

### <a name="set-subscription"></a>サブスクリプションの設定

このコマンドを使用して、使用するサブスクリプションを設定します。

[!INCLUDE [Sign in to Azure with the CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> このチュートリアルを実行する前に、[コマンド ラインのクイックスタートから Azure で C# 関数を作成](../../azure-functions/create-first-function-cli-csharp.md)することを強くお勧めします。  これは、それに含まれているセットアップ手順がここで必要な手順と同じであるためです。  また、このチュートリアルの基になっている簡単な例を使用することもできます。

## <a name="resource-names"></a>リソース名

作業を開始する前に、作成するリソースの名前を決定します。  これらは、テストの完了後に使用する予定がない場合は特に、セットとして簡単に識別できる必要があります。 名前付けルールは多くのリソースの種類で異なるため、すべて小文字を使用することをお勧めします。 たとえば、リソース グループ名は "mediatest1rg"、ストレージ アカウント名は "mediatest1stor" にします。 この記事の各手順では、同じ名前を使用します。

これらの名前は、下のコマンドで参照されます。  必要なリソースの名前は次のとおりです。

- your-resource-group-name
- your-storage-account-name
- your-media-services-account-name
- your-region
- your-function-name: "OnAir" を使用
- your-live-event-name: "live1" を使用
- your-ip-address: "0.0.0./32" を使用

> [!NOTE]
> 上記のハイフンは、ガイダンス ワードを分離するためにのみ使用されています。 Azure サービスではリソースの名前付けに一貫性がないため、リソースに名前を付けるときにハイフンを使用しないでください。<br/><br/>
> 00000000-0000-0000-0000000000 で表されるものは、リソースの一意の識別子です。  通常、この値は JSON 応答によって返されます。 また、JSON 応答には、後の CLI コマンドに必要な値が含まれているため、これらの応答をコピーしてメモ帳または他のテキスト エディターに貼り付けることをお勧めします。<br/><br/>
> また、リージョン名は作成しないでください。  リージョン名は Azure によって決定されます。

### <a name="list-azure-regions"></a>Azure リージョンを一覧表示する

使用する実際のリージョン名が不明な場合は、次のコマンドを使用して一覧を取得します。

[!INCLUDE [List Azure regions with the CLI](./includes/task-list-azure-regions-cli.md)]

## <a name="sequence"></a>シーケンス

JSON 応答から 1 つまたは複数の値がシーケンスの次の手順で使用されるため、下の各手順は特定の順序で行われます。

## <a name="create-a-resource-group"></a>リソース グループを作成する

作成するリソースは、リソース グループに属している必要があります。 まず、リソース グループを作成します。 Media Services アカウントの作成手順と後続の手順には、`your-resource-group-name` を使用します。

[!INCLUDE [Create a resource group with the CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

作成する Media Services アカウントには、ストレージ アカウントが関連付けられている必要があります。 最初に、Media Services アカウントのストレージ アカウントを作成します。 以降のステップには、`your-storage-account-name` を使用します。

[!INCLUDE [Create a Storage account with the CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

次に、Media Services アカウントを作成します。 ` を探します。

[!INCLUDE [Create a Media Services account with the CLI](./includes/task-create-media-services-account-cli.md)]

## <a name="set-up-the-azure-function"></a>Azure 関数を設定する

このセクションでは、Azure 関数を設定します。

### <a name="get-the-code"></a>コードを取得する

Azure Functions を使用して、関数プロジェクトを作成し、HTTP テンプレートからコードを取得します。

```azurecli-interactive
func init MediaServicesLiveMonitor –dotnet
```

### <a name="change-directory"></a>ディレクトリを変更する

作業ディレクトリをプロジェクト ディレクトリに必ず変更してください。  そうしない場合、エラーが発生します。

```azurecli-interactive
cd .\MediaServicesLiveMonitor\
```

### <a name="name-your-function"></a>関数名の指定

```azurecli-interactive
func new --name OnAir --template "HTTP trigger" --authlevel "anonymous"
```

## <a name="configure-the-functions-project"></a>関数プロジェクトを構成する

### <a name="install-media-services-and-other-extensions"></a>Media Services とその他の拡張機能をインストールする

ターミナル ウィンドウで dotnet add package コマンドを実行して、プロジェクトに必要な拡張機能パッケージをインストールします。 次のコマンドを実行すると、Media Services と Azure ID パッケージがインストールされます。

```bash
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

### <a name="edit-the-onaircs-code"></a>OnAir.cs コードを編集する

`OnAir.cs` ファイルを変更します。 `subscriptionId`、`resourceGroup`、`mediaServicesAccountName` の各変数を、前に決定したものに変更します。

```csharp
using Azure.Core;
using Azure.Identity;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Rest;
using System.Threading.Tasks;

namespace MediaServicesLiveMonitor
{
    public static class OnAir
    {
        [FunctionName("OnAir")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            string name = req.Query["name"];

            if (string.IsNullOrWhiteSpace(name))
            {
                return new BadRequestObjectResult("Missing 'name' URL parameter");
            }

            var credential = new ManagedIdentityCredential();
            var accessTokenRequest = await credential.GetTokenAsync(
                new TokenRequestContext(
                    scopes: new string[] { "https://management.core.windows.net" + "/.default" }
                    )
                );
            ServiceClientCredentials credentials = new TokenCredentials(accessTokenRequest.Token, "Bearer");

            var subscriptionId = "00000000-0000-0000-000000000000";                 // Update
            var resourceGroup = "<your-resource-group-name>";                       // Update
            var mediaServicesAccountName = "<your-media-services-account-name>";    // Update

            var mediaServices = new AzureMediaServicesClient(credentials)
            {
                SubscriptionId = subscriptionId
            };

            var liveEvent = await mediaServices.LiveEvents.GetAsync(resourceGroup, mediaServicesAccountName, name);

            if (liveEvent == null)
            {
                return new NotFoundResult();
            }

            return new OkObjectResult(liveEvent.ResourceState == LiveEventResourceState.Running ? "On air" : "Off air");
        }
    }
}
```

## <a name="create-the-function-app"></a>関数アプリを作成する

関数をホストする関数アプリを作成します。 名前は、前にダウンロードした `MediaServicesLiveMonitorApp` と同じです。

```azurecli-interactive

az functionapp create --resource-group <your-resource-group-name> --consumption-plan-location your-region --runtime dotnet --functions-version 3 --name MediaServicesLiveMonitorApp --storage-account mediatest3store --assign-identity "[system]"

```

JSON の応答で `principalId` を見つけます。

```json
{
...  
"identity": {
//Note the principalId value for the following step
    "principalId": "00000000-0000-0000-000000000000",
    "tenantId": "00000000-0000-0000-000000000000",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  }
...
```

## <a name="grant-the-function-app-access-to-the-media-services-account-resource"></a>関数アプリに Media Services アカウント リソースへのアクセスを許可する

この要求の場合:

- `assignee` は、`az functionapp create` からの JSON 応答内の `principalId` です。
- `scope` は、`az ams account create` からの JSON 応答内の `id` です。  上記の JSON 応答の例を参照してください。

```azurecli-interactive
az role assignment create --assignee 00000000-0000-0000-000000000000 --role "Media Services Account Administrator" --scope "/subscriptions/<the-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.Media/mediaservices/<your-media-services-account-name>"
```

## <a name="publish-the-function"></a>関数を発行する

```azurecli-interactive
func azure functionapp publish MediaServicesLiveMonitorApp
```

## <a name="validation"></a>検証

ブラウザーで、関数の URL に移動します。例:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

ライブ イベントがまだ存在しないため、404 (Not Found) エラーが返されます。

### <a name="create-a-live-event"></a>ライブ イベントを作成する

```azurecli-interactive
az ams live-event create --resource-group test3 --account-name mediatest3 --name live1 --streaming-protocol RTMP
```

ブラウザーで、関数の URL に移動します。例:

`https://mediaserviceslivemonitorapp.azurewebsites.net/api/onair?name=live1`

これで、"Off Air" (放送中止) と表示されます。

### <a name="start-the-live-event"></a>ライブ イベントを開始する

ライブ イベントを開始すると、関数は "On Air" (放送中) を返します。

```azurecli-interactive
az ams live-event start live1
```

この関数は、すべてのユーザーにアクセスを許可します。 このドキュメントでは、Azure 関数へのアクセスをセキュリティで保護し、"On Air" ライトを接続する方法については説明しません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを使用する予定がない場合は、そのリソース グループを削除します。

[!INCLUDE [Create a Media Services account with the CLI](./includes/clean-up-resources-cli.md)]
