---
title: Media Services v3 のサンプル
description: この記事では、Media Services v3 向けに提供されているすべてのサンプルを、手法と SDK 別に整理して掲載しています。  サンプルには、.NET、Node.JS、Python、Java のほか、Postman を使用した REST が含まれています。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1d827d734c434204ff6b7ec60d27e507ae626abd
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227689"
---
# <a name="media-services-v3-samples"></a>Media Services v3 のサンプル

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Media Services 向けに提供されているすべてのサンプルを、手法と SDK 別に整理して掲載しています。  サンプルには、.NET、Node.JS、Python、Java のほか、Postman を使用した REST が含まれています。

## <a name="rest-postman-collection"></a>REST Postman コレクション

[REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) サンプルには、Postman クライアントにインポートするための Postman コレクションと Postman 環境が含まれています。

## <a name="samples-by-sdk"></a>SDK 別サンプル

それぞれのタブに、目的のサンプルの説明とリンクが記載されています。

## <a name="net"></a>[.NET](#tab/net/)

| Folder | 説明 |
|-------------|-------------|
| [VideoEncoding/EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESPredefinedPreset)|組み込みのプリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法。|
| [VideoEncoding/EncodingWithMESCustomPreset_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264)|カスタム H.264 エンコード プリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法。|
| [VideoEncoding/EncodingWithMESCustomPreset_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_HEVC)|カスタム HEVC エンコード プリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法。|
| [VideoEncoding/EncodingWithMESCustomStitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomStitchTwoAssets)|JobInputSequence を使用したジョブの送信、開始時間と終了時間でクリップできる複数のアセットの合成を行う方法。 最終的にエンコードされたファイルは、すべてのアセットが合成された単一の動画になります。  また、このサンプルでは、ストリーミング用に出力アセットを発行し、検証用に結果をダウンロードします。|
| [VideoEncoding/EncodingWithMESCustomPresetAndSprite](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPresetAndSprite)|カスタム プリセットとサムネイル スプライト、および HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法。|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|最長 25 時間のフル アーカイブと 5 分の DVR ウィンドウを備えたアセット フィルターとを使用して LiveEvent を作成する方法。 フィルターを使用してストリーミング用のロケーターを作成する方法。|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|ビデオ アナライザーの変換の作成、入力アセットへのビデオ ファイルのアップロード、変換を使用したジョブの送信、検証用の結果のダウンロードを行う方法。|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|オーディオ アナライザーの変換の作成、入力アセットへのメディア ファイルのアップロード、変換を使用したジョブの送信、検証用の結果のダウンロードを行う方法。|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Azure Media Player での再生に使用する URL の出力を行う方法。 プレーヤーによってストリームが要求されると、Media Services は、指定されたキーを使用して、AES-128 によってコンテンツを動的に暗号化します。Azure Media Player は、トークンを使用して暗号化を解除します。|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、Widevine 構成と秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Widevine Player での再生に使用する URL の出力を行う方法。 Widevine によって保護されたコンテンツをユーザーが要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 プレーヤー アプリケーションが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 Widevine ライセンスには、クライアント プレーヤーがコンテンツの復号化とストリーミングに使用できる復号化キーが含まれています。|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、PlayReady 構成と秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Azure Media Player での再生に使用する URL の出力を行う方法。 PlayReady によって保護されたコンテンツをユーザーが要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 プレーヤー アプリケーションが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 PlayReady ライセンスには、クライアント プレーヤーがコンテンツの暗号化解除およびストリーミングを行うときに使用できる暗号化解除キーが含まれています。|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|ライセンス サービスにライセンスを要求することなく、PlayReady と Widevine DRM を使用してコンテンツを動的に暗号化し、再生する方法。 組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、オープン制限と PlayReady または Widevine の永続的な構成を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、再生用の URL の出力を行う方法を紹介します。|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、アセットフィルターとアカウントフィルターの作成、ストリーミング ロケーターへのフィルターの関連付け、再生用の URL の出力を行う方法。|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、HLS および DASH ストリーミング用の出力アセットの発行を行う方法。|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | オンデマンドのエンコードまたは分析を使用する運用システム向けのガイダンスとベスト プラクティス。 [Media Services と VOD を使用した高可用性](https://docs.microsoft.com/azure/media-services/latest/architecture-high-availability-encoding-concept)に関する記事を先にお読みください。 [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) サンプル用に別個のソリューション ファイルが用意されています。 |

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|Folder|説明|
|---|---|
|[HelloWorld-ListAssets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/HelloWorld-ListAssets) |アセットに接続してそれらを一覧表示する方法 |
|[ライブ](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live)| 基本的なライブ ストリーミングを実行する方法。 **警告**: ライブを使用する場合は、すべてのリソースがクリーンアップされていて、ポータルで課金されなくなっていることを確認してください。|
|[StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample)| ソース URL からローカル ファイルまたはエンコードをアップロードする方法、ストレージ SDK を使用してコンテンツをダウンロードする方法、プレーヤーにストリーム配信する方法。 |
|[StreamFilesWithDRMSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample)| Widevine と PlayReady DRM を使用したエンコードとストリーミングの方法 |
|[VideoIndexerSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample)| ビデオおよびオーディオ アナライザーのプリセットを使用して、ビデオまたはオーディオ ファイルからメタデータと分析情報を生成する方法。 |

## <a name="python"></a>[Python](#tab/python)

現在存在する Python サンプルは 1 つです ([Python を使用した Basic Encoding](https://github.com/Azure-Samples/media-services-v3-python))。

## <a name="java"></a>[Java](#tab/java)

|Folder|説明|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|メディア ファイル内のオーディオを分析する方法。 |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|AES-128 を使用してコンテンツを動的に暗号化する方法。|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|PlayReady DRM を使用してコンテンツを動的に暗号化する方法。|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Widevine DRM を使用してコンテンツを動的に暗号化する方法。|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|ライセンス サービスにライセンスを要求することなく、FairPlay DRM を使用してコンテンツを動的に暗号化し、コンテンツを再生する方法。|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|ライセンス サービスにライセンスを要求することなく、PlayReady と Widevine DRM を使用してコンテンツを動的に暗号化し、再生する方法。|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|アセット フィルターとアカウント フィルターを使用してコンテンツをフィルター処理する方法。|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|ストリーミング用の HLS または DASH に VOD コンテンツを動的にパッケージする方法。|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|最長 25 時間のフル アーカイブと 5 分の DVR ウィンドウを備えたアセット フィルターとを使用してライブ イベントを作成する方法、およびストリーミング用のロケーターを作成し、フィルターを使用する方法。|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|最長 25 時間のフル アーカイブと 5 分の DVR ウィンドウを備えたアセット フィルターとを使用してライブ イベントを作成する方法、およびストリーミング用のロケーターを作成し、フィルターを使用する方法。|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|StandardEncoderPreset 設定を使用してカスタム エンコード Transform を作成する方法。|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|組み込みのプリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法。|

---
