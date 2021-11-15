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
ms.openlocfilehash: 1f7397423eef2969e3a3e61b2201770a1e54a2ed
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438355"
---
# <a name="media-services-v3-samples"></a>Media Services v3 のサンプル

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Media Services 向けに提供されているすべてのサンプルを、手法と SDK 別に整理して掲載しています。  サンプルには、.NET、Node.js (TypeScript)、Python、Java のほかに、Postman を使用した REST が含まれています。

## <a name="samples-by-sdk"></a>SDK 別サンプル

それぞれのタブに、目的のサンプルの説明とリンクが記載されています。

## <a name="net"></a>[.NET](#tab/net/)

| サンプル | 説明 |
|-------------|-------------|
| [Account/CreateAccount](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Account/CreateAccount)|このサンプルでは、キー配信 IP 許可リスト、マネージド ID、ストレージ認証、自分の暗号化キーの持ち込みなどの高度な構成設定に加え、Media Services アカウントを作成してプライマリ ストレージ アカウントを設定する方法を示します。|
| [VideoEncoding/Encoding_PredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|このサンプルでは、組み込みのプリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法を示します。|
| [VideoEncoding/Encoding_H264_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | 制約のない H.264 コンテンツ対応エンコードの最も基本的な使用方法を示します |
| [VideoEncoding/Encoding_H264_ContentAware_Constrained](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | PresetConfigurations クラスを使用して、プリセットの出力動作を制限する方法を示します|
| [VideoEncoding/Encoding_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|このサンプルでは、カスタム H.264 エンコード プリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法を示します。|
| [VideoEncoding/Encoding_HEVC_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | コンテンツ対応エンコードを使用した、制約のない HEVC コーデックの基本的な使用方法を示します。  PresetConfigurations クラスは HEVC でもサポートされ、このサンプルに追加できます。|
| [VideoEncoding/Encoding_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|このサンプルでは、カスタム HEVC エンコード プリセットと HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法を示します。|
| [VideoEncoding/Encoding_StitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|このサンプルでは、JobInputSequence を使用したジョブの送信、開始または終了時間でクリップできる複数のアセットの合成を行う方法を示します。 最終的にエンコードされたファイルは、すべてのアセットが合成された単一の動画になります。  また、このサンプルでは、ストリーミング用に出力アセットを発行し、検証用に結果をダウンロードします。|
| [VideoEncoding/Encoding_SpriteThumbnail](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|このサンプルでは、カスタム プリセットとサムネイル スプライト、および HTTP URL 入力を使用したジョブの送信、ストリーミング用の出力アセットの発行、検証用の結果のダウンロードを行う方法を示します。|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|このサンプルでは、まず最大 25 時間のフル アーカイブと 5 分の DVR ウィンドウを備えたアセット フィルターを使用して LiveEvent を作成する方法を示します。次に、そのフィルターを使用してストリーミング用のロケーターを作成する方法を示します。|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|このサンプルでは、ビデオ アナライザーの変換の作成、入力アセットへのビデオ ファイルのアップロード、変換を使用したジョブの送信、検証用の結果のダウンロードを行う方法を示します。|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|このサンプルでは、オーディオ アナライザーの変換の作成、入力アセットへのメディア ファイルのアップロード、変換を使用したジョブの送信、検証用の結果のダウンロードを行う方法を示します。|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|このサンプルでは、組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Azure Media Player での再生に使用する URL の出力を行う方法を示します。 プレーヤーによってストリームが要求されると、Media Services は、指定されたキーを使用して、AES-128 によってコンテンツを動的に暗号化します。Azure Media Player は、トークンを使用して暗号化を解除します。|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|このサンプルでは、組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、Widevine 構成と秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Widevine Player での再生に使用する URL の出力を行う方法を示します。 Widevine によって保護されたコンテンツをユーザーが要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 プレーヤー アプリケーションが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 Widevine ライセンスには、クライアント プレーヤーがコンテンツの復号化とストリーミングに使用できる復号化キーが含まれています。|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|このサンプルでは、組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、PlayReady 構成と秘密鍵を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、トークンの取得、Azure Media Player での再生に使用する URL の出力を行う方法を示します。 PlayReady によって保護されたコンテンツをユーザーが要求すると、プレーヤー アプリケーションが Media Services ライセンス サービスにライセンスを要求します。 プレーヤー アプリケーションが承認されると、Media Services ライセンス サービスはプレーヤーにライセンスを発行します。 PlayReady ライセンスには、クライアント プレーヤーがコンテンツの暗号化解除およびストリーミングを行うときに使用できる暗号化解除キーが含まれています。|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|このサンプルでは、ライセンス サービスにライセンスを要求することなく、PlayReady と Widevine DRM を使用してコンテンツを動的に暗号化し、再生する方法を示します。 組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、オープン制限と PlayReady または Widevine の永続的な構成を使用した ContentKeyPolicy の作成、StreamingLocator への ContentKeyPolicy の関連付け、再生用の URL の出力を行う方法を紹介します。|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|このサンプルでは、組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、アセットフィルターとアカウントフィルターの作成、ストリーミング ロケーターへのフィルターの関連付け、再生用の URL の出力を行う方法を示します。|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|このサンプルでは、組み込みの AdaptiveStreaming プリセットを使用した変換の作成、ジョブの送信、HLS および DASH ストリーミング用の出力アセットの発行を行う方法を示します。|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | このサンプルでは、オンデマンドのエンコードまたは分析を使用する運用システム向けのガイダンスとベスト プラクティスを提供します。 [Media Services と VOD を使用した高可用性](architecture-high-availability-encoding-concept.md)に関する記事を先にお読みください。 [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) サンプル用に別個のソリューション ファイルが用意されています。 |
| [Media Services 用の Azure Functions](https://github.com/xpouyat/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)|このプロジェクトには、ビデオ処理のために Azure Media Services v3 に接続する Azure Functions の例が含まれています。 Visual Studio 2019 または Visual Studio Code を使用して、関数を作成して実行できます。 Azure Resource Manager (ARM) テンプレートと GitHub Actions ワークフローが、Function リソースのデプロイのために用意されており、継続的なデプロイが可能になっています。|

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|サンプル|説明|
|---|---|
|[Hello World - list assets](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/HelloWorld-ListAssets/list-assets.ts)|アセットに接続して一覧表示する方法の基本的な例 |
|[ライブ ストリーミング](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| ライブ ストリーミングの基本的な例。 **警告**: ライブを使用する場合は、すべてのリソースがクリーンアップされていて、ポータルで課金されなくなっていることを確認してください|
|[HLS と DASH をアップロードしてストリーミングする](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| ソース URL からローカル ファイルまたはエンコードをアップロードするための基本的な例。 サンプルでは、Storage SDK を使用してコンテンツをダウンロードする方法と、プレーヤーにストリーミングする方法が示されています |
|[PlayReady と Widevine DRM を使用して HLS と DASH をアップロードしてストリーミングする](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Widevine と PlayReady DRM を使用したエンコードとストリーミングの方法を示しています |
|[AI をアップロードおよび使用してビデオとオーディオにインデックスを付ける](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| ビデオおよびオーディオ アナライザーのプリセットを使用して、ビデオまたはオーディオ ファイルからメタデータと分析情報を生成する例 |

## <a name="python"></a>[Python](#tab/python)

|サンプル|説明|
|---|---|
|[Python を使用した基本的なエンコード](https://github.com/Azure-Samples/media-services-v3-python)| ソース URL からローカル ファイルまたはエンコードをアップロードするための基本的な例。 サンプルでは、Storage SDK を使用してコンテンツをダウンロードする方法と、プレーヤーにストリーミングする方法が示されています |
|[イベントと関数を使用した顔編集](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased)| これは、Azure ストレージ アカウントにアクセスするとすぐにビデオで Azure Media Services Face Redactor ジョブをトリガーするイベントベースのアプローチの例です。 このソリューションでは Azure Media Services、Azure 関数、Event Grid、Azure Storage を活用します。 ソリューションの詳細については、[README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md) を参照してください|


## <a name="java"></a>[Java](#tab/java)

|サンプル|説明|
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

## <a name="rest-postman-collection"></a>REST Postman コレクション

[REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) のサンプルには、Postman クライアントにインポートするための Postman の環境とコレクションが含まれています。 API の構造と Azure Resource Management (ARM) での動作、およびクライアント SDK からの呼び出しの構造をよく理解するために、Postman コレクションのサンプルをお勧めします。 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]
