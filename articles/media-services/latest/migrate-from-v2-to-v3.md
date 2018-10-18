---
title: Azure Media Services v2 から v3 への移行 | Microsoft Docs
description: この記事では、Azure Media Services v3 で導入された変更点と、2 つのバージョンの違いについて説明します。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: a17bad5beb651aaa085c626296c200a00c30647e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376364"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Media Services v2 から v3 への移行

この記事では、Azure Media Services (AMS) v3 で導入された変更点と、2 つのバージョンの違いについて説明します。

## <a name="why-should-you-move-to-v3"></a>v3 に移行する理由

### <a name="api-is-more-approachable"></a>API がわかりやすい

*  v3 は、Azure Resource Manager 上に構築された管理と操作の両方の機能を公開する統一された API サーフェスに基づいています。 Azure Resource Manager テンプレートを使って、変換、ストリーミング エンドポイント、LiveEvent などを作成して展開できます。
* Open API (別称 Swagger) 仕様ドキュメント。
* .NET、.Net Core、Node.js、Python、Java、Ruby で利用できる SDK。
* Azure CLI の統合。

### <a name="new-features"></a>新機能

* エンコードは HTTPS の取り込み (URL ベースの入力) をサポートするようになりました。
* 変換は v3 の新機能です。 変換は、構成の共有、Azure Resource Manager テンプレートの作成、特定の顧客またはテナントのエンコード設定の分離に使用されます。 
* 1 つの資産で、ダイナミック パッケージと動的暗号化の設定が異なる複数の StreamingLocator を持つことができます。
* コンテンツ保護はマルチキー機能をサポートしています。 
* LiveEvent Preview は、ダイナミック パッケージと動的暗号化をサポートしています。 そのため、プレビューだけでなく、DASH および HLS パッケージに対するコンテンツ保護が可能です。
* LiveOuput は、以前の Program エンティティよりも使用方法が簡単です。 
* エンティティに対する RBAC のサポートが追加されました。

## <a name="changes-from-v2"></a>v2 からの変更点

* Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

    v3 で作成された資産の場合、Media Services は [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) サーバー側のストレージ暗号化をサポートします。
    
* Media Services SDK は Storage SDK から分離され、使用される Storage SDK をより詳細に制御し、バージョン管理の問題を回避できるようになりました。 
* v3 では、エンコード ビット レートはすべてビット/秒単位です。 これは REST v2 Media Encoder Standard のプリセットとは異なります。 たとえば、v2 のビットレートは 128 と指定されていますが、v3 では 128,000 です。 
* v3 には AssetFiles、AccessPolicies、IngestManifests が存在しません。
* ContentKeys は、StreamingLocator のエンティティ、プロパティではなくなりました。
* Event Grid のサポートによって NotificationEndpoints を置き換えられました。
* 一部のエンティティ名が変更されました

  * JobOutput によって Task が置き換えられ、ジョブの一部になりました。
  * LiveEvent によって Channel が置き換えられました。
  * LiveOutput によって Program が置き換えられました。
  * StreamingLocator によって Locator が置き換えられました。

## <a name="code-changes"></a>コードの変更

### <a name="create-an-asset-and-upload-a-file"></a>資産の作成とファイルのアップロード 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>ジョブの送信

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>AES 暗号化を使用して資産を公開する 

#### <a name="v2"></a>v2

1. ContentKeyAuthorizationPolicyOption を作成します
2. ContentKeyAuthorizationPolicy を作成します
3. AssetDeliveryPolicy を作成します
4. 資産を作成し、コンテンツをアップロードするか、ジョブを送信して出力資産を使用します
5. AssetDeliveryPolicy を資産に関連付けます
6. ContentKey を作成します
7. ContentKey を資産にアタッチします
8. AccessPolicy を作成します
9. ロケーターを作成します

#### <a name="v3"></a>v3

1. コンテンツ キー ポリシーを作成します
2. 資産を作成します
3. コンテンツをアップロードするか、資産を JobOutput として使用します
4. StreamingLocator を作成する

## <a name="next-steps"></a>次の手順

ビデオ ファイルのエンコードおよびストリーミングを簡単に始める方法については、[ファイルのストリーム配信](stream-files-dotnet-quickstart.md)に関するページをご覧ください。 

