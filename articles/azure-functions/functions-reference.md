---
title: Azure Functions の開発に関するガイダンス
description: プログラミング言語とバインドを問わず、Azure での関数開発に必要な Azure Functions の概念とテクニックについて説明します。
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 9/02/2021
ms.openlocfilehash: 7aef7301207772711bcce7fbec4bde8937c94cf1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523450"
---
# <a name="azure-functions-developer-guide"></a>Azure Functions 開発者ガイド
Azure Functions の特定の関数は、使用する言語またはバインドに関係なく、いくつかの中核となる技術的な概念とコンポーネントを共有します。 特定の言語またはバインド固有の詳細を学習する前に、それらすべてに当てはまるこの概要をお読みください。

この記事では、「[Azure Functions の概要](functions-overview.md)」を既に読んでいることを前提としています。

## <a name="function-code"></a>関数のコード
*関数* は Azure Functions の主要な概念です。 関数には 2 つの重要な要素が含まれています。さまざまな言語で記述できるコードと、いくつかの構成、つまり function.json ファイルです。 コンパイル式の言語の場合、この構成ファイルはコード内の注釈から自動的に生成されます。 スクリプト言語の場合は、構成ファイルを自分で用意する必要があります。

function.json ファイルには、関数のトリガー、バインド、その他の構成設定を定義します。 すべての関数には、1 つだけトリガーがあります。 ランタイムはこの構成ファイルを使用して、監視対象のイベントを特定し、関数の実行との間でデータを渡したりデータを受け取ったりする方法を判断します。 以下に function.json ファイルの例を示します。

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

詳しくは、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」をご覧ください。

`bindings` プロパティで、トリガーとバインドの両方を構成します。 各バインドは、いくつかの一般的な設定と、バインドの特定の種類に固有の設定を共有します。 すべてのバインドには次の設定が必要です。

| プロパティ    | 値 | Type | 説明|
|---|---|---|---|
| type  | バインドの名前。<br><br>たとえば、「 `queueTrigger` 」のように入力します。 | string | |
| 方向 | `in`, `out`  | string | バインドが関数への受信データか、関数からの送信データかを示します。 |
| name | 関数識別子。<br><br>たとえば、「 `myQueue` 」のように入力します。 | string | 関数のバインドされたデータに使用される名前。 C# の場合は引数の名前です。JavaScript の場合はキー/値リストのキーです。 |

## <a name="function-app"></a>関数アプリ
関数アプリからは、関数が実行される、Azure における実行コンテキストが提供されます。 そのため、これが関数のデプロイと管理の単位となります。 関数アプリは、まとめて管理、デプロイ、およびスケールされる 1 つまたは複数の個々の関数で構成されます。 関数アプリ内のすべての関数は、同じ料金プラン、デプロイ方法、およびランタイム バージョンを共有します。 関数を整理し、まとめて管理する方法として関数アプリを考えてください。 詳しくは、[関数アプリの管理方法](functions-how-to-use-azure-function-app-settings.md)に関する記事をご覧ください。 

> [!NOTE]
> 関数アプリ内のすべての関数は、同じ言語で作成する必要があります。 Azure Functions ランタイムの[以前のバージョン](functions-versions.md)では、これは必須ではありませんでした。

## <a name="folder-structure"></a>フォルダー構造
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

上記は、関数アプリの既定の (そして推奨される) フォルダー構造です。 関数のコードのファイルの場所を変更するには、_function.json_ ファイルの `scriptFile` セクションを変更します。 また、[パッケージ デプロイ](deployment-zip-push.md)を使用して Azure の関数アプリにプロジェクトをデプロイすることもお勧めします。 [継続的インテグレーションとデプロイ](functions-continuous-deployment.md)や Azure DevOps など、既存のツールを使用することもできます。

> [!NOTE]
> パッケージを手動でデプロイする場合は、必ず _host.json_ ファイルと関数フォルダーを直接 `wwwroot` フォルダーにデプロイします。 デプロイに `wwwroot` フォルダーを含めないでください。 そうしないと、`wwwroot\wwwroot` フォルダーができてしまいます。

#### <a name="use-local-tools-and-publishing"></a>ローカル ツールの使用と公開
関数アプリは、[Visual Studio](./functions-develop-vs.md)、[Visual Studio Code](./create-first-function-vs-code-csharp.md)、[IntelliJ](./functions-create-maven-intellij.md)、[Eclipse](./functions-create-maven-eclipse.md)、[Azure Functions Core Tools](./functions-develop-local.md) など、さまざまなツールを利用して作成し、公開できます。 詳細については、「[Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)」を参照してください。

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Azure portal で関数を編集する方法
Azure portal に組み込まれている関数エディターを使用すると、コードと *function.json* ファイルを直接インラインで更新できます。 これは、軽微な変更や概念実証の場合にのみお勧めします。ベスト プラクティスは、VS Code などのローカル開発ツールを使うことです。

## <a name="parallel-execution"></a>並列実行
シングル スレッドの関数ランタイムが処理できるより速く複数のトリガー イベントが発生する場合、ランタイムは関数を並列で複数回呼び出す場合があります。  関数アプリが[従量課金ホスティング プラン](event-driven-scaling.md)を使用している場合、関数アプリは自動的にスケールアウトできます。  アプリが従量課金ホスティング プランと標準の [App Service ホスティング プラン](../app-service/overview-hosting-plans.md)のどちらで実行されていても、関数アプリの各インスタンスは、複数の同時関数呼び出しを、複数のスレッドを使用して並列に処理します。  各関数アプリ インスタンスでの同時関数呼び出しの最大数は、使用されるトリガーの種類と、関数アプリ内の他の関数によって使用されるリソースに応じて異なります。

## <a name="functions-runtime-versioning"></a>Functions ランタイムのバージョン管理

`FUNCTIONS_EXTENSION_VERSION` アプリ設定を使用して、Functions ランタイムのバージョンを構成できます。 たとえば、"~3" の値は、関数アプリがそのメジャー バージョンとして 3.x を使用することを示します。 関数アプリは、リリースされたときにそれぞれの新しいマイナー バージョンにアップグレードされます。 お使いの関数アプリの正確なバージョンを表示する方法など、詳細については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」をご覧ください。

## <a name="repositories"></a>リポジトリ
Azure Functions のコードはオープン ソースであり、GitHub リポジトリに保存されています。

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions のホスト](https://github.com/Azure/azure-functions-host/)
* [Azure Functions ポータル](https://github.com/azure/azure-functions-ux)
* [Azure Functions テンプレート](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK 拡張機能](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>バインド
サポートされるすべてのバインドを次の表に示します。

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

バインドが原因のエラーが発生している場合は、 [Azure Functions のバインド エラー コード](functions-bindings-error-pages.md)に関するドキュメントを参照してください。


## <a name="connections"></a>接続

関数プロジェクトでは、接続情報を構成プロバイダーからの名前で参照しています。 接続の詳細を直接受け入れないため、環境間で変更できます。 たとえば、トリガー定義に `connection` プロパティが含まれるとします。 これは接続文字列が参照されている場合がありますが、接続文字列を `function.json` に直接設定することはできません。 代わりに、`connection` を、接続文字列を含む環境変数の名前に設定します。

既定の構成プロバイダーでは環境変数を使用します。 これらは、Azure Functions サービスで実行している場合は [[アプリケーションの設定]](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings)、ローカルでの開発時には[ローカル設定ファイル](functions-develop-local.md#local-settings-file)で設定できます。

### <a name="connection-values"></a>接続値

接続名が 1 つの正確な値に解決されると、ランタイムでは、値を _接続文字列_ として識別します。これには通常、シークレットが含まれます。 接続文字列の詳細は、接続先のサービスによって定義されます。

ただし、接続名は複数の構成アイテムのコレクションを参照することもでき、これは [IDベースの接続](#configure-an-identity-based-connection)を構成するのに便利です。 2 つのアンダースコア `__` で終わる共有プレフィックスを使用して、環境変数をコレクションとして扱うことができます。 このプレフィックスに接続名を設定することによって、グループを参照できます。

たとえば、Azure BLOB トリガー定義の `connection` プロパティが "Storage1" であるとします。 "Storage1" という名前の環境変数で構成された単一の文字列値がない限り、`Storage1__blobServiceUri` という名前の環境変数を使用して、接続の `blobServiceUri` プロパティを通知できます。 接続のプロパティはサービスによって異なります。 接続を使用するコンポーネントのドキュメントを参照してください。

### <a name="configure-an-identity-based-connection"></a>ID ベースの接続を構成する

Azure Functions の一部の接続は、シークレットの代わりに ID を使用するように構成できます。 サポートは、接続を使用する拡張機能によって異なります。 場合によっては、接続先のサービスで ID ベースの接続がサポートされている場合でも、Functions で接続文字列が必要になることがあります。 マネージド ID を使用して関数アプリを構成するチュートリアルについては、ID ベースの接続を使用した関数アプリの作成に関 [するチュートリアルを参照してください](./functions-identity-based-connections-tutorial.md)。

ID ベースの接続は、次のコンポーネントでサポートされています。

| 接続元                                       | サポートされているプラン | 詳細情報                                                                                                         |
|---------------------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------|
| Azure BLOB のトリガーとバインディング               | All             | [拡張機能バージョン 5.0.0 以降](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)     |
| Azure キューのトリガーとバインディング            | All             | [拡張機能バージョン 5.0.0 以降](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher)    |
| Azure Event Hubs のトリガーとバインディング     | All             | [拡張機能バージョン 5.0.0 以降](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher)    |
| Azure Service Bus のトリガーとバインディング       | All             | [拡張機能バージョン 5.0.0 以降](./functions-bindings-service-bus.md#service-bus-extension-5x-and-higher)  |
| Azure Cosmos DB のトリガーとバインディング - プレビュー         | エラスティック Premium | [拡張機能バージョン 4.0.0-preview1 以降](./functions-bindings-cosmosdb-v2.md#cosmos-db-extension-4x-and-higher) |
| ホスト (必須) ストレージ ("AzureWebJobsStorage") - プレビュー | All             | [ID を使用してホスト ストレージに接続する](#connecting-to-host-storage-with-an-identity-preview)                        |

> [!NOTE]
> ID ベースの接続は、Durable Functions ではサポートされません。

[!INCLUDE [functions-identity-based-connections-configuration](../../includes/functions-identity-based-connections-configuration.md)]

以下のタブを選択して、各コンポーネントのアクセス許可について学習します。

# <a name="azure-blobs-extension"></a>[Azure BLOB の拡張機能](#tab/blob)

[!INCLUDE [functions-blob-permissions](../../includes/functions-blob-permissions.md)]

# <a name="azure-queues-extension"></a>[Azure キューの拡張機能](#tab/queue)

[!INCLUDE [functions-queue-permissions](../../includes/functions-queue-permissions.md)]

# <a name="event-hubs-extension"></a>[Event Hubs の拡張機能](#tab/eventhubs)

[!INCLUDE [functions-event-hubs-permissions](../../includes/functions-event-hubs-permissions.md)]

# <a name="service-bus-extension"></a>[Service Bus の拡張機能](#tab/servicebus)

[!INCLUDE [functions-service-bus-permissions](../../includes/functions-service-bus-permissions.md)]

# <a name="azure-cosmos-db-extension-preview"></a>[Azure Cosmos DB の拡張機能 (プレビュー)](#tab/cosmos)

[!INCLUDE [functions-cosmos-permissions](../../includes/functions-cosmos-permissions.md)]

# <a name="functions-host-storage-preview"></a>[Functions ホスト ストレージ (プレビュー)](#tab/azurewebjobsstorage)

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

---

#### <a name="common-properties-for-identity-based-connections"></a>ID ベース接続に共通のプロパティ

Azure サービスの ID ベース接続では、次の共通プロパティが受け入れられます。ここで `<CONNECTION_NAME_PREFIX>` は、トリガーまたはバインディング定義内の `connection` プロパティの値です。

| プロパティ    |  環境変数テンプレート | 説明 |
|---|---|---|---|
| トークン資格情報 |  `<CONNECTION_NAME_PREFIX>__credential` | 接続のためにトークンを取得する方法を定義します。 "managedidentity" に設定する必要がある場合の、ユーザー割り当て ID の指定時にのみ推奨されます。 これは Azure Functions サービスでホストされるときにのみ有効です。 |
| クライアント ID | `<CONNECTION_NAME_PREFIX>__clientId` | `credential` が "managedidentity" に設定されると、このプロパティによって、トークンの取得時に使用されるユーザー割り当て ID が指定されます。 このプロパティは、アプリケーションに割り当てられたユーザー割り当て ID に相当するクライアント ID を受け取ります。 指定されていない場合、システム割り当て ID が使用されます。 このプロパティは、`credential` を設定しないとき、[ローカル開発シナリオ](#local-development-with-identity-based-connections)で異なる方法で使用されます。 |

特定の接続の種類に対して、追加のオプションがサポートされている場合があります。 接続を確立するコンポーネントのドキュメントを参照してください。

##### <a name="local-development-with-identity-based-connections"></a>ID ベースの接続によるローカル開発

> [!NOTE]
> IDベースの接続を使用したローカル開発には、 [Azure Functions Core Tools](./functions-run-local.md)の更新バージョンが必要です。 `func -v` コマンドを入力することによって、現在インストールされているバージョンを確認できます。 Functions v3 の場合は、バージョン以降を使用 `3.0.3904` します。 Functions v4 の場合は、バージョン以降を使用 `4.0.3904` します。 

ローカルで実行している場合、上記の構成によって、ローカルの開発者 ID を使用するようにランタイムに指示します。 接続では次の場所から順番にトークンを取得しようとします。

- Microsoft アプリケーション間で共有されるローカル キャッシュ
- Visual Studio の現在のユーザー コンテキスト
- Visual Studio Code の現在のユーザー コンテキスト
- Azure CLI の現在のユーザー コンテキスト

これらのオプションのいずれも成功しなかった場合は、エラーが発生します。

これには開発者 ID が使用されているため、開発リソースに対して既にいくつかのロールがある可能性はありますが、データ アクセスが提供されない場合があります。 [所有者](../role-based-access-control/built-in-roles.md#owner)のような管理ロールでは十分ではありません。 各コンポーネントの接続に必要なアクセス許可を再確認し、それらが自分に割り当てられていることを確認してください。

場合によっては、別の ID の使用を指定したい場合があります。 Azure Active Directory サービス プリンシパルのクライアント ID とクライアント シークレットに基づいて、代替 ID をポイントする接続の構成プロパティを追加できます。 **Azure Functions サービスでホストされている場合、この構成オプションはサポートされません。** ローカル コンピューターで ID とシークレットを使用するには、次の追加のプロパティを指定して接続を定義します。

| プロパティ    | 環境変数テンプレート | 説明 |
|---|---|---|
| テナント ID | `<CONNECTION_NAME_PREFIX>__tenantId` | Azure Active Directory のテナント (ディレクトリ) ID。 |
| クライアント ID | `<CONNECTION_NAME_PREFIX>__clientId` |  テナント内のアプリの登録のクライアント (アプリケーション) ID。 |
| クライアント シークレット | `<CONNECTION_NAME_PREFIX>__clientSecret` | アプリの登録で生成されたクライアント シークレット。 |

Azure BLOB への ID ベース接続に必要な `local.settings.json` プロパティの例を以下に示します。 

```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__blobServiceUri": "<blobServiceUri>",
    "<CONNECTION_NAME_PREFIX>__queueServiceUri": "<queueServiceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="connecting-to-host-storage-with-an-identity-preview"></a>ID を使用するホスト ストレージへの接続 (プレビュー)

Azure Functions では、タイマー トリガーのシングルトン実行の調整や既定のアプリ キー ストレージなどのコア動作に "AzureWebJobsStorage" 接続が既定で使用されます。 これは、ID を利用するように構成することもできます。

> [!CAUTION]
> Functions の他のコンポーネントは、既定の動作では "AzureWebJobsStorage" に依存します。 この種の接続をサポートしていない古いバージョンの拡張機能 (Azure BLOB および Event Hubs のトリガーとバインディングを含む) を使用している場合は、それを ID ベースの接続に移動させないでください。 同様に、 `AzureWebJobsStorage` は、Linux でのサーバー側ビルドの使用時に配置アーティファクトに使用されます。これを有効にする場合は、 [外部デプロイパッケージ](/run-functions-from-deployment-package)を使用してデプロイする必要があります。
>
> また、一部のアプリでは、トリガー、バインディング、関数コード内の他のストレージ接続に "AzureWebJobsStorage" が再利用されます。 接続文字列からこの接続を変更する前に、"AzureWebJobsStorage" のすべての用途で ID ベースの接続形式を使用できるようにしてください。

"AzureWebJobsStorage" に ID ベース接続を使用するには、次のアプリ設定を構成します。

| 設定                       | 説明                                | 値の例                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__blobServiceUri`| HTTPS スキームを使用する、ストレージ アカウントの BLOB サービスのデータ プレーン URI。 | https://<storage_account_name>.blob.core.windows.net |
| `AzureWebJobsStorage__queueServiceUri` | HTTPS スキームを使用する、ストレージ アカウントのキュー サービスのデータ プレーン URI。 | https://<storage_account_name>.queue.core.windows.net |

同様に [ID ベース接続に共通のプロパティ](#common-properties-for-identity-based-connections)を設定することもできます。

グローバル Azure の既定の DNS サフィックスとサービス名を使用するストレージ アカウントを使う場合は、`https://<accountName>.blob/queue/file/table.core.windows.net` 形式に従って、代わりにストレージ アカウントの名前に `AzureWebJobsStorage__accountName` を設定できます。 このアカウントの BLOB およびキュー エンドポイントが推定されます。 ストレージ アカウントがソブリン クラウドにある場合、またはカスタム DNS がある場合、これは機能しません。

| 設定                       | 説明                                | 値の例                                        |
|-----------------------------------------------------|--------------------------------------------|------------------------------------------------|
| `AzureWebJobsStorage__accountName` | ストレージ アカウントのアカウント名。アカウントがソブリン クラウドに存在せず、カスタム DNS が与えられていない場合にのみ有効。 | <storage_account_name> |

[!INCLUDE [functions-azurewebjobsstorage-permissions](../../includes/functions-azurewebjobsstorage-permissions.md)]

## <a name="reporting-issues"></a>問題の報告
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)
* [Azure Functions をローカルでコーディングしてテストする](./functions-develop-local.md)
* [Azure Functions のベスト プラクティス](functions-best-practices.md)
* [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)
* [Azure Functions Node.js 開発者向けリファレンス](functions-reference-node.md)
