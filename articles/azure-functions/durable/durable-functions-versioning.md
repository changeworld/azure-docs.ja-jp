---
title: Durable Functions でのバージョン管理 - Azure
description: Azure Functions の Durable Functions 拡張機能にバージョン管理を実装する方法を説明します。
author: cgillum
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 16de5f5950cf305115de4c2849b3ebbbc4cf2536
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507723"
---
# <a name="versioning-in-durable-functions-azure-functions"></a>Durable Functions (Azure Functions) でのバージョン管理

アプリケーションの存続期間中に関数が追加、削除、変更されることは避けられません。 [Durable Functions](durable-functions-overview.md) では、以前は不可能だった方法で関数を連結できます。この連結は、バージョン管理への対応方法に影響します。

## <a name="how-to-handle-breaking-changes"></a>重大な変更に対応する方法

注意すべき重大な変更の例がいくつかあります。 この記事では、最も一般的なものについて説明します。 それらすべての背後にある主なテーマは、関数コードの変更によって新規と既存両方の関数オーケストレーションが影響を受けることです。

### <a name="changing-activity-or-entity-function-signatures"></a>アクティビティ関数シグネチャまたはエンティティ関数シグネチャの変更

シグネチャの変更とは、関数の名前、入力、または出力の変更のことを指します。 アクティビティ関数またはエンティティ関数にこの種の変更を行うと、それに依存するオーケストレーター関数が中断される可能性があります。 この変更に対応してオーケストレーター関数を更新すると、処理中の既存のインスタンスが中断される可能性があります。

例として、次のような C# オーケストレーター関数があるとします。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

この単純な関数は、**Foo** の結果を取得して **Bar** に渡します。 広範な結果値をサポートするために、**Foo** の戻り値を、`bool` から `string` に変更する必要があるとします。 結果は次のようになります。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string result = await context.CallActivityAsync<string>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

この変更は、オーケストレーター関数の新しいインスタンスではすべて正しく機能しますが、処理中のインスタンスでは中断を発生させます。 たとえば、オーケストレーション インスタンスで `Foo` という名前の関数を呼び出し、ブール値を返し、その後チェックポイントを返す場合を考えます。 シグネチャの変更がこの時点でデプロイされると、チェックポイントが付いたインスタンスは、`Foo` の呼び出しを再開して再生すると、直ちに失敗します。 この失敗が起きるのは、履歴テーブル内の結果が `bool` であるにも関わらず、新しいコードで `string` への逆シリアル化が試行され、タイプ セーフ言語に対する実行時例外が発生するためです。

この例は、関数シグネチャの変更によって既存のインスタンスが中断するさまざまな原因の 1 つにすぎません。 一般に、オーケストレーターで関数の呼び出し方を変更する必要がある場合、その変更に問題がある可能性があります。

### <a name="changing-orchestrator-logic"></a>オーケストレーターのロジックの変更

その他のバージョン管理の問題は、処理中のインスタンスの実行パスを変更するようなオーケストレーター関数コードの変更が原因となっています。

次の C# オーケストレーター関数について考えてみます。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    await context.CallActivityAsync("Bar", result);
}
```

次に、既存の2つの関数呼び出しの間に新しい関数呼び出しを追加するための変更を行うとします。

```csharp
[FunctionName("FooBar")]
public static Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool result = await context.CallActivityAsync<bool>("Foo");
    if (result)
    {
        await context.CallActivityAsync("SendNotification");
    }

    await context.CallActivityAsync("Bar", result);
}
```

この変更では、*Foo* と *Bar* の間に *SendNotification* への新しい関数呼び出しを追加します。 シグネチャの変更はありません。 *Bar* の呼び出しから既存のインスタンスを再開すると、問題が発生します。 再生中に、元の *Foo* への呼び出しから `true` が返されると、オーケストレーターの再生で実行履歴にない *SendNotification* が呼び出されます。 *Bar* の呼び出しの実行が想定されるときに *SendNotification* の呼び出しが発生したことで、Durable Task Framework はこの不整合を検出し、`NonDeterministicOrchestrationException` をスローします。 永続的なタイマーの作成、外部イベントの待機、サブオーケストレーションの呼び出しなど、他の永続的な操作に API 呼び出しを追加するときにも、同じ種類の問題が発生する可能性があります。

## <a name="mitigation-strategies"></a>対応方法

バージョン管理の課題に対応するための方法をいくつか次に示します。

* 何も行わない (非推奨)
* 処理中のすべてのインスタンスを停止する
* 並行してデプロイする

### <a name="do-nothing"></a>何もしない

バージョン管理の単純な手法は、何もせずに、インフライト オーケストレーション インスタンスを失敗させることです。 変更の種類によっては、次の種類の障害が発生する可能性があります。

* `NonDeterministicOrchestrationException` エラーを出して、オーケストレーションが失敗する場合があります。
* `Running` 状態を報告し、オーケストレーションが無期限に停止する場合があります。
* 関数が削除された場合、その関数を呼び出そうとする関数が、エラーが発生して失敗する可能性があります。
* 関数の実行がスケジュールされた後に関数が削除されると、アプリで持続的なタスク フレームワーク エンジンで低レベルの実行時エラーが発生し、パフォーマンスが大幅に低下する可能性があります。

これらの潜在的な障害により、「何もしない」手法はお勧めできません。

### <a name="stop-all-in-flight-instances"></a>処理中のすべてのインスタンスを停止する

別の選択肢として、処理中のすべてのインスタンスを停止することもできます。 既定の [Durable Functions 用 Azure Storage プロバイダー](durable-functions-storage-providers.md#azure-storage)を使用している場合に、すべてのインスタンスを停止するには、内部にある **control-queue** および **workitem-queue** というキューのコンテンツを消去します。 または、関数アプリを停止し、これらのキューを削除してから、アプリを再起動して行うこともできます。 アプリが再起動すると、キューは自動的に再作成されます。 以前のオーケストレーション インスタンスは、"実行中" 状態のまま無期限に保持されますが、エラーメッセージでログがわかりにくくなったり、アプリに害が及ぼされることはありません。 この方法は、ローカル開発など、迅速なプロトタイプ開発に最適です。

> [!NOTE]
> この方法では、基になるストレージ リソースに直接アクセスする必要があり、Durable Functions によってサポートされるすべての記憶域プロバイダーに適しているわけではありません。

### <a name="side-by-side-deployments"></a>並行してデプロイする

重大な変更が安全にデプロイされるようにする最も失敗のない方法は、古いバージョンと並行してデプロイすることです。 これを行うには、次のいずれかの手法を使用します。

* 既存の関数のままにして、すべての更新プログラムをまったく新しい関数としてデプロイする。 これは多くの場合、新しい関数バージョンの呼び出し元を再帰的に更新する複雑さがあるため推奨されません。
* 別のストレージ アカウントを使用して、すべての更新プログラムを新しい関数アプリとしてデプロイする。
* 関数アプリの新しいコピーを、同じストレージ アカウントであるが、更新された[タスク ハブ](durable-functions-task-hubs.md)名でデプロイする。 これにより、新しいバージョンのアプリで使用できる新しいストレージ成果物が作成されます。 以前のバージョンのアプリは、以前のストレージ成果物のセットを使用して引き続き実行されます。

サイド バイ サイド デプロイは、関数アプリの新しいバージョンをデプロイする場合に推奨される手法です。

> [!NOTE]
> サイド バイ サイドデプロイ手法に関するこのガイダンスでは、Azure Storage 固有の用語を使用しますが、一般に、サポートされているすべての [Durable Functions ストレージ プロバイダー](durable-functions-storage-providers.md)に適用されます。

### <a name="deployment-slots"></a>デプロイ スロット

Azure Functions または Azure App Service でサイド バイ サイド デプロイを実行する場合は、新しいバージョンの関数アプリを新しい[デプロイメント スロット](../functions-deployment-slots.md)にデプロイすることをお勧めします。 デプロイ スロットでは、複数のコピーの関数アプリを並行して実行し、そのうちの 1 つのみをアクティブな *運用* スロットとして実行できます。 既存のインフラストラクチャに新しいオーケストレーション ロジックを公開する準備ができている場合、新しいバージョンを運用スロットにスワップするのと同じくらい簡単です。

> [!NOTE]
> この方法は、オーケストレーター関数の HTTP トリガーや Webhook トリガーを使用する場合に最適です。 キューや Event Hubs などの HTTP 以外のトリガーの場合、トリガー定義は、スワップ操作の一環として更新された[アプリ設定を派生する](../functions-bindings-expressions-patterns.md#binding-expressions---app-settings)必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [パフォーマンスとスケーリングの問題に対応する方法を知る](durable-functions-perf-and-scale.md)
