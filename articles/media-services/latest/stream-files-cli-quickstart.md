---
title: Azure Media Services で動画ファイルをストリーム配信する - CLI | Microsoft Docs
description: このクイック スタートの手順では、新しい Azure Media Services アカウントを作成し、ファイルをエンコードして、Azure Media Player にストリーム配信します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: Azure Media Services, ストリーム配信
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: ''
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: 8de004b0ca55cb46336a072dabb682f342c7d8dd
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56446496"
---
# <a name="quickstart-stream-video-files---cli"></a>クイック スタート:ビデオ ファイルのストリーム配信 - CLI

このクイック スタートでは、Azure Media Services を利用すると、さまざまなブラウザーおよびデバイスを対象とした動画のエンコードとストリーム配信の開始がいかに容易であるかを示します。 HTTPS URL、SAS URL、または Azure Blob Storage 内に存在するファイルへのパスを使って入力コンテンツを指定できます。
このトピックのサンプルでは、HTTPS URL を使用してアクセスできるようにするコンテンツをエンコードします。 現時点では、AMS v3 には、HTTPS URL 経由でチャンク転送エンコード処理がサポートされていません。

このクイック スタートを最後まで行うと、動画をストリーム配信できるようになります。  

![ビデオを再生する](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。

Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待ち時間やデータ エグレス コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

### <a name="create-a-resource-group"></a>リソース グループの作成

```azurecli
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Azure ストレージ アカウントの作成

この例では、General Purpose v2、Standard LRS アカウントを作成します。

ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 詳細については、[ストレージ アカウント](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)に関するページを参照してください。
 
```azurecli
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-service-account"></a>Azure Media Services アカウントの作成

```azurecli
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

次のような応答画面が表示されます。

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-streaming-endpoint"></a>ストリーミング エンドポイントを開始する

次の CLI は、既定の**ストリーミング エンドポイント**を開始します。

```azurecli
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

開始されると、次のような応答画面が表示されます。

```
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

ストリーミング エンドポイントが既に実行されている場合は、次のように表示されます。

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>アダプティブ ビットレート エンコードのための変換を作成する

**変換**を作成して、ビデオのエンコードや分析を行うための一般的なタスクを構成します。 この例では、アダプティブ ビットレート エンコードを実行します。 その後、作成した変換の下に、**ジョブ**を送信します。 ジョブは、特定の入力ビデオまたはオーディオ コンテンツに変換を適用する、Media Services への実際の要求です。

```azurecli
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

次のような応答画面が表示されます。

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>出力アセットを作成する

エンコード ジョブの出力として使用される出力**アセット**を作成します。

```azurecli
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

次のような応答画面が表示されます。

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-job-with-https-input"></a>HTTPS 入力でジョブを開始する

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、HTTPS URL をジョブの入力として指定することです (この例で示すように)。 

`az ams job start` を実行すると、ジョブの出力にラベルを設定できます。 ラベルは、後でこの出力アセットの目的を識別するために使用できます。 

- ラベルに値を割り当てる場合は、"--output-assets" を "assetname=label" に設定します。
- ラベルに値を割り当てない場合は、"--output-assets" を "assetname=" に設定します。
  `output-assets` に "=" を追加することに注意してください。 

```azurecli
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup 
```

次のような応答画面が表示されます。

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>状態の確認

5 分後、ジョブの状態を確認してください。 "Finished" になっている必要があります。 そうでない場合は、さらに数分後に確認してください。 "Finished" になったら、次の手順に進み、**ストリーミング ロケーター**を作成します。

```azurecli
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-streaming-locator-and-get-path"></a>ストリーミング ロケーターを作成し、パスを取得する

エンコードが完了したら、次の手順は、出力アセット内のビデオをクライアントが再生に使用できるようにすることです。 これを実現するには 2 つのステップがあります。最初に**ストリーミング ロケーター**を作成し、次にクライアントが使用できるストリーミング URL を作成します。

### <a name="create-a-streaming-locator"></a>ストリーミング ロケーターを作成する

```azurecli
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

次のような応答画面が表示されます。

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>ストリーミング ロケーターのパスを取得する

```azurecli
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

次のような応答画面が表示されます。

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Hls パスをコピーします。 ここでは `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)` です。

## <a name="build-url"></a>URL を構築する 

### <a name="get-streaming-endpoint-host-name"></a>ストリーミング エンドポイントのホスト名を取得する

```azurecli
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

`hostName` 値をコピーします。 ここでは `amsaccount-usw22.streaming.media.azure.net` です。

### <a name="assemble-url"></a>URL を組み立てる

"https:// " + &lt;hostName の値&gt; + &lt;Hls パスの値&gt;

#### <a name="example"></a>例

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-with-azure-media-player"></a>Azure Media Player を使用して再生をテストする

ストリーム配信をテストするため、この記事では Azure Media Player を使います。 

> [!NOTE]
> プレーヤーが HTTPS サイトでホストされている場合は、忘れずに URL を "https" に更新してください。

1. Web ブラウザーを開いて、[https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) にアクセスします。
2. **[URL:]** ボックスに、前のセクションで構築した URL を貼り付けます。 

  URL は、HLS、Dash、または Smooth 形式で貼り付けることができます。Azure Media Player によって、デバイスでの再生に適切なストリーミング プロトコルに自動的に切り替えられます。
3. **[Update Player]\(プレーヤーの更新\)** をクリックします。

Azure Media Player はテストには使用できますが、運用環境では使わないでください。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートで作成した Media Services アカウントとストレージ アカウントも含め、リソース グループ内のどのリソースも必要なくなった場合は、リソース グループを削除します。

次の CLI コマンドを実行します。

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>関連項目

[ジョブのエラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [CLI のサンプル](cli-samples.md)
