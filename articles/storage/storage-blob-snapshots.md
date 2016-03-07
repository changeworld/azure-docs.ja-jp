<properties
	pageTitle="BLOB の読み取り専用スナップショットの作成 | Microsoft Azure"
	description="BLOB のスナップショットを作成して、特定の時点での BLOB データをバックアップする方法について説明します。スナップショットの課金方法と、スナップショットを使用して容量使用料金を最小限に抑える方法を理解します。"
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/19/2016"
	ms.author="tamram"/>

# BLOB のスナップショットの作成

## 概要

スナップショットは、ある時点で作成された読み取り専用の BLOB です。スナップショットは BLOB をバックアップするのに便利です。作成したスナップショットの読み取り、コピー、削除はできますが、変更はできません。

BLOB のスナップショットには、スナップショットの取得元となるベース BLOB と同じ名前が付いており、スナップショットが取得された時間を示す **DateTime** 値が付加されています。たとえば、ページ BLOB の URI が `http://storagesample.core.blob.windows.net/mydrives/myvhd` である場合、スナップショットの URI は `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` のようになります。すべての BLOB のスナップショットは URI を共有しており、この追加された **DateTime** 値のみによって識別されます。

BLOB に対するスナップショットの数に制限はありません。スナップショットは、明示的に削除されるまで保持されます。スナップショットは、コピー元 BLOB よりも長く保持することはできません。BLOB に関連付けられたスナップショットを列挙して、現在のスナップショットを追跡できます。

BLOB のスナップショットを作成すると、BLOB のシステム プロパティが同じ値でスナップショットにコピーされます。

ベース BLOB に関連付けられているリースはスナップショットにコピーされません。スナップショットはリースを取得することはできません。

## スナップショットのコピー

BLOB やスナップショットに関連するコピー操作は次のルールに従います。

- スナップショットをベース BLOB にコピーします。スナップショットをベース BLOB に昇格することにより、BLOB を以前のバージョンに復元できます。スナップショットは元のままですが、ベース BLOB はスナップショットの書き込み可能なコピーによって上書きされます。

- スナップショットを別の名前でコピー先 BLOB にコピーします。結果として得られるコピー先 BLOB は書き込み可能な BLOB であり、スナップショットではありません。

- コピー元 BLOB をコピーしても、コピー元 BLOB のスナップショットはコピー先にコピーされません。コピー先 BLOB をコピーで上書きした場合、コピー先 BLOB に関連付けられたスナップショットは名前が変わっても元のままです。

- ブロック BLOB のスナップショットを作成する場合、BLOB のコミットされたブロック リストもスナップショットにコピーされます。コミット前のブロックはコピーされません。

## アクセス条件の指定

アクセス条件を指定することで、その条件を満たしている場合にのみスナップショットを作成できます。アクセス条件を指定するには、**AccessCondition** プロパティを使用します。指定した条件を満たしていない場合、スナップショットは作成されず、BLOB サービスはステータス コード HTTPStatusCode.PreconditionFailed を返します。

## スナップショットの削除

スナップショットを持つ BLOB は、スナップショットが削除されてからでないと、削除できません。スナップショットは個別に削除できます。また、コピー元 BLOB を削除するとき、ストレージ サービスを利用することですべてのスナップショットを削除できます。スナップショットを持つ BLOB を削除しようとすると、エラーが返されます。

## Azure Premium Storage のスナップショット
Premium Storage でスナップショットを使うには次のルールに従います。

- Premium Storage アカウントにおけるページ BLOB あたりのスナップショットの数は 100 が上限です。上限を超えると、スナップショット BLOB の操作でエラー コード 409 (**SnapshotCountExceeded**) が返されます。

- Premium Storage アカウントのページ BLOB のスナップショットは 10 分ごとに作成されます。この頻度を超えると、スナップショット BLOB の操作でエラー コード 409 (**SnaphotOperationRateExceeded**) が返されます。

- Premium Storage アカウントのページ BLOB スナップショットの読み取りは、Get Blob 経由ではサポートされていません。Premium Storage アカウントのスナップショットで Get Blob を呼び出すと、エラー コード 400 (**InvalidOperation**) が返されます。ただし、スナップショットに対する Get Blob プロパティや Get Blob メタデータのコールはサポートされています。

- スナップショットを読み込むには、Copy Blob を操作してアカウントの別のページ BLOB にスナップショットをコピーします。すでにスナップショットがある BLOB にはスナップショットをコピーできません。コピー先の BLOB にスナップショットがある場合、Copy Blob 操作でエラー コード 409 (**SnapshotsPresent**) が返されます。

## スナップショットに絶対 URI を返す

この C# コードの例は、新しいスナップショットを作成し、プライマリの場所の絶対 URI を書き込みます。

    //Create the blob service client object.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## スナップショットの課金方法について

スナップショット (BLOB の読み取り専用コピー) を作成すると、別途データ ストレージ料金がアカウントに課金される場合があります。不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

### 課金に関する重要な考慮事項

スナップショットの作成時に考慮する必要のある重要なポイントを以下に示します。

- 料金はブロックまたはページに対して一意的に発生します。そのブロックまたはページが BLOB であるかスナップショットであるかは関係ありません。BLOB に関連付けられているスナップショットについてアカウントに追加料金が発生するのは、そのベースになっている BLOB を更新したときです。いったんベース BLOB を更新すると、ベース BLOB はそのスナップショットから分化し、それぞれの BLOB またはスナップショットの中で、一意のブロックまたはページに対する料金が発生するようになります。

- ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。そのブロックに割り当てられているブロック ID とデータが、スナップショット側と同じであっても変わりません。ブロックが再度コミットされると、そのブロックはスナップショット内の対応するブロックから分化し、そのデータに対する料金が発生します。これは、ページ BLOB 内のページを、まったく同じデータで更新した場合にも当てはまります。

- **UploadFile**、**UploadText**、**UploadStream**、**UploadByteArray** のいずれかのメソッドを呼び出してブロック BLOB を置き換えると、その BLOB 内のすべてのブロックが置き換えられます。その BLOB に関連付けられているスナップショットがある場合、ベース BLOB とスナップショット内のすべてのブロックが分化し、両方の BLOB のすべてのブロックが課金対象となります。これは、ベース BLOB 内のデータとスナップショット内のデータとがまったく同一であっても変わりません。

- 2 つのブロックに格納されているデータが同一であるかどうかを判断する方法は、Azure BLOB サービスには用意されていません。アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。料金は一意のブロックに対して発生するため、スナップショットがある BLOB を更新すると、一意のブロックが増え、追加料金が発生することを考慮することが重要です。

> [AZURE.NOTE] 不要な料金を避けるためにも、スナップショットは慎重に管理してください。スナップショットは次のように管理することをお勧めします。

> - まったく同じデータで更新する場合も含め、BLOB を更新するときは必ずその BLOB に関連付けられているスナップショットを削除してから作成し直すようにします (アプリケーションの設計上、スナップショットを維持しなければならない場合を除く)。BLOB のスナップショットを削除してから作成し直すことにより、BLOB とスナップショットの分化を確実に防ぐことができます。

> - BLOB のスナップショットを維持する場合は、**UploadFile**、**UploadText**、**UploadStream**、**UploadByteArray** を呼び出して BLOB を更新することは避けてください。これらのメソッドを実行すると、BLOB 内のすべてのブロックが置き換えられます。代わりに、**PutBlock** メソッドと **PutBlockList** メソッドを使用して、最小数のブロックのみを更新するようにしてください。


### スナップショットの課金シナリオ


ブロック BLOB とそのスナップショットについて料金が発生するしくみを次のシナリオで説明します。

シナリオ 1 では、スナップショットが作成されてからベース BLOB は更新されていません。したがって、料金の発生対象は、一意のブロックである 1、2、3 のみです。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

シナリオ 2 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。更新されたのはブロック 3 です。格納されているデータと ID が同じでも、スナップショット内のブロック 3 とは異なります。このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

シナリオ 3 では、ベース BLOB は更新されていますが、スナップショットは更新されていません。ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、スナップショット側はブロック 3 のままです。このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。このため、8 つある一意のブロックすべてについての料金がアカウントに課金されます。このシナリオは、**UploadFile**、**UploadText**、**UploadFromStream**、**UploadByteArray** などの更新メソッド (これらのメソッドは BLOB の内容をすべて置き換えるため) を使用した場合に発生する可能性があります。

![Azure Storage のリソース](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=AcomDC_0224_2016-->