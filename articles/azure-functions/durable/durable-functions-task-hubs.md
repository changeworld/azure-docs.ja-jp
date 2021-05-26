---
title: Durable Functions におけるタスク ハブ - Azure
description: Azure Functions の Durable Functions 拡張機能におけるタスク ハブについて説明します。 タスク ハブを構成する方法について説明します。
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 9172075ca22937a85fd7fd5827ebb40a4b58bcfa
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375760"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Durable Functions におけるタスク ハブ (Azure Functions)

[Durable Functions](durable-functions-overview.md) の *タスク ハブ* は、オーケストレーションとエンティティに使用される持続性の高いリソースの論理コンテナーです。 オーケストレーター、アクティビティ、およびエンティティの関数は、同じタスク ハブに属しているときに限り、情報を直接やり取りすることができます。

> [!NOTE]
> このドキュメントでは、既定の [Durable Functions の Azure Storage プロバイダー](durable-functions-storage-providers.md#azure-storage)に特化した形で、タスク ハブの詳細を説明しています。 Durable Functions アプリで既定以外のストレージ プロバイダーを使用している場合は、プロバイダー固有のドキュメントで詳細なタスク ハブのドキュメントを確認できます。
> 
> * [Netherite ストレージ プロバイダーのタスク ハブ情報](https://microsoft.github.io/durabletask-netherite/#/storage)
> * [Microsoft SQL (MSSQL) ストレージ プロバイダーのタスク ハブの情報](https://microsoft.github.io/durabletask-mssql/#/taskhubs)
> 
> さまざまなストレージ プロバイダーのオプションとその比較については、[Durable Functions ストレージ プロバイダー](durable-functions-storage-providers.md)に関するドキュメントを参照してください。

複数の関数アプリがストレージ アカウントを共有している場合、各関数アプリには個別のタスク ハブ名を構成する *必要があります*。 ストレージ アカウントには、複数のタスク ハブを含めることができます。 通常、この制限は他のストレージ プロバイダーにも適用されます。

次の図は、共有ストレージ アカウントと専用 Azure Storage アカウントの各関数アプリにタスク ハブが 1 つあることを示しています。

![共有ストレージ アカウントと専用ストレージ アカウントの図](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage のリソース

Azure Storage のタスク ハブは次のリソースから構成されます。

* 1 つまたは複数のコントロールキュー。
* 1 つの作業項目キュー。
* 1 つの履歴テーブル。
* 1 つのインスタンス テーブル。
* Lease Blob を少なくとも 1 つ含んだ 1 つのストレージ コンテナー。
* サイズの大きいメッセージ ペイロードを含むストレージ コンテナー (該当する場合)。

これらのリソースはすべて、オーケストレーター、エンティティ、またはアクティビティ関数の実行時 (またはスケジュール時) に、構成した Azure Storage アカウントに自動的に作成されます。 これらのリソースがどのように使用されるかについては、[パフォーマンスとスケーリング](durable-functions-perf-and-scale.md)に関する記事で説明しています。

## <a name="task-hub-names"></a>タスク ハブ名

Azure Storage のタスク ハブは、これらの規則に準拠した名前で識別されます。

* 英数字のみを含む
* 文字で始まる
* 最小長が 3 文字で、最大長が 45 文字である

次の例に示すように、タスク ハブ名は *host.json* ファイルで宣言されます。

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.json (Functions 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

次の `host.json` ファイルの例に示すように、タスク ハブはアプリの設定を使用して構成することもできます。

### <a name="hostjson-functions-10"></a>host.json (Functions 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (Functions 2.0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

タスク ハブ名は、`MyTaskHub` アプリ設定の値に設定されます。 次の `local.settings.json` は、`MyTaskHub` 設定を `samplehubname` として定義する方法を示しています。

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

タスク ハブ名は、**host.json** に加えて、[オーケストレーション クライアント バインディング](durable-functions-bindings.md#orchestration-client) メタデータでも設定できます。 これは、別の関数アプリに存在するオーケストレーションまたはエンティティにアクセスする必要がある場合に便利です。 次のコードでは、アプリ設定として構成されているタスク ハブを操作するために[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)を使用する関数の記述方法を示しています。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> 前記の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`function.json` ファイルのタスク ハブ プロパティは、アプリ設定を通じて設定されます。

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

`function.json` ファイルのタスク ハブ プロパティは、アプリ設定を通じて設定されます。

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

> [!NOTE]
> クライアント バインド メタデータでタスク ハブ名を構成する必要があるのは、ある関数アプリを使用して別の関数アプリのオーケストレーションとエンティティにアクセスする場合のみです。 クライアント関数がオーケストレーションやエンティティと同じ関数アプリで定義されている場合は、バインド メタデータでタスク ハブ名を指定するのは避ける必要があります。 既定では、すべてのクライアント バインドでは、タスク ハブのメタデータは **host.json** の設定から取得されます。

Azure Storage のタスク ハブの名前の先頭文字は、アルファベットにする必要があります。また、使用できるのはアルファベットと数値だけです。 指定しない場合、次の表に示すように、既定のタスク ハブ名が使用されます。

| Durable の拡張機能のバージョン | 既定のタスク ハブ名 |
| - | - |
| 2.x | Azure にデプロイする場合、タスク ハブ名は _関数アプリ_ の名前から派生します。 Azure の外部で実行する場合、既定のタスク ハブ名は `TestHubName` です。 |
| 1.x | すべての環境で、既定のタスク ハブ名は `DurableFunctionsHub` です。 |

拡張機能のバージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

> [!NOTE]
> この名前は共有ストレージ アカウント内に複数のタスク ハブがある場合に、それぞれのタスク ハブを区別するものです。 共有ストレージ アカウントを共有する関数アプリが複数ある場合、*host.json* ファイルでタスク ハブごとに異なる名前を明示的に構成する必要があります。 このようにしないと、複数の関数アプリがメッセージをめぐって互いに競合し、その結果、オーケストレーションが `Pending` または `Running` 状態で予期せず "スタック" するなど、未定義の動作が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [オーケストレーションのバージョン管理を処理する方法の詳細](durable-functions-versioning.md)
