---
title: アプリ認証コードを作成する
titleSuffix: Azure Digital Twins
description: クライアント アプリケーションに認証コードを書き込む方法を説明します
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a1e397acfe8118c339b45d6c786ae2c0b2cc82e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124827016"
---
# <a name="write-client-app-authentication-code"></a>クライアント アプリの認証コードを書き込む

[Azure Digital Twins のインスタンスと認証を設定](how-to-set-up-instance-portal.md)した後、インスタンスとのやりとりに使用するクライアント アプリケーションを作成できます。 スターター クライアント プロジェクトを設定した後、Azure Digital Twins のインスタンスに対して **認証を行うためのコードをそのクライアント アプリに書き込む** 必要があります。

Azure Digital Twins では [OAUTH 2.0 に基づく Azure AD セキュリティ トークン](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)を使用して認証します。 ご使用の SDK を認証するには、Azure Digital Twins に対する適切なアクセス許可を持つベアラー トークンを取得し、API 呼び出しと共にこれを渡す必要があります。 

この記事では、`Azure.Identity` クライアント ライブラリを使用して資格情報を取得する方法について説明します。 この記事では C# のコード サンプルを示していますが ([.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) 用に記述するコードなど)、使用する SDK に関係なく `Azure.Identity` のバージョンを使用できます (Azure Digital Twins で使用できる SDK の詳細については、「[Azure Digital Twins API および SDK](concepts-apis-sdks.md)」をご覧ください)。

## <a name="prerequisites"></a>前提条件

最初に、「[インスタンスと認証を設定する](how-to-set-up-instance-portal.md)」のセットアップ手順を完了します。 このセットアップにより、Azure Digital Twins インスタンスがあり、ユーザーにアクセス許可があることが確保されます。 このセットアップが完了すると、クライアント アプリのコードを記述することができます。

続行するには、コードを記述するクライアント アプリ プロジェクトが必要です。 クライアント アプリ プロジェクトをまだ設定していない場合は、このチュートリアルで使用する基本的なプロジェクトを、選択した言語で作成します。

## <a name="authenticate-using-azureidentity-library"></a>Azure.Identity ライブラリを使用して認証する

`Azure.Identity` は、ベアラー トークンを取得して SDK で認証するために使用できる、資格情報を取得するためのメソッドをいくつか提供するクライアント ライブラリです。 この記事では C# の例を示していますが、次のようないくつかの言語で `Azure.Identity` を表示できます。

* [.NET (C#)](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

`Azure.Identity` で資格情報を取得するための一般的な 3 つのメソッドは次のとおりです。

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) では、Azure にデプロイされるアプリケーションに対して既定の `TokenCredential` 認証フローが提供されます。これは、**ローカル開発に推奨** されています。 また、これを有効にして、この記事で推奨されている他の 2 つのメソッドを試すこともできます。つまり、これで `ManagedIdentityCredential` をラップすることや、構成変数で `InteractiveBrowserCredential` にアクセスすることができます。
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) は、[マネージド ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) を必要とする場合に適しており、Azure Functions を操作する場合や Azure サービスにデプロイする場合の有力な選択肢です。
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) は、対話型アプリケーションを対象とし、認証された SDK クライアントを作成するために使用できます

この記事の残りの部分で、[.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) でこれらを使用する方法について説明します。

### <a name="add-azureidentity-to-your-net-project"></a>.NET プロジェクトに Azure.Identity を追加する

`Azure.Identity` を使用して認証するように .NET プロジェクトを設定するには、次の手順を完了します。

1. プロジェクトに SDK パッケージ `Azure.DigitalTwins.Core` と `Azure.Identity` パッケージを含めます。 選択するツールによっては、Visual Studio パッケージ マネージャーまたは `dotnet` コマンド ライン ツールを使用して、パッケージを含めることができます。 

1. 次の using ステートメントをプロジェクト コードに追加します。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

次に、`Azure.Identity` のメソッドの 1 つを使用して、資格情報を取得するコードを追加します。 それぞれの使用方法について、以下のセクションで詳しく説明します。

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential メソッド

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) では、Azure にデプロイされるアプリケーションに対して既定の `TokenCredential` 認証フローが提供されます。これは、**ローカル開発に推奨** されています。

既定の Azure 資格情報を使用するには、Azure Digital Twins インスタンスの URL が必要です ([確認の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))。

`DefaultAzureCredential` をご自分のプロジェクトに追加するためのコード サンプルを次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>ローカルの Azure 資格情報を設定する

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential メソッド

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) メソッドは、[マネージド ID (MSI)](../active-directory/managed-identities-azure-resources/overview.md) が必要な場合に適しています。たとえば、[Azure Functions で認証する](#authenticate-azure-functions)場合などです。

つまり、同じプロジェクトで `ManagedIdentityCredential` を `DefaultAzureCredential` または `InteractiveBrowserCredential` として使用し、プロジェクトの別の部分を認証することができます。

既定の Azure 資格情報を使用するには、Azure Digital Twins インスタンスの URL が必要です ([確認の手順](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))。 また、[アプリ登録](./how-to-create-app-registration-portal.md)と、その登録の[アプリケーション (クライアント) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id) が必要になる場合があります。

Azure 関数で、次のようにマネージド ID の資格情報を使用できます。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential メソッド

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) メソッドは、対話型アプリケーションを対象としており、認証用の Web ブラウザーが開きます。 対話型認証が必要な場合には、このメソッドを `DefaultAzureCredential` の代わりに使用できます。

対話型のブラウザー資格情報を使用するには、Azure Digital Twins API へのアクセス許可がある **アプリの登録** が必要になります。 このアプリ登録を設定する手順については、「[アプリ登録を作成する](./how-to-create-app-registration-portal.md)」をご覧ください。 アプリの登録が設定されたら、次のものが必要になります。
* [アプリの登録のアプリケーション (クライアント) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [アプリの登録のディレクトリ (テナント) ID](./how-to-create-app-registration-portal.md#collect-client-id-and-tenant-id)
* [Azure Digital Twins インスタンスの URL](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

`InteractiveBrowserCredential` を使用して認証された SDK クライアントを作成するコードの例を次に示します。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 前述のように、クライアント ID、テナント ID およびインスタンス URL をコードに直接配置することはできますが、代わりに、コードでこれらの値を構成ファイルまたは環境変数から取得することをお勧めします。

## <a name="authenticate-azure-functions"></a>Azure Functions を認証する

このセクションでは、Azure Functions で認証するコンテキストにおける重要な構成の選択肢をいくつか紹介します。 最初に、関数から Azure Digital Twins にアクセスできるようにするためのお勧めのクラス レベルの変数と認証コードについて説明します。 次に、コードが Azure に発行された後に、関数の完了に必要な最終的な構成手順についていくつか説明します。 

### <a name="write-application-code"></a>アプリケーション コードを記述する

Azure 関数を記述するときは、次の変数とコードを関数に追加することを検討してください。

* **Azure Digital Twins サービスの URL を環境変数または構成設定として読み取るコード。** 関数内にハードコーディングするのではなく、[アプリケーション設定または環境変数](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal)からサービス URL を読み取ることをお勧めします。 Azure 関数で、環境変数を読み取るコードは次のようになります。 

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

    後で、関数を発行してから、このコードで読み取る環境変数の値を作成して設定します。 これを行う方法については、「[アプリケーション設定の構成](#configure-application-settings)」に進んでください。

* **HttpClient インスタンスを保持する静的変数。** HttpClient の作成には比較的コストがかかります。そのため、認証コードを使用して 1 回作成し、すべての関数呼び出しに対して作成されないようにすることをお勧めします。

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **マネージド ID 資格情報。** 関数で Azure Digital Twins にアクセスするために使用するマネージド ID 資格情報を作成します。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

    後で、関数を発行してから、関数の ID に Azure Digital Twins API へのアクセス許可があることを確認します。 これを行う方法については、「[アクセス ロールを割り当てる](#assign-an-access-role)」に進んでください。    

* **ローカル変数 _DigitalTwinsClient_。** 関数の内部にこの変数を追加して Azure Digital Twins クライアント インスタンスを保持します。 クラス内でこの変数を static に "*しないでください*"。
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **_adtInstanceUrl_ の null チェック。** adtInstanceUrl の null チェックを追加し、関数のロジックを、try/catch ブロックで囲んで例外をすべてキャッチします。

これらの変数が関数に追加されると、関数コードは次の例のようになります。

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

認証と関数のロジックの追加など、関数コードが完了したら、[アプリを Azure に発行](../azure-functions/functions-develop-vs.md#publish-to-azure)します

### <a name="configure-published-app"></a>発行されたアプリを構成する

最後に、発行された Azure 関数に対して次の構成手順を完了して、Azure Digital Twins インスタンスにアクセスできることを確認します。

[!INCLUDE [digital-twins-configure-function-app-cli.md](../../includes/digital-twins-configure-function-app-cli.md)]

## <a name="authenticate-across-tenants"></a>テナントをまたいだ認証

Azure Digital Twins は、1 つの [Azure Active Directory (Azure AD) テナント](../active-directory/develop/quickstart-create-new-tenant.md) (Azure Digital Twins インスタンスが配置されているサブスクリプションのメイン テナント) のみをサポートするサービスです。

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

インスタンスとは異なるテナントに属しているサービス プリンシパルまたはユーザー アカウントを使用して Azure Digital Twins インスタンスにアクセスする必要がある場合は、別のテナントの各フェデレーション ID が Azure Digital Twins インスタンスの "ホーム" テナントからの **トークン** を要求することができます。 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

また、コードの資格情報オプションでホーム テナントを指定することもできます。 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>その他の資格情報のメソッド

上で取り上げた認証シナリオがご自分のアプリのニーズを満たしていない場合、[Microsoft ID プラットフォーム](../active-directory/develop/v2-overview.md#getting-started)で提供されている他の種類の認証を検討することができます。 このプラットフォームのドキュメントでは、その他の認証シナリオが取り上げられており、アプリケーションの種類別に整理されています。

## <a name="next-steps"></a>次のステップ

以下を参照し、Azure Digital Twins でのセキュリティのしくみの詳細を確認します。
* [Azure Digital Twins ソリューションのセキュリティ](concepts-security.md)

または、認証が設定されたので、インスタンスでのモデルの作成および管理に進みます。
* [DTDL モデルを管理する](how-to-manage-model.md)