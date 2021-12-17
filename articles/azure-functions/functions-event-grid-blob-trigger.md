---
title: Azure Functions Event Grid BLOB トリガー
description: Event Grid BLOB トリガーを使用したセットアップとデバッグについて説明します。
author: cachai2
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: cachai
ms.openlocfilehash: 259928cbfc046930d82bc3462db6d8aad4cd6b06
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124997"
---
# <a name="azure-function-event-grid-blob-trigger"></a>Azure Functions Event Grid BLOB トリガー

この記事では、ストレージ アカウントによって発生したイベントを処理するローカル Event Grid BLOB によってトリガーされる関数をデバッグおよびデプロイする方法について説明します。

> [!NOTE]
> Event Grid BLOB トリガーはプレビュー段階です。

## <a name="prerequisites"></a>前提条件

- 既存の関数アプリを作成または使用する
- 既存のストレージ アカウントを作成または使用する
- バージョン5.0 以降の [Microsoft.Azure.WebJobs.Extensions.Storage 拡張機能](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2) がインストールされていること
- [ngrok](https://ngrok.com/) をダウンロードして、Azure でローカル関数を呼び出せるようにする

## <a name="create-a-new-function"></a>新しい関数を作成する

1. Visual Studio Code で関数アプリを開いてください。

1. **F1 キーを押して** 、新しい BLOB トリガー関数を作成します。 ストレージ アカウントに接続文字列を使うようにします。

1. Event grid BLOB トリガーの既定の URL は次のとおりです。

    # <a name="c"></a>[C#](#tab/csharp)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName={functionname}
    ```

    # <a name="python"></a>[Python](#tab/python)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    # <a name="java"></a>[Java](#tab/java)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    ---

    関数アプリの名前をメモし、トリガーの種類が BLOB トリガーであることを確認します。これは、URL で `blobs` によって示されます。 これは、後でハウツーガイドでエンドポイントを設定するときに必要になります。

1. 関数が作成されたら、Event Grid ソース パラメーターを追加します。

    # <a name="c"></a>[C#](#tab/csharp)
    **Source = BlobTriggerSource. EventGrid** を関数のパラメーターに追加します。
    
    ```csharp
    [FunctionName("BlobTriggerCSharp")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Source = BlobTriggerSource.EventGrid, Connection = "connection")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
    ```

    # <a name="python"></a>[Python](#tab/python)
    **"source": "EventGrid"** を function.json バインド データに追加します。
    
    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "name": "myblob",
          "type": "blobTrigger",
          "direction": "in",
          "path": "samples-workitems/{name}",
          "source": "EventGrid",
          "connection": "MyStorageAccountConnectionString"
        }
      ]
    }
    ```

    # <a name="java"></a>[Java](#tab/java)
    **Press F5** キーを押して、関数をビルドします。 ビルドが完了したら、**function.js** バインドデータに **"source": "eventgrid"** を追加します。
    
    ```json
    {
      "scriptFile" : "../java-1.0-SNAPSHOT.jar",
      "entryPoint" : "com.function.{MyFunctionName}.run",
      "bindings" : [ {
        "type" : "blobTrigger",
        "direction" : "in",
        "name" : "content",
        "path" : "samples-workitems/{name}",
        "dataType" : "binary",
        "source": "EventGrid",
        "connection" : "MyStorageAccountConnectionString"
       } ]
    }
    ```

    ---

1. ログを処理する行の関数にブレークポイントを設定します。

1. デバッグ セッションを開始します。

    # <a name="c"></a>[C#](#tab/csharp)
    **F5 キー** を押してデバッグ セッションを開始します。

    # <a name="python"></a>[Python](#tab/python)
    **F5 キー** を押してデバッグ セッションを開始します。

    # <a name="java"></a>[Java](#tab/java)
    新しいターミナルを開き、次の mvn コマンドを実行してデバッグ セッションを開始します。

    ```bash
    mvn azure-functions:run
    ```

    ---

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>関数をデバッグする
新しいファイルがストレージ コンテナーにアップロードされることを BLOB Trigger が認識すると、ローカル関数でブレークポイントがヒットします。

## <a name="deployment"></a>デプロイ

関数アプリを Azure にデプロイするときに、ローカル エンドポイントからデプロイ済みのアプリ エンドポイントに Webhook エンドポイントを更新します。 エンドポイントを更新するには、「[ストレージイベントの追加](#add-a-storage-event)」の手順に従い、ステップ 5. の Webhook URL に以下を使用します。 `<BLOB-EXTENSION-KEY>` は、**Function App** の左側のメニューにある **[関数アプリ]** セクションにあります。

# <a name="c"></a>[C#](#tab/csharp)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="python"></a>[Python](#tab/python)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="java"></a>[Java](#tab/java)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースをクリーンアップするには、このチュートリアルで作成したイベント グリッドのサブスクリプションを削除します。

## <a name="next-steps"></a>次のステップ

- [Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions の Event Grid トリガー](./functions-bindings-event-grid.md)