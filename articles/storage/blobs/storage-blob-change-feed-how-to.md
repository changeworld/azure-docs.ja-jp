---
title: Azure Blob Storage の変更フィードを処理する (プレビュー) | Microsoft Docs
description: .NET クライアント アプリケーションで変更フィード ログを処理する方法を学習します
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111852"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob Storage の変更フィードを処理する (プレビュー)

変更フィードでは、ストレージ アカウント内の BLOB と BLOB メタデータに対して行われるすべての変更のトランザクション ログが提供されます。 この記事では、BLOB 変更フィード プロセッサ ライブラリを使用して、変更フィード レコードを読み取る方法を示します。

変更フィードの詳細については、[Azure Blob Storage の変更フィード (プレビュー)](storage-blob-change-feed.md) に関するページを参照してください。

> [!NOTE]
> 変更フィードはパブリック プレビュー段階にあり、**westcentralus** と **westus2** リージョンで利用できます。 この機能の詳細および既知の問題と制限事項については、「[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)」を参照してください。 変更フィード プロセッサ ライブラリは、このライブラリが一般公開されるまでの間に変更される可能性があります。

## <a name="get-the-blob-change-feed-processor-library"></a>BLOB 変更フィード プロセッサ ライブラリを取得する

1. Visual Studio で、NuGet パッケージ ソースに `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` URL を追加します。 

   詳細については、「[パッケージ ソース](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)」を参照してください。

2. NuGet パッケージ マネージャーで、**Microsoft.Azure.Storage.Changefeed** パッケージを見つけて、それをプロジェクトにインストールします。 

   詳細については、「[パッケージを検索してインストールする](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)」を参照してください。

## <a name="connect-to-the-storage-account"></a>ストレージ アカウントへの接続

[CloudStorageAccount.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) メソッドを呼び出して、接続文字列を解析します。 

その後、[CloudStorageAccount.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) メソッドを呼び出して、ストレージ アカウント内の Blob Storage を表すオブジェクトを作成します。

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>変更フィードを初期化する

次の using ステートメントを、コード ファイルの先頭に追加します。 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

その後、**GetContainerReference** メソッドを呼び出して、**ChangeFeed** クラスのインスタンスを作成します。 変更フィード コンテナーの名前を渡します。

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>レコードの読み取り

> [!NOTE]
> 変更フィードは、ストレージ アカウントの不変および読み取り専用のエンティティです。 任意の数のアプリケーションで、変更フィードの読み取りと処理を都合のよいときに同時に個別に行うことができます。 アプリケーションでの読み取り時に、変更フィードからレコードが削除されることはありません。 使用する各リーダーの読み取りまたは反復処理の状態は独立しており、アプリケーションによってのみ維持されます。

レコードを読み取る最もシンプルな方法は、**ChangeFeedReader** クラスのインスタンスを作成することです。 

この例では、変更フィード内のすべてのレコードを反復処理し、その後、各レコードからいくつかの値をコンソールに出力します。 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>保存位置からのレコードの読み取りの再開

変更フィードに読み取り位置を保存し、後でレコードの反復処理を再開するように選択できます。 **ChangeFeedReader.SerializeState()** メソッドを使用して、いつでも変更フィードの反復処理の状態を保存できます。 状態は**文字列**であり、アプリケーションでは、アプリケーションの設計に基づいて (たとえば、データベースやファイルに)、その状態を保存できます。

```csharp
    string currentReadState = processor.SerializeState();
```

**CreateChangeFeedReaderFromPointerAsync** メソッドを使用して **ChangeFeedReader** を作成することにより、最後の状態からレコードの反復処理を続行できます。

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>レコードのストリーム処理

変更フィード レコードを到達時に処理するように選択できます。 「[仕様](storage-blob-change-feed.md#specifications)」を参照してください。

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>時間範囲内のレコードの読み取り

変更フィードは、変更イベントの時刻に基づいて時間単位のセグメントにまとめられます。 「[仕様](storage-blob-change-feed.md#specifications)」を参照してください。 特定の時間範囲内にある変更フィード セグメントからレコードを読み取ることができます。

この例では、すべてのセグメントの開始時刻を取得します。 その後、開始時刻が、最後の使用可能なセグメントの時刻を超えるか、目的の範囲の終了時刻を超えるまで、そのリストを反復処理します。 

### <a name="selecting-segments-for-a-time-range"></a>時間範囲のセグメントの選択

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>セグメント内のレコードの読み取り

個々のセグメントまたはセグメントの範囲からレコードを読み取ることができます。

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>時刻から始まるレコードを読み取る

開始セグメントから終了まで変更フィードのレコードを読み取ることができます。 時間範囲内のレコードの読み取りと同様に、セグメントをリストし、反復処理を開始するセグメントを選択できます。

この例では、処理する最初のセグメントの [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) を取得します。

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

この例では、開始セグメントの [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) から始まる変更フィード レコードを処理します。

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> セグメントでは、1 つまたは複数の *chunkFilePath* に変更フィード ログを含めることができます。 複数の *chunkFilePath* の場合、システムでは内部的にレコードを複数のシャードにパーティション分割して、公開スループットを管理します。 セグメントの各パーティションに相互排他的な BLOB の変更が含まれており、順序付けに違反することなく個別に処理できることが保証されます。 **ChangeFeedSegmentShardReader** クラスを使用すると、ご自分のシナリオで最も効率的な場合にシャード レベルでレコードを反復処理できます。

## <a name="next-steps"></a>次のステップ

変更フィード ログについて、さらに学習します。 [Azure Blob Storage の変更フィード (プレビュー)](storage-blob-change-feed.md) に関するページを参照してください
