---
title: Azure Functions で接続を管理する方法
description: 静的接続クライアントを使用して、Azure Functions のパフォーマンスの問題を回避する方法について説明します。
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659325"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Azure Functions で接続を管理する方法

関数アプリ内の Functions はリソースを共有します。それらの共有リソースの中には、HTTP 接続、データベース接続、ストレージなどの Azure サービスへの接続があります。 多くの関数が同時に実行されている場合、利用可能な接続がなくなる可能性があります。 この記事では、実際に必要な接続よりも多くの接続を使用しないように関数をコーディングする方法について説明します。

## <a name="connections-limit"></a>接続の制限

利用できる接続の数が制限される理由の一部は、関数アプリが [Azure App Service サンドボックス](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)内で実行されるためです。 サンドボックスがコードに課す制限の 1 つは、[接続数の上限 (現在は 300)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) です。 この制限に達すると、関数ランタイムは `Host thresholds exceeded: Connections` というメッセージでログを作成します。

[スケール コントローラーが関数アプリ インスタンス](functions-scale.md#how-the-consumption-plan-works)を追加すると、制限を超える可能性が高くなります。 各関数アプリ インスタンスは一度に何度も関数を呼び出すことができます。このようなすべての関数は 300 の制限まで接続を使用します。

## <a name="use-static-clients"></a>静的クライアントを使用する

必要以上に多くの接続を保持しないようにするには、関数の呼び出しごとに新しいインスタンスを作成するのではなく、クライアント インスタンスを再利用します。 `HttpClient`、`DocumentClient`、Azure Storage クライアントのような .NET クライアントは、単一の静的クライアントを使用する場合に接続を管理できます。

Azure Functions アプリケーションでサービス固有のクライアントを使用する場合のガイドラインを次に示します。

- 関数の呼び出しごとに新しいクライアントを**作成しない**。
- 関数の呼び出しごとに使用できる単一の静的クライアントを**作成する**。
- 異なる関数が同じサービスを使用している場合、共有ヘルパー クラスで単一の静的クライアントを**作成することを検討する**。

## <a name="httpclient-code-example"></a>HttpClient コードの例

静的 `HttpClient` を作成する関数コードの例を次に示します。

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

.NET の `HttpClient` については、"クライアントを破棄する方がよいですか" という質問がよく寄せられます。 一般的に、`IDisposable` を実装したオブジェクトは、使用の終了後に破棄します。 ただし、関数の終了時に静的クライアントの使用は終了しないため、静的クライアントは破棄しません。 アプリケーションの起動中は、静的クライアントを存続することができます。

## <a name="documentclient-code-example"></a>DocumentClient のコード例

`DocumentClient` は Cosmos DB インスタンスに接続します。 Cosmos DB のドキュメントでは、[アプリケーションの有効期間中はシングルトン Azure Cosmos DB クライアントを使用する](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage)ことを勧めています。 次の例は、関数内でそれを行うパターンの 1 つを示しています。

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client; 

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="next-steps"></a>次の手順

静的クライアントが推奨される理由の詳細については、「[不適切なインスタンス化のアンチパターン](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)」を参照してください。

Azure Functions のパフォーマンスに関するその他のヒントについては、「[Azure Functions のパフォーマンスと信頼性を最適化する](functions-best-practices.md)」を参照してください。