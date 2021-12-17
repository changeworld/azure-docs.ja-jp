---
title: BLOB リハイドレート イベントに応答して Azure 関数を実行する
titleSuffix: Azure Storage
description: .NET を使用して Azure 関数を開発し、BLOB がアーカイブ層からリハイドレートされたときに発生するイベントに応答して関数を実行するように Azure Event Grid を構成する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: beaf5e72c74e066a0fc517d12100cb1703928b71
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441516"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>BLOB リハイドレート イベントに応答して Azure 関数を実行する

アーカイブ層にある BLOB を読み取るには、最初に BLOB をホット層またはクール層にリハイドレートする必要があります。 このリハイドレート プロセスが完了するまでに数時間かかることがあります。 リハイドレート操作の状態を繰り返しポーリングするのではなく、BLOB のリハイドレート操作が完了したときにイベントを発生させるように [Azure Event Grid](../../event-grid/overview.md) を構成し、アプリケーション内でこのイベントを処理することができます。

イベントが発生すると、Event Grid によってエンドポイントを介してイベント ハンドラーにイベントが送信されます。 いくつかの Azure サービスは、[Azure Functions](../../azure-functions/functions-overview.md) などのイベント ハンドラーとして機能できます。 Azure 関数は、イベントに応答して実行できるコードのブロックです。 この操作方法では、Azure 関数を開発し、BLOB がリハイドレートされるときに発生するイベントに応答して関数を実行するように Event Grid を構成するプロセスについて説明します。

この記事では、Visual Studio から .NET を使用して Azure 関数を作成し、テストする方法を示します。 さまざまなローカル開発環境から、さまざまなプログラミング言語を使用して Azure 関数を作成することができます。 Azure Functions でサポートされている言語の詳細については、「[Azure Functions でサポートされている言語](../../azure-functions/supported-languages.md)」を参照してください。 Azure Functions の開発オプションの詳細については、「[Azure Functions をローカルでコーディングしてテストする](../../azure-functions/functions-develop-local.md)」を参照してください。

アーカイブ層からの BLOB のリハイドレートの詳細については、「[アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事では、[Visual Studio 2019](https://visualstudio.microsoft.com/vs/) を使用して .NET で Azure 関数を開発する方法を示します。 Visual Studio Community は無料でインストールできます。 [.NET を使用した Azure 開発用に Visual Studio を構成](/dotnet/azure/configure-visual-studio)していることを確認します。

Azure 関数をローカルでデバッグするには、Postman など、HTTP 要求を送信できるツールを使用する必要があります。

[Azure サブスクリプション](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)が必要です。 アカウントをまだお持ちでない場合は、始める前に[無料で作成](https://azure.microsoft.com/free/dotnet/)してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

関数アプリは、Azure 関数のコンテナーとして機能する Azure リソースです。 新規または既存の関数アプリを使用して、この記事で説明されている手順を実行できます。

Azure portal 内で新しい関数アプリを作成するには、次の手順に従います。

1. Azure portal 内で、"*関数アプリ*" を検索します。 **[関数アプリ]** アイコンを選択して、サブスクリプション内の関数アプリの一覧に移動します。
1. **[作成]** ボタンを選択して新しい関数アプリを作成します。
1. **[基本]** タブで、リソース グループを指定し、新しい関数アプリの一意の名前を指定します。
1. **[発行]** オプションが *[コード]* に設定されていることを確認します。
1. **[ランタイム スタック]** ドロップダウンから、 *[.NET]* を選択します。 .NET Core の最新バージョンを使用するように **[バージョン]** フィールドが自動的に設定されます。
1. 新しい関数アプリのリージョンを選択します。

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="Azure - [基本] タブで新しい関数アプリを作成する方法を示すスクリーンショット":::

1. **[基本]** タブの入力が完了したら、 **[ホスティング]** タブに移動します。
1. **[ホスティング]** タブで、Azure 関数を格納するストレージ アカウントを選択します。 既存のストレージ アカウントを選択するか、新規に作成できます。
1. **[オペレーティング システム]** フィールドが *[Windows]* に設定されていることを確認します。
1. **[プランの種類]** フィールドで、 *[消費量 (サーバーレス)]* を選択します。 このプランの詳細については、「[Azure Functions の従量課金プラン ホスティング](../../azure-functions/consumption-plan.md)」を参照してください。

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="Azure - [ホスティング] タブで新しい関数アプリを作成する方法を示すスクリーンショット":::

1. 次に、 **[確認および作成]** を選択して新しい関数アプリを作成します。

関数アプリの構成の詳細については、Azure Functions のドキュメントの「[お使いの関数アプリの管理](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)」を参照してください。

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>イベント グリッド トリガーを使用して Azure 関数を作成する

次に、特定のストレージ アカウントで BLOB がリハイドレートされるときに実行される Azure 関数を作成します。 C# および .NET Core を使用して Visual Studio 内で Azure 関数を作成するには、次の手順に従います。

1. Visual Studio 2019 を起動し、新しい Azure Functions プロジェクトを作成します。 詳細については、「[関数アプリ プロジェクトを作成する](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project)」に記載されている手順に従ってください。
1. **[新しい Azure Functions アプリケーションの作成]** の手順で、以下の値を選択します。
    - 既定では、Azure Functions ランタイムは **Azure Functions v3 (.NET Core)** に設定されています。 Microsoft では、このバージョンの Azure Functions ランタイムを使用することをお勧めします。
    - 可能なトリガーの一覧から、 **[イベント グリッド トリガー]** を選択します。 Azure 関数で Blob Storage イベントを処理するためにイベント グリッド トリガーが推奨される種類のトリガーである理由の詳細については、「[Event Grid イベントのイベント ハンドラーとして関数を使用する](../../event-grid/handler-functions.md)」を参照してください。
    - **[ストレージ アカウント]** 設定は、Azure 関数が格納される場所を示します。 既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成できます。
1. **[作成]** を選択して Visual Studio 内で新しいプロジェクトを作成します。
1. 次に、「[フォルダーの名前を変更する](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function)」の説明に従って、クラスと Azure 関数の名前を変更します。 シナリオに適した名前を選択します。
1. Visual Studio 内で、 **[ツール]**  |  **[NuGet パッケージ マネージャー]**  |  **[パッケージ マネージャー コンソール]** の順に選択し、コンソールから次のパッケージをインストールします。

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. Azure 関数のクラス ファイル内で、次の using ステートメントを貼り付けます。

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. クラス ファイル内で **Run** メソッドを探します。 これは、イベントが発生したときに実行されるメソッドです。 **Run** メソッドの本文に次のコードを貼り付けます。 山かっこ内のプレースホルダーをお客様独自の値に置き換えてください。

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

Azure Functions の開発の詳細については、[Azure Functions の開発に関するガイダンス](../../azure-functions/functions-reference.md)に関する記事を参照してください。

Blob Storage イベントがイベント ハンドラーに発行されたときに含まれる情報の詳細については、「[Event Grid ソースとしての Azure Blob Storage](../../event-grid/event-schema-blob-storage.md)」を参照してください。

## <a name="run-the-azure-function-locally-in-the-debugger"></a>デバッガー内で Azure 関数をローカルに実行する

Azure 関数コードをローカルでテストするには、イベントをトリガーする HTTP 要求を手動で送信する必要があります。 Postman などのツールを使用して、要求を投稿できます。

Azure 関数のクラス ファイルの先頭には、ローカル環境でのテストに使用できる URL エンドポイントがあります。 この URL を使用して要求を投稿すると、コードをデバッグできるように、ローカル環境内でイベントがトリガーされます。 URL は次の形式です。

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

このエンドポイントに送信する要求は、シミュレートされた要求です。 Azure Storage アカウントとの間でデータを送受信することはありません。

要求を作成してこのエンドポイントに送信するには、次の手順に従います。 この例では、Postman を使用して要求を送信する方法を示します。

1. Postman で、新しい要求を作成します。
1. 上に示した URL を要求 URL のフィールドに貼り付けます。このとき、関数の名前を `{functionname}` に置き換え、中かっこを削除します。 要求動詞が GET に設定されていることを確認します。

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="Postman のイベント トリガーのローカル URL を指定する方法を示すスクリーンショット":::

1. **Content-Type** ヘッダーを追加し、それを *application/json* に設定します。
1. **aeg-event-type** ヘッダーを追加し、それを *Notification* に設定します。

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="イベントをトリガーするローカル要求のヘッダー構成を示すスクリーンショット":::

1. Postman で、本文の種類を *JSON* に、形式を "*未フォーマット*" に設定して、要求本文を指定します。 次の例では、**BLOB のコピー** 要求をシミュレートします。 山かっこ内のプレースホルダーの値は、忘れずに実際の値に置き換えます。 日付と時刻または識別子の値は、シミュレートされた要求であるため、変更する必要はないことに注意してください。

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. Visual Studio で、必要なブレークポイントをコードに配置し、**F5** キーを押してデバッガーを実行します。
1. Postman で、 **[送信]** ボタンを選択して、要求をエンドポイントに送信します。

要求を送信すると、Event Grid によって Azure 関数が呼び出され、通常どおりにデバッグできます。 詳細と例については、Azure Functions のドキュメントの「[要求を手動で投稿する](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request)」を参照してください。

イベントをトリガーする要求はシミュレートされますが、イベント発生時に実行される Azure 関数により、ログ情報がストレージ アカウントの新しい BLOB に書き込まれます。 次の図に示すように、BLOB の内容を確認し、Azure portal 内で最終更新時刻を表示できます。

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="Azure portal 内のログ BLOB の内容を示すスクリーンショット":::

## <a name="publish-the-azure-function"></a>Azure 関数を発行する

Azure 関数をローカルでテストした後、次の手順では、先ほど作成した Azure 関数を Azure Function App に発行します。 ストレージ アカウント上で発生するイベントを関数エンドポイントに送信するように Event Grid を構成できるように、関数を発行する必要があります。

関数を発行するには、次の手順に従います。

1. ソリューション エクスプローラー内で Azure Functions プロジェクトを選択したままとし (または右クリックし)、 **[発行]** を選択します。
1. **[発行]** ウィンドウで **[Azure]** をターゲットとして選択し、 **[次へ]** を選択します。
1. **[Azure Function App (Windows)]** を特定のターゲットとして選択し、 **[次へ]** を選択します。
1. **[Functions instance]\(関数インスタンス\)** タブで、ドロップダウン メニューからサブスクリプションを選択し、使用可能な関数アプリの一覧で Azure Function App を探します。
1. **[Run from package file]\(パッケージ ファイルから実行します\)** チェックボックスがオンになっていることを確認します。
1. **[完了]** をクリックして関数の発行を準備します。
1. **[発行]** ページで、構成が正しいことを確認します。 Application Insights に対するサービスの依存関係が構成されていないことを示す警告が表示された場合は、このページから構成できます。
1. **[発行]** ボタンを選択して、先ほど作成した Azure 関数の Azure Function App への発行を開始します。

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="Visual Studio から Azure 関数を発行するためのページを示すスクリーンショット":::

Azure 関数のコードに変更を加えるたびに、更新された関数を Azure に発行する必要があります。

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>ストレージ アカウントからの BLOB のリハイドレート イベントをサブスクライブする

イベントに応答して実行できる Azure 関数を含む関数アプリが完成しました。 次の手順では、ストレージ アカウントからイベント サブスクリプションを作成します。 イベント サブスクリプションでは、ストレージ アカウント内の BLOB に対する操作への応答として Event Grid を通じてイベントを発行するようにストレージ アカウントを構成します。 Event Grid により、指定したイベント ハンドラー エンドポイントにイベントが送信されます。 この場合、イベント ハンドラーは、前のセクションで作成した Azure 関数です。

イベント サブスクリプションを作成するときに、イベント ハンドラーに送信されるイベントをフィルター処理できます。 アーカイブ層から BLOB をリハイドレートするときにキャプチャするイベントは、[BLOB 層の設定](/rest/api/storageservices/set-blob-tier)操作に対応する **Microsoft.Storage.BlobTierChanged** と、[BLOB のコピー](/rest/api/storageservices/copy-blob)操作に対応する **Microsoft.Storage.BlobCreated** イベントです。 シナリオに応じて、これらのイベントのうち 1 つだけを処理します。

イベント サブスクリプションを作成するには、次の手順に従います。

1. Azure portal 内で、アーカイブ層からリハイドレートする BLOB が含まれているストレージ アカウントに移動します。
1. 左側のナビゲーション ペインで、 **[イベント]** 設定を選択します。
1. **[イベント]** ページで、 **[その他のオプション]** を選択します。
1. **[イベント サブスクリプションの作成]** を選択します。
1. **[イベント サブスクリプションの作成]** ページの **[イベント サブスクリプションの詳細]** セクションで、イベント サブスクリプションの名前を指定します。
1. **[トピックの詳細]** セクションで、システム トピックの名前を指定します。 システム トピックは、Azure Storage によって発行された 1 つ以上のイベントを表します。 システム トピックの詳細については、「[Azure Event Grid でのシステム トピック](../../event-grid/system-topics.md)」を参照してください。
1. **[イベントの種類]** セクションで、 **[Blob Created]\(変更された BLOB\)** および **[Blob Tier Changed]\(変更された BLOB 層\)** イベントを選択します。 アーカイブ層から BLOB をリハイドレートする方法に応じて、この 2 つのイベントのいずれかが発生します。

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="Azure portal 内の BLOB リハイドレート イベントのイベントの種類を選択する方法を示すスクリーンショット":::

1. **[エンドポイントの詳細]** セクションで、ドロップダウン メニューから *[Azure 関数]* を選択します。
1. **[エンドポイントの選択]** を選択して、前のセクションで作成した関数を指定します。 **[Azure 関数の選択]** ダイアログで、Azure 関数のサブスクリプション、リソース グループ、関数アプリを選択します。 最後に、ドロップダウンから関数名を選択し、 **[選択の確認]** を選択します。

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="Event Grid サブスクリプションのエンドポイントとして Azure 関数を選択する方法を示すスクリーンショット":::

1. **[作成]** ボタンを選択してイベント サブスクリプションを作成し、Azure 関数イベント ハンドラーへのイベントの送信を開始します。

イベント サブスクリプションの詳細については、[Azure Event Grid の概念](../../event-grid/concepts.md#event-subscriptions) に関するページを参照してください。

## <a name="test-the-azure-function-event-handler"></a>Azure 関数のイベント ハンドラーをテストする

Azure 関数をテストするには、イベント サブスクリプションを含むストレージ アカウントでイベントをトリガーします。 以前に作成したイベント サブスクリプションでは、**Microsoft.Storage.BlobCreated** および **Microsoft.Storage.BlobTierChanged** という 2 つのイベントがフィルター処理されます。 これらのイベントのいずれかが発生すると、Azure 関数がトリガーされます。

この記事に示されている Azure 関数では、次の 2 つのシナリオでログ BLOB に書き込まれます。

- イベントが **Microsoft.Storage.BlobCreated** で API 操作が **[BLOB のコピー]** の場合。
- イベントが **Microsoft.Storage.BlobTierChanged** で API 操作が **[BLOB 層の設定]** の場合。

BLOB をリハイドレートして関数をテストする方法については、次の 2 つの手順のいずれかを参照してください。

- [コピー操作を使用して BLOB をリハイドレートする](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [BLOB の階層を変更してリハイドレートする](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

リハイドレートが完了すると、ログ BLOB は、リハイドレートした BLOB と同じコンテナーに書き込まれます。 たとえば、コピー操作を使用して BLOB をリハイドレートした後、元のソース BLOB がアーカイブ層に残り、完全にリハイドレートされたコピー先 BLOB が対象のオンライン層に表示され、Azure 関数によって作成されたログ BLOB も一覧に表示されていることを Azure portal で確認できます。

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="アーカイブ層の元の BLOB、ホット層のリハイドレートされた BLOB、イベント ハンドラーなどによって書き込まれたログ BLOB を示すスクリーンショット":::

リハイドレートの優先度の設定によっては、BLOB のリハイドレートに最大 15 時間かかることに注意してください。 リハイドレートの優先度を **[高]** に設定すると、サイズが 10 GB 未満の BLOB の場合、リハイドレートが 1 時間以内に完了する可能性があります。 ただし、優先度の高いリハイドレートではコストが高くなります。 詳細については、「[アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)」をご覧ください。

> [!TIP]
> この方法の目的は、BLOB のリハイドレートのコンテキストでこれらのイベントを処理することですが、テスト目的では、イベントが直ちに発生するため、BLOB のアップロードやオンライン BLOB の層の変更 (*例*: ホットからクールなど) に応じてこれらのイベントを観察することも役立ちます。

Event Grid 内でイベントをフィルター処理する方法の詳細については、[Azure Event Grid のイベントをフィルター処理する方法](../../event-grid/how-to-filter-events.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

- [BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)。
- [アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)
- [アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)
- [Blob Storage のイベント処理](storage-blob-event-overview.md)
