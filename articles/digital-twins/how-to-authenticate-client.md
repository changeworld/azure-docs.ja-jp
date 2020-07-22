---
title: クライアント アプリケーションを認証する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins サービスに対して、クライアント アプリケーションを認証する方法を確認します。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390824"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Azure Digital Twins を使用してクライアント アプリケーションを認証する

[Azure Digital Twins インスタンスを作成](how-to-set-up-instance.md)した後、インスタンスとのやり取りに使用するクライアント アプリケーションを作成できます。 スターター クライアント プロジェクトを設定したら、この記事では、そのクライアント アプリケーションを Azure Digital Twins インスタンスを使用して適切に認証する方法を示します。

これは、次の 2 つの手順で行われます
1. アプリの登録を作成する
2. クライアント アプリケーションに認証コードを書き込む

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>アプリの登録を作成する

クライアント アプリケーションから Azure Digital Twins に対して認証を行うには、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) で**アプリの登録**を設定する必要があります。

このアプリの登録では、[Azure Digital Twins API](how-to-use-apis-sdks.md) に対するアクセス許可を構成します。 クライアント アプリによって、アプリの登録に対する認証が行われます。その結果、API に対する構成済みのアクセス許可が付与されます。

アプリの登録を作成するには、Azure Digital Twins API のリソース ID と、API に対するベースライン アクセス許可を指定する必要があります。 作業ディレクトリで、新しいファイルを開き、次の JSON スニペットを入力して、これらの詳細を構成します。 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

このファイルを *manifest.json* として保存します。

> [!NOTE] 
> GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` ではなく、"わかりやすい" 人間が判読できる文字列 `https://digitaltwins.azure.net` を Azure Digital Twins のリソース アプリ ID に使用できる場所がいくつかあります。 たとえば、このドキュメント セット全体の多くの例では、MSAL ライブラリでの認証が使用されており、わかりやすい文字列を使用できます。 しかし、アプリの登録を作成するこの手順では、上に示されているように、GUID 形式の ID が必要です。 

Cloud Shell ウィンドウで、[ファイルのアップロード/ダウンロード] アイコンをクリックし、[アップロード] を選択します。

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="[アップロード] オプションが選択されていることを示す Cloud Shell ウィンドウ":::
先ほど作成した *manifest.json* に移動し、[開く] をクリックします。

次に、以下のコマンドを実行して、アプリの登録を作成します (必要に応じて、プレースホルダーを置き換える)。

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

このコマンドの出力はこのようになります。

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="新しい AAD アプリの登録":::

アプリの登録を作成した後、[このリンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)に従って、Azure portal の AAD アプリ登録の概要ページに移動します。

この概要で、先ほど作成したアプリの登録を一覧から選択します。 これにより、このようなページでその詳細が示されます。

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure portal: 認証 ID":::

**実際の**ページに表示される、"*アプリケーション (クライアント) ID*" と "*ディレクトリ (テナント) ID*" をメモしておきます。 これらの値は、後で Azure Digital Twins API に対してクライアント アプリを認証するために使用します。

> [!NOTE]
> シナリオによっては、アプリの登録にさらに変更を加える必要がある場合があります。 満たす必要がある場合がある一般的な要件をいくつか以下に示します。
> * パブリック クライアント アクセスをアクティブ化する
> * Web およびデスクトップへのアクセスに特定の応答 URL を設定する
> * 暗黙の OAuth2 認証フローを許可する
> * Azure サブスクリプションが Live、Xbox、Hotmail などの Microsoft アカウントを使用して作成されている場合は、個人のアカウントをサポートするように、アプリの登録で *signInAudience* を設定する必要があります。
> これらの設定を行う最も簡単な方法は、[Azure portal](https://portal.azure.com/) を使用することです。 このプロセスの詳細については、「[Microsoft ID プラットフォームにアプリケーションを登録する](https://docs.microsoft.com/graph/auth-register-app-v2)」を参照してください。

## <a name="write-client-app-authentication-code-net-c-sdk"></a>クライアント アプリの認証コードを書き込む: .NET (C#) SDK

このセクションでは、.NET (C#) SDK を使用して認証プロセスを完了するために、クライアント アプリケーションに含める必要があるコードについて説明します。
Azure Digital Twins C# SDK は、Azure SDK for .NET. に含まれています。 これはこちらにあります: [.NET 用 Azure IoT Digital Twins クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。

### <a name="prerequisites"></a>前提条件

スターター クライアント アプリ プロジェクトをまだ設定していない場合は、このチュートリアルで使用する基本的な .NET プロジェクトを作成します。

.NET SDK を使用するには、次のパッケージをプロジェクトに含める必要があります。
* `Azure.DigitalTwins.Core` (バージョン `1.0.0-preview.2`)
* `Azure.Identity`

選択するツールによっては、Visual Studio パッケージ マネージャーまたは `dotnet` コマンド ライン ツールを使用して行うことができます。 

### <a name="authentication-and-client-creation-net"></a>認証およびクライアントの作成: .NET

.NET SDK を使用して認証するには、[Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) ライブラリに定義されている資格情報の取得方法のいずれかを使用します。

一般的に使用される 2 つを以下に示します。 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). この方法は、対話型アプリケーションを対象としており、認証用の Web ブラウザーが開きます。
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). この方法は、Azure Functions を使用する場合など、[マネージド ID (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) が必要な場合に適しています。 

次の using ステートメントも必要です。

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

対話型ブラウザーの資格情報を使用して、認証された SDK クライアントを作成するには、このコードを追加します。

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 前述のように、クライアント ID、テナント ID およびインスタンス URL をコードに直接配置することはできますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

その後、Azure 関数で、このようにマネージド ID の資格情報を使用できます。

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

[方法: データを処理するための Azure 関数の設定](how-to-create-azure-function.md)に関するページを参照し、関数のコンテキストでの重要な構成のいくつかの選択について説明する詳細な例を確認してください。

また、関数で認証を使用する場合は、次の点に注意してください。
* [マネージド ID を有効にする](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [環境変数](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 関数アプリにアクセス許可を割り当てて、Digital Twins API にアクセスできるようにする。 [方法: データを処理するための Azure 関数の設定](how-to-create-azure-function.md)に関するページを参照し、詳細を確認してください。

## <a name="authentication-in-an-autorest-generated-sdk"></a>AutoRest で生成された SDK での認証

.NET を使用しない場合は、任意の言語で SDK ライブラリを構築することを選択できます ([方法: AutoRest を使用する Azure Digital Twins 用のカスタム SDK の作成](how-to-create-custom-sdks.md)に関するページを参照)。

このセクションでは、その場合に認証する方法について説明します。

### <a name="prerequisites"></a>前提条件

この例では、AutoRest で生成された Typescript SDK を使用します。 その結果、次のものも必要になります。
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>最小限の認証コード サンプル

Azure サービスを使用して .NET アプリを認証する場合は、クライアント アプリ内で次の最小限のコードを使用できます。

Azure Digital Twins インスタンスの URL だけでなく、前述の "*アプリケーション (クライアント) ID*" と "*Directory (テナント) ID*" が必要になります。

> [!TIP]
> Azure Digital Twins インスタンスの URL は、Azure Digital Twins インスタンスの *hostName* の先頭に *https://* を追加することによって作成されます。 インスタンスのすべてのプロパティと共に *hostName* を表示する場合は、`az dt show --dt-name <your-Azure-Digital-Twins-instance>` を実行できます。 `az account show --query tenantId` コマンドを使用して、"*ディレクトリ (テナント) ID*" を表示できます。 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

ここでも、わかりやすくするために、上記のコードではクライアント ID、テナント ID およびインスタンス URL をコードに直接配置していますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

MSAL には、キャッシュなどの認証フローを実装するために、使用できるオプションが他にも多数用意されています。 この詳細については、「[Microsoft Authentication Library (MSAL) の概要](../active-directory/develop/msal-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

以下を参照し、Azure Digital Twins でのセキュリティのしくみの詳細を確認します。
* [概念:Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)

または、認証が設定されたので、以下を参照し、インスタンスでのモデルの作成に進みます。
* [方法: カスタム モデルを管理する](how-to-manage-model.md)