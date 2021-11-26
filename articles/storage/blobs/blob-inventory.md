---
title: Azure Storage BLOB インベントリ
description: Azure Storage インベントリは、ストレージ アカウント内のすべての BLOB データの概要を把握するのに役立つツールです。
services: storage
author: normesta
ms.service: storage
ms.date: 10/11/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 0de81ce6f7f6f042ce0e0cb933a85566af7f3875
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724161"
---
# <a name="azure-storage-blob-inventory"></a>Azure Storage BLOB インベントリ

Azure Storage BLOB インベントリ機能により、ストレージ アカウント内のご自身のコンテナー、BLOB、スナップショット、BLOB バージョンの概要が提供されます。 インベントリ レポートを使用すると、ご自身の合計データ サイズ、経過日数、暗号化の状態、不変ポリシー、訴訟ホールドなど、BLOB とコンテナーのさまざまな属性を把握できます。 このレポートには、ビジネス要件とコンプライアンス要件に関するデータの概要が示されます。

## <a name="inventory-features"></a>インベントリ機能

次の一覧では、Azure Storage BLOB インベントリの現在のリリースで使用できる機能について説明します。

- **BLOB とコンテナーのインベントリ レポート**

  BLOB とコンテナーのインベントリ レポートを生成できます。 BLOB のレポートには、ベース BLOB、スナップショット、BLOB バージョン、およびそれらに関連付けられたプロパティ (作成時刻、最終更新時刻など) を含めることができます。 コンテナーのレポートでは、コンテナーとそれに関連付けられているプロパティ (不変ポリシーの状態、法的なホールドの状態など) が記述されます。

- **カスタム スキーマ**

  レポートに表示するフィールドを選択できます。 サポートされているフィールドの一覧から選択します。 この一覧は、この記事の後半に記載されています。

- **CSV および Apache Parquet の出力形式**

  インベントリ レポートは、CSV または Apache Parquet のいずれかの出力形式で生成できます。

- **インベントリ レポートごとのマニフェスト ファイルと Azure Event Grid イベント**

  インベントリ レポートごとのマニフェスト ファイルと Azure Event Grid イベントが生成されます。 これらは、この記事の後半で説明します。

## <a name="enabling-inventory-reports"></a>インベントリ レポートの有効化

BLOB インベントリ レポートを有効にするには、1 つ以上のルールが含まれるポリシーを、ストレージ アカウントに追加します。 ガイダンスについては、「[Azure Storage BLOB のインベントリレポートを有効にする](blob-inventory-how-to.md)」をご覧ください。

## <a name="upgrading-an-inventory-policy"></a>インベントリ ポリシーをアップグレードする

2021 年 6月より前にインベントリを構成した既存の Azure Storage BLOB インベントリ ユーザーの場合、ポリシーを読み込んでポリシーを変更した後に保存し直すことで、新しい機能の使用を開始できます。 ポリシーを再読み込みすると、ポリシーの新しいフィールドに既定値が設定されます。 これらの値は、必要に応じて変更できます。 また、次の 2 つの機能が使用可能になります。

- ポリシーに対してのみサポートされるのではなく、すべての規則でターゲット コンテナーがサポートされるようになりました。

- ポリシーごとではなく、マニフェスト ファイルと Azure Event Grid イベントがルールごとに生成されるようになりました。

## <a name="inventory-policy"></a>インベントリ ポリシー

インベントリ レポートは、1 つまたは複数のルールを含むインベントリ ポリシーを追加することによって構成されます。 インベントリ ポリシーは、JSON ドキュメントに記述されたルールのコレクションです。

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

Azure portal の **[BLOB インベントリ]** セクションで **[コード ビュー]** タブを選択して JSON を表示し、 インベントリ ポリシーを確認します。

| パラメーター名 | パラメーターのタイプ | Notes | 必須 |
|--|--|--|--|
| enabled | boolean | ポリシー全体を無効にするために使用します。 **true** に設定すると、このパラメーターはルール レベルの enabled フィールドによってオーバーライドされます。 無効にすると、すべてのルールのインベントリが無効になります。 | ○ |
| rules | ルール オブジェクトの配列 | ポリシーには少なくとも 1 つのルールが必要です。 ポリシーごとに最大 100 のルールがサポートされています。 | ○ |

## <a name="inventory-rules"></a>インベントリ ルール

ルールには、インベントリ レポートを生成するためのフィルター条件と出力パラメーターが含まれます。 各ルールからインベントリ レポートが作成されます。 ルールには、重複するプレフィックスを含めることができます。 ルールの定義によっては、1 つの BLOB が複数のインベントリに出現する場合があります。

ポリシー内の各ルールには、次のいくつかのパラメーターがあります。

| パラメーター名 | パラメーターのタイプ | Notes | 必須 |
|--|--|--|--|
| name | string | ルール名には最大 256 の英数字を含めることができ、大文字と小文字は区別されます。 名前は、ポリシー内で一意である必要があります。 | ○ |
| enabled | boolean | ルールを有効または無効にするためのフラグ。 既定値は **true** です。 | ○ |
| 定義 | JSON インベントリ ルール定義 | 各定義は、ルール フィルター セットで構成されます。 | ○ |
| destination | string | すべてのインベントリ ファイルが生成される出力先コンテナー。 出力先コンテナーは既に存在している必要があります。 |

グローバルな **[BLOB インベントリが有効]** フラグの方が、ルールの *enabled* パラメーターよりも優先されます。

### <a name="rule-definition"></a>規則定義

| パラメーター名 | パラメーターのタイプ | Notes | 必須 |
|--|--|--|--|
| filters | json | フィルターは、BLOB またはコンテナーが在庫の一部であるかどうかを判断します。 | はい |
| format | string | インベントリ ファイルの出力を決定します。 有効な値は `csv` であり (CSV形式の場合)、`parquet` (Apache Parquet 形式の場合) です。| はい |
| objectType | string | これが BLOB またはコンテナーのインベントリ ルールであるかどうかを示します。 有効な値は、`blob`、`container` です。 |はい |
| schedule | string | このルールを実行するスケジュールです。 有効な値は、`daily`、`weekly` です。 | はい |
| schemaFields | JSON 配列 | インベントリの一部となるスキー マフィールドの一覧。 | はい |

### <a name="rule-filters"></a>ルール フィルター

BLOB インベントリ レポートをカスタマイズするには、いくつかのフィルターを使用できます。

| フィルター名 | フィルターの種類 | Notes | 必須 |
|--|--|--|--|
| blobTypes | 定義済みの列挙型の値の配列 | 有効な値は、階層型名前空間が有効になっているアカウントの場合は `blockBlob` と `appendBlob`、他のアカウントの場合は `blockBlob`、`appendBlob`、および `pageBlob` です。 このフィールドは、コンテナー (objectType: `container`) のインベントリには適用されません。 | はい |
| prefixMatch | プレフィックスを照合する最大 10 の文字列の配列。 | *prefixMatch* を定義していない場合、または空のプレフィックスを指定した場合、ルールはストレージ アカウント内のすべての BLOB に適用されます。 プレフィックスは、コンテナー名のプレフィックスまたはコンテナー名である必要があります。 たとえば、`container`、`container1/foo` のようになります。 | いいえ |
| includeSnapshots | boolean | インベントリにスナップショットを含めるかどうかを指定します。 既定値は `false` です。 このフィールドは、コンテナー (objectType: `container`) のインベントリには適用されません。 | いいえ |
| includeBlobVersions | boolean | インベントリに BLOB バージョンを含めるかどうかを指定します。 既定値は `false` です。 このフィールドは、コンテナー (objectType: `container`) のインベントリには適用されません。 | いいえ |

Azure portal の **[BLOB インベントリ]** セクションで **[コード ビュー]** タブを選択して JSON を表示し、 インベントリ ルールを確認します。 フィルターは、ルール定義内で指定されます。

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>BLOB インベントリでサポートされているカスタム スキーマ フィールド

- 名前 (必須)
- 作成時刻
- 更新日時
- Content-Length
- Content-MD5
- BlobType
- AccessTier
- AccessTierChangeTime
- 有効期限
- hdi_isfolder
- 所有者
- グループ
- アクセス許可
- Acl
- スナップショット (レポートにスナップショットを含めることを選択した場合に使用可能および必須)
- VersionId (レポートに BLOB バージョンを含めることを選択した場合に使用可能および必須)
- IsCurrentVersion (レポートに BLOB バージョンを含めることを選択した場合に使用可能および必須)
- メタデータ
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>コンテナー インベントリでサポートされているカスタム スキーマ フィールド

- 名前 (必須)
- 更新日時
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- メタデータ

## <a name="inventory-run"></a>インベントリの実行

BLOB インベントリの実行は、毎日自動的にスケジュールされます。 インベントリの実行が完了するまでに最大で 24 時間かかることがあります。 階層型名前空間が有効になっているアカウントの場合、実行に 2 日間かかる可能性があります。また、処理されるファイルの数によっては、2 日間経過しても実行が完了しない場合があります。 実行が正常に完了しない場合、サポートに連絡する前に、その後の実行が完了するかどうかを確認します。 実行のパフォーマンスは変化する可能性があるため、実行が完了しない場合でも、その後の実行は完了する可能性があります。

インベントリ ポリシーの読み取りまたは書き込みは全体に対して行われます。 部分的な更新はサポートされません。

> [!IMPORTANT]
> ストレージ アカウントでファイアウォール規則を有効にしている場合、インベントリ要求がブロックされることがあります。 信頼できる Microsoft サービスに例外を指定することで、このような要求のブロックを解除できます。 詳細については、[ファイアウォールおよび仮想ネットワークの構成](../common/storage-network-security.md#exceptions)に関するページの「例外」セクションを参照してください。

## <a name="inventory-completed-event"></a>インベントリ完了イベント

`BlobInventoryPolicyCompleted` イベントは、インベントリの実行がルールに対して完了したときに生成されます。 このイベントは、インベントリの実行が開始前に失敗してユーザー エラーになった場合にも発生します。 たとえば、無効なポリシー、または送信先コンテナーが存在しないというエラーが発生すると、このイベントがトリガーされます。 次の JSON は、`BlobInventoryPolicyCompleted` エントリの例を示しています。

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

次の表で、`BlobInventoryPolicyCompleted` イベントのスキーマについて説明します。

|フィールド|型|説明|
|---|---|
|scheduleDateTime|string|インベントリ ポリシーがスケジュールされた時刻。|
|accountName|string|ストレージ アカウント名。|
|ruleName|string|ルール名。|
|policyRunStatus|string|インベントリの実行の状態。 指定できる値は、`Succeeded`、`PartiallySucceeded`、`Failed` です。|
|policyRunStatusMessage|string|インベントリ実行のステータス メッセージ。|
|policyRunId|string|インベントリ実行のポリシー実行 ID。|
|manifestBlobUrl|string|インベントリ実行用のマニフェスト ファイルの BLOB URL。|

## <a name="inventory-output"></a>インベントリの出力

インベントリ ルールの実行ごとに、そのルールの指定したインベントリ出力先コンテナーに一連のファイルが生成されます。 インベントリ出力は `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName` というパスの下に生成されます。それぞれ以下の内容を表します。

- *accountName* は、ご使用の Azure Blob Storage アカウント名です。
- *inventory-destination-container* は、インベントリ ルールで指定した出力先コンテナーです。
- *YYYY/MM/DD/HH-MM-SS* は、インベントリの実行が開始された時刻です。
- *ruleName* はインベントリ ルール名です。

### <a name="inventory-files"></a>インベントリ ファイル

ルールのインベントリの実行ごとに、次のファイルが生成されます。

- **インベントリ ファイル:** ルールを使用してインベントリを実行すると、CSV または Apache Parquet 形式のファイルが少なくとも 1 つ生成されます。 一致したオブジェクトの数が大きい場合は、1 つのファイルではなく複数のファイルが生成されます。 そのような各ファイルには、一致したオブジェクトとそのメタデータが含まれます。 

  > [!NOTE]
  > Apache Parquet 形式のレポートでは、次の形式で日付が表示されます。`timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC`

  CSV 形式のファイルの場合、最初の行は常にスキーマ行になります。 次の図は、Microsoft Excel で開いたインベントリ CSV ファイルを示しています。

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Microsoft Excel で開いたインベントリ CSV ファイルのスクリーンショット":::

  > [!IMPORTANT]
  > インベントリ ファイルに表示される BLOB パスは、特定の順序で表示されないことがあります。 

- **チェックサム ファイル:** チェックサム ファイルには、manifest.json ファイルの内容の MD5 チェックサムが記載されています。 チェックサムファイルの名前は `<ruleName>-manifest.checksum` です。 チェックサム ファイルの生成は、インベントリ ルールの実行の完了を示します。

- **マニフェスト ファイル:** manifest.json ファイルには、そのルールに基づいて生成されたインベントリ ファイルの詳細が記載されています。 ファイルの名前が `<ruleName>-manifest.json` です。 ファイルには、ユーザーが指定したルール定義とそのルールのインベントリへのパスも含まれます。 次の json は、ファイルのサンプル manifest.jsの内容を示しています。

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>価格と課金

在庫の価格は、請求期間中にスキャンされる BLOB とコンテナーの数に基づいています。 例として、アカウントに100万の BLOB が含まれており、BLOB インベントリが 1 週間に 1 回実行されるように設定されているとします。 4 週間後、400 万の BLOB エントリがスキャンされます。

BLOB インベントリの課金は、2021 年 10 月 1 日から開始されます。 リージョンの料金は、その時点で公開されます。 リージョン調整をしないベースライン価格は、BLOB ストレージの場合、スキャンした 100 万エントリあたり約 0.0025 米国ドル、Data Lake Storage Gen2 を有効にした場合は 0.0035 米国ドルとなります。 インベントリ ファイルを作成した後、アカウントでインベントリによって生成されたファイルの格納、読み取り、書き込みを行うために、追加の標準データ ストレージと操作料金が発生します。

インベントリ レポートが完成した後、インベントリ レポートをストレージア カウントに格納、読み取り、書き込みするために、標準データのストレージおよび操作の追加料金が発生します。

ルールに他のルールのプレフィックスと重複するプレフィックスが含まれている場合、同じ BLOB が複数のインベントリ レポートに表示されることがあります。 この場合、両方のインスタンスに対して課金されます。 たとえば、あるルールの `prefixMatch` 要素が `["inventory-blob-1", "inventory-blob-2"]` に設定され、別のルールの `prefixMatch` 要素が `["inventory-blob-10", "inventory-blob-20"]` に設定されているとします。 `inventory-blob-200` という名前のオブジェクトが両方のインベントリ レポートに表示されます。

`includeSnapshots` と `includeVersions` のフィルターを `false` に設定していても、BLOB のスナップショットやバージョンも課金対象となります。 これらのフィルター値は課金に影響しません。 レポートに表示される内容をフィルター処理するためにのみ使用できます。

Azure Storage BLOB インベントリの価格の詳細については、[Azure Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)に関するページを参照してください。

## <a name="feature-support"></a>機能サポート

次の表は、アカウントでのこの機能のサポートと、特定の機能を有効にした場合のサポートへの影響を示しています。

| ストレージ アカウントの種類 | Blob Storage (既定のサポート) | Data Lake Storage Gen2 <sup>1</sup> | NFS 3.0 <sup>1</sup> | SFTP <sup>1</sup> |
|--|--|--|--|--|
| Standard 汎用 v2 | ![はい](../media/icons/yes-icon.png) | ![はい](../media/icons/yes-icon.png)  <sup>2</sup>              | ![はい](../media/icons/yes-icon.png) <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium ブロック BLOB | ![はい](../media/icons/yes-icon.png)| ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> | ![はい](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2、ネットワーク ファイル システム (NFS) 3.0 プロトコル、セキュア ファイル転送プロトコル (SFTP) のサポートでは、すべて階層型名前空間が有効になっているストレージ アカウントが必要です。

<sup>2</sup> 機能のサポートはプレビュー段階です。

## <a name="known-issues"></a>既知の問題

このセクションでは、Azure Storage BLOB インベントリ機能の制限事項と既知の問題について説明します。

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>階層型名前空間が有効になっているアカウントでインベントリ ジョブを完了できない

何億もの BLOB と階層型名前空間が有効になっているアカウントでは、インベントリ ジョブが 2 日以内に完了しないことがあります。 これが発生した場合、インベントリ ファイルは作成されません。 ジョブが正常に完了しない場合、サポートに連絡する前に、その後のジョブが完了するかどうかを確認してください。 ジョブのパフォーマンスは変化する可能性があるため、ジョブが完了しない場合でも、その後のジョブは完了する可能性があります。

### <a name="inventory-job-cannot-write-inventory-reports"></a>インベントリ ジョブがインベントリ レポートを書き込めない

オブジェクト レプリケーション ポリシーを使用すると、インベントリ ジョブによってインベントリ レポートが送信先コンテナーに書き込まれないようにすることができます。 他のシナリオでは、レポートを部分的に完了したときにレポートをアーカイブしたり、変更できないようにすることができます。 これにより、インベントリ ジョブの失敗が発生する可能性があります。

## <a name="next-steps"></a>次のステップ

- [Azure Storage BLOB のインベントリレポートを有効にする](blob-inventory-how-to.md)
- [コンテナーごとの BLOB の数と合計サイズを計算する](calculate-blob-count-size.md)
- [Azure Blob Storage のライフサイクルを管理する](./lifecycle-management-overview.md)
