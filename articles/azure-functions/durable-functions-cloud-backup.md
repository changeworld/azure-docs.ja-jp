---
title: "Durable Functions のファンアウト/ファンイン シナリオ - Azure"
description: "Azure Functions の Durable Functions 拡張機能でファンアウト ファンイン シナリオを実装する方法を説明します。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: aa7c0738120ecda8d43669725748585e1ad5a581
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Durable Functions のファンアウト/ファンイン シナリオ - クラウド バックアップの例

"*ファンアウト/ファンイン*" は、複数の関数を同時に実行した後、その結果に対して集計を行うパターンを指します。 この記事では、[Durable Functions](durable-functions-overview.md) を使用してファンイン/ファンアウト シナリオを実装するサンプルについて説明します。 このサンプルは、アプリのサイトのコンテンツの一部またはすべてを Azure Storage にバックアップする永続関数です。

## <a name="prerequisites"></a>前提条件

* [Durable Functions のインストール](durable-functions-install.md)に関するページの指示に従って、サンプルを設定します。
* この記事では、[Hello シーケンス](durable-functions-sequence.md) サンプルのチュートリアルを既に終了していることを前提としています。

## <a name="scenario-overview"></a>シナリオの概要

このサンプルでは、関数は、指定されたディレクトリの下にあるすべてのファイルを BLOB ストレージに再帰的にアップロードします。 さらに、アップロードされたバイトの合計数をカウントします。

すべてを管理する単一の関数を記述できます。 その際に発生する最大の問題は**スケーラビリティ**です。 単一の関数は、単一の VM でのみ実行できるため、スループットは単一の VM のスループットによって制限されます。 別の問題として、**信頼性**があります。 途中でエラーが発生した場合、またはプロセス全体が 5 分以上かかる場合、バックアップは部分的に完了した状態で失敗する可能性があります。 これにより、再起動が必要になることがあります。

もっと堅牢な方法は、2 つの標準的な関数 (ファイルを列挙し、ファイル名をキューに追加する関数と、キューからファイルを読み取り、そのファイルを BLOB ストレージにアップロードする関数) を記述することです。 これにより、スループットと信頼性は向上しますが、キューのプロビジョニングと管理を行う必要があります。 さらに重要なのは、アップロードされた合計バイト数の報告などを行うと、**状態管理**と**調整**が非常に複雑になることです。

Durable Functions を使用する方法は、上記の利点を非常に少ないオーバーヘッドで実現できます。

## <a name="the-functions"></a>関数

この記事では、サンプル アプリで使用されている次の関数について説明します。

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

以下のセクションでは、Azure ポータル開発で使用される構成とコードについて説明します。 Visual Studio 開発用のコードは、この記事の最後に記載されています。

## <a name="the-cloud-backup-orchestration"></a>クラウド バックアップのオーケストレーション

`E2_BackupSiteContent` 関数は、オーケストレーター関数用の標準的な *function.json* を使用します。

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

オーケストレーター関数を実装するコードを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

このオーケストレーター関数は、基本的に次の操作を行います。

1. `rootDirectory` 値を入力パラメーターとして使用します。
2. `rootDirectory` の下のファイルの再帰リストを取得する関数を呼び出します。
3. 複数の並列関数を呼び出して、各ファイルを Azure Blob Storage にアップロードします。
4. すべてのアップロードが完了するまで待機します。
5. Azure Blob ストレージにアップロードされたバイト数の合計を返します。

`await Task.WhenAll(tasks);` 行に注目してください。 `E2_CopyFileToBlob` 関数に対するすべての呼び出しが*待機されているわけではありません*。 これは、呼び出しを同時に実行できるようにするための意図的な設定です。 このタスク配列を `Task.WhenAll` に渡すと、"*すべてのコピー操作が完了するまで*" 完了することがない 1 つのタスクが戻ります。 .NET のタスク並列ライブラリ (TPL) を知っていれば、これは新しい事柄ではありません。 違いは、これらのタスクは複数の VM で同時に実行される可能性があり、エンド ツー エンドの実行がプロセスのリサイクルに柔軟に対応することが拡張機能によって保証されることです。

`Task.WhenAll` から応答が返ることは、すべての関数呼び出しが完了し、値が戻っていることを意味します。 `E2_CopyFileToBlob` への各呼び出しがアップロードしたバイト数を返しているため、バイト数の合計を計算することは、これらの返された値をすべて合計するだけの操作です。

## <a name="helper-activity-functions"></a>ヘルパー アクティビティ関数

ヘルパー アクティビティ関数は、他のサンプルと同じように、`activityTrigger` トリガー バインドを使用する標準的な関数です。 たとえば、`E2_GetFileList` の *function.json* ファイルは次のようになります。

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

その実装を次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> このコードをオーケストレーター関数に直接配置できないことを疑問に思うかもしれません。 配置することは可能ですが、それを行うと、オーケストレーター関数の基本ルールの 1 つである、ローカル ファイル システムへのアクセスを含めて I/O 操作を行うべきではないというルールを破ることになります。

`E2_CopyFileToBlob` の *function.json* ファイルも同じように単純です。

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

実装も、非常にシンプルです。 Azure Functions のバインドの高度な機能を使用します (`Binder` パラメーターを使用します) が、このチュートリアルでは、詳細を気にする必要はありません。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

この実装は、ディスクからファイルを読み込み、同じ名前の BLOB に内容を非同期でストリーミングします。 戻り値はストレージにコピーされたバイト数であり、この数値がオーケストレーター関数によって集計の合計を計算するために使用されます。

> [!NOTE]
> これは、I/O 操作を `activityTrigger` 関数に移動させる完璧な例です。 作業を複数の VM に分散できるだけではなく、進行状況のチェックポイント処理のメリットも得ることができます。 ホスト プロセスが何らかの理由で終了した場合でも、どのアップロードが完了しているかがわかります。

## <a name="running-the-sample"></a>サンプルの実行

サンプルに含まれる HTTP によってトリガーされる関数によって、次の HTTP POST 要求を使用するオーケストレーションを開始できます。

```
POST http://{host}/orchestrators/E2_BackupSiteContent HTTP/1.1
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> 呼び出している `HttpStart` 関数は、JSON 形式のコンテンツでのみ動作します。 このため、`Content-Type: application/json` ヘッダーは必須であり、ディレクトリ パスは JSON 文字列としてエンコードされます。

これにより、`E2_BackupSiteContent` オーケストレーターがトリガーされ、文字列 `D:\home\LogFiles` がパラメーターとして渡されます。 応答は、このバックアップ操作の状態を取得するためのリンクを提供します。

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

この操作は、関数アプリ内のログ ファイルの数によっては、完了するまで数分かかる場合があります。 前の HTTP 202 応答の `Location` ヘッダー内の URL をクエリすることで、最新の状態を取得できます。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

ここでは、関数はまだ実行中です。 オーケストレーターの状態と最終更新時間に保存された入力を確認できます。 `Location` ヘッダーの値を引き続き使用して、完了するまでポーリングできます。 状態が "Completed" になると、次のような HTTP 応答値が表示されます。

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

これで、オーケストレーションが完了したこと、完了までにかかったおおよその時間を確認できます。 `output` フィールドの値から、約 450 KB のログがアップロードされたことも確認できます。

## <a name="visual-studio-sample-code"></a>Visual Studio のサンプル コード

Visual Studio プロジェクトの単一の C# ファイルとしてのオーケストレーションを次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>次のステップ

ここでは、Durable Functions の中心となるオーケストレーション機能について詳しく説明しました。 以降のサンプルでは、さらに高度な機能とシナリオについて説明します。

> [!div class="nextstepaction"]
> [ステートフル シングルトンのサンプルを実行する](durable-functions-counter.md)


