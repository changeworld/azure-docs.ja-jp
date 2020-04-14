---
title: Azure Media Services のアセットを管理する
titleSuffix: Azure Media Services
description: アセットでは、メディアの入力 (たとえば、アップロードまたはライブ取り込みを通して)、メディアの出力 (ジョブ出力から)、およびメディアの公開 (ストリーミングの場合) を行います。 このトピックでは、新しいアセットを作成してファイルをアップロードする方法の概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582196"
---
# <a name="manage-assets"></a>アセットの管理

Azure Media Services では、[アセット](https://docs.microsoft.com/rest/api/media/assets)で以下のことを行います。 

* メディア ファイルをアセットにアップロードする
* ライブ ストリームをアセットに取り込んで、アーカイブする
* 分析ジョブのエンコードの結果をアセットに出力する
* ストリーミング用のメディアを公開する 
* アセットからファイルをダウンロードする

このトピックでは、ファイルをアセットにアップロードし、その他の一般的な操作を実行する方法の概要を説明します。 また、コード サンプルと関連トピックへのリンクも示します。

## <a name="prerequisite"></a>前提条件 

開発を開始する前に、以下を確認してください。

* [概念](concepts-overview.md)
* [Media Services v3 API を使用した開発](media-services-apis-overview.md) (API へのアクセスや名前付け規則などに関する情報を含む) 

## <a name="upload-media-files-into-an-asset"></a>メディア ファイルをアセットにアップロードする

デジタル ファイルは、ストレージにアップロードされアセットに関連付けられた後、Media Services エンコード、ストリーム配信、およびコンテンツ分析ワークフローで使用できます。 Media Services の一般的なワークフローの 1 つに、ファイルのアップロード、エンコード、ストリーム配信があります。 このセクションでは、一般的な手順について説明します。

1. Media Services v3 API を使用して、新しい "入力" アセットを作成します。 この操作により、Media Services アカウントに関連付けられているストレージ アカウントにコンテナーが作成されます。 API からコンテナー名が返されます (例: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。

    アセットに関連付ける BLOB コンテナーが既にある場合、アセットを作成するときに、そのコンテナーの名前を指定できます。 Media Services では、現在、ファイル名にパスが含まれていない、コンテナー ルートにある BLOB のみをサポートしています。 そのため、"input.mp4" というファイル名のコンテナーは使用できます。 ただし、"videos/inputs/input.mp4" というファイル名のコンテナーは使用できません。

    Azure CLI を使用して、自分のサブスクリプション内にある、権限を持っている任意のストレージ アカウントおよびコンテナーに直接アップロードできます。

    コンテナー名は一意であり、ストレージの名前付けガイドラインに従っている必要があります。 名前は Media Services アセット コンテナー名 (Asset-GUID) の形式に従う必要はありません。

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. デジタル ファイルをアセット コンテナーにアップロードするために使用する、読み取り/書き込みアクセス許可のある SAS URL を取得します。

    Media Services API を使用して、[アセット コンテナーの URL を一覧表示](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)できます。

    **AssetContainerSas.listContainerSas** が受け取る [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) パラメーターに `expiryTime` を設定します。 時間は 24 時間未満に設定してください。

    ストレージ アカウントごとに 2 つのストレージ アカウント キーがあるため、[ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) は複数の SAS URL を返します。 ストレージ アカウントには、ストレージ アカウント キーのフェールオーバーとシームレスなローテーションに役立つ 2 つのキーがあります。 最初の SAS URL は最初のストレージ アカウント キーを表し、2 番目の SAS URL は 2 番目のキーを表します。
3. Azure Storage API または SDK (たとえば、[Storage REST API](../../storage/common/storage-rest-api-auth.md)、[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) を使用して、ファイルをアセット コンテナーにアップロードします。
4. Media Services v3 API を使用して、"入力" アセットを処理する変換とジョブを作成します。 詳しくは、「[Transform と Job](transform-concept.md)」をご覧ください。
5. "出力" アセットからのコンテンツをストリーム配信します。

### <a name="create-a-new-asset"></a>新しいアセットの作成

> [!NOTE]
> アセットの Datetime 型のプロパティは、常に UTC 形式です。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST の例については、[REST を使用したアセットの作成](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)の例をご覧ください。

この例では、**要求本文**を作成する方法を示しており、説明、コンテナー名、ストレージ アカウントなどの有用な情報を指定できます。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>関連項目

* [ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)
* [HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>ライブ ストリームをアセットに取り込んで、アーカイブする

Media Services の[ライブ出力](https://docs.microsoft.com/rest/api/media/liveoutputs)オブジェクトは、ご利用のライブ ストリームをキャッチしてご利用の Media Services アカウントのアセットに記録するデジタル ビデオ レコーダーのようなものです。 記録されたコンテンツは、[アセット](https://docs.microsoft.com/rest/api/media/assets) リソースによって定義されたコンテナーに保存されます。

詳細については、次を参照してください。

* [クラウド DVR の使用](live-event-cloud-dvr.md)
* [ストリーミング ライブ チュートリアル](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>ジョブの結果をアセットに出力する

Media Services では、ご利用のビデオを処理するとき (エンコードや分析など)、自分の[ジョブ](transforms-jobs-concept.md)の結果を格納するために出力[アセット](assets-concept.md)を作成する必要があります。

詳細については、次を参照してください。

* [ビデオのエンコード](encoding-concept.md)
* [ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>ストリーミング用のアセットを公開する

ストリーミング用のアセットを公開するには、[ストリーミング ロケーター](streaming-locators-concept.md)を作成する必要があります。 公開するアセット名は、ストリーミング ロケーターによって認識される必要があります。 

詳細については、次を参照してください。

[チュートリアル:Media Services v3 を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>出力アセットからジョブの結果をダウンロードする

これで、Media Service と Storage API を使用して、自分のジョブの結果をローカル フォルダーにダウンロードできます。 

[ファイルのダウンロード](download-results-howto.md)の例を参照してください。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ライブおよびオンデマンドでアップロード、エンコード、分析、ストリーム配信を行う方法を示す完全なコード例を参照してください。 

* [Java](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.NET](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [REST](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
