---
title: パイプライン拡張機能 - Azure Video Analyzer
description: Azure Video Analyzer を使用すると、パイプライン拡張ノードを通じてパイプライン処理機能を拡張できます。 この記事では、パイプライン拡張ノードについて説明します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7641524fb712d9564fca46ef40e8949f4dd9bad1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851586"
---
# <a name="pipeline-extension"></a>パイプライン拡張機能

Azure Video Analyzer を使用すると、パイプライン拡張ノードを通じてパイプライン処理機能を拡張できます。 分析拡張機能プラグインでは、従来のイメージ処理手法またはコンピューター ビジョンの AI モデルを利用できます。 パイプライン拡張機能を有効にするには、拡張プロセッサ ノードをパイプライン フローに含めます。 拡張プロセッサ ノードは、構成済みのエンドポイントにビデオ フレームを中継し、拡張機能へのインターフェイスとして機能します。 ローカルまたはリモートのエンドポイントに接続でき、必要に応じて認証と TLS 暗号化によって保護することができます。 また、パイプライン拡張プロセッサ ノードでは、ビデオ フレームをカスタム拡張機能に送信する前に、オプションでそれらのスケーリングとエンコードを実行できます。

Video Analyzer では、次のパイプライン拡張プロセッサがサポートされています。

* [HTTP 拡張プロセッサ](pipeline.md#http-extension-processor) 
* [gRPC 拡張プロセッサ](pipeline.md#grpc-extension-processor)
* [Cognitive Services 拡張プロセッサ](pipeline.md#cognitive-services-extension-processor)

パイプライン拡張ノードが結果を JSON 形式で返すためには、分析拡張機能プラグインが必要となります。 理想的には、結果が[推論メタデータ スキーマ オブジェクト モデル](inference-metadata-schema.md)に準拠している必要があります。

[!INCLUDE [available-features](./includes/available-features.md)]

## <a name="http-extension-processor"></a>HTTP 拡張プロセッサ

HTTP 拡張プロセッサでは、[HTTP プロトコル](http-extension-protocol.md)を使用して拡張性シナリオを実現します。この場合、パフォーマンスや最適なリソース使用率が主要な懸念事項ではありません。 HTTP REST エンドポイントを使用して独自の AI をパイプラインに公開できます。

HTTP 拡張プロセッサ ノードは、次の場合に使用します。

* 既存の HTTP 推論システムとの相互運用性を向上させたい。
* パフォーマンスの低いデータ転送が許容される。
* Video Analyzer に対して単純な要求 - 応答インターフェイスを使用したい。

## <a name="grpc-extension-processor"></a>gRPC 拡張プロセッサ

gRPC 拡張プロセッサでは、gRPC ベースの高性能な[構造化プロトコル](grpc-extension-protocol.md)を使用して拡張シナリオを実現します。 これは、パフォーマンスや最適なリソース使用率が優先されるシナリオに最適です。 gRPC 拡張プロセッサを使用すると、構造化データ定義のメリットを最大限に活用できます。 gRPC は、以下を使用して、コンテンツ転送の高いパフォーマンスを実現します。

* [未処理の共有メモリ](https://en.wikipedia.org/wiki/Shared_memory)
* gRPC メッセージの本文へのコンテンツの直接埋め込み。

gRPC 拡張プロセッサは、推論メッセージの交換と共にプロパティの送信を行うために使用できます。 したがって、gRPC 拡張プロセッサ ノードは、次の場合に使用します。

* 構造化されたコントラクトを使用したい (たとえば、要求と応答の構造化されたメッセージ)。
* [プロトコル バッファー (protobuf)](https://developers.google.com/protocol-buffers) を、通信の基礎となるメッセージ交換形式として使用したい。
* 受信要求を解析して適切な実装関数を呼び出すためにカスタム要求ハンドラーを必要とする従来の要求 - 応答モデルではなく、単一ストリーム セッションで gRPC サーバーと通信したい。
* Video Analyzer と対象のモジュール間で、低遅延かつ高スループットの通信を必要とする。

## <a name="cognitive-services-extension-processor"></a>Cognitive Services 拡張プロセッサ

Cognitive Services 拡張プロセッサ (Microsoft によって構築およびサポートされている AI) は、Video Analyzer が gRPC ベースの高パフォーマンスの[構造化プロトコル](grpc-extension-protocol.md)を使用して [Computer Vision 空間分析](../../cognitive-services/computer-vision/overview.md)機能とうまく連携できるようにするカスタムビルドの拡張プロセッサです。 

Cognitive Services 拡張プロセッサ ノードは、次の場合に使用します。

* 既存の[空間分析操作](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md)との相互運用性を向上させたい。
* **Microsoft によって構築およびサポートされている AI** の gRPC プロトコル、精度、パフォーマンスのすべての利点を利用したい。
* 低遅延かつ高スループットで複数のカメラ フィードを分析する。

## <a name="use-your-inferencing-model"></a>推論モデルを使用する

パイプライン拡張機能を使用すると、ONNX、TensorFlow、PyTorch などの使用可能な任意の推論ランタイム、または独自の Docker コンテナーで、任意の推定モデルを実行できます。 推論カスタム拡張機能は、最適なパフォーマンスを得るために Video Analyzer の Edge モジュールと共にデプロイする必要があります。その後、パイプライン トポロジに含まれる HTTP 拡張プロセッサ、gRPC 拡張プロセッサ、または Cognitive Services 拡張プロセッサを使用して呼び出されます。 さらに、カスタム拡張機能に対する呼び出しの頻度を調整するには、必要に応じて、[モーション検出プロセッサ](pipeline.md#motion-detection-processor)をパイプライン拡張プロセッサのアップストリームに追加します。

次の図は、高レベルのデータ フローを示しています。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline-extension/ai-inference-results.svg" alt-text="推論モデル":::
 
## <a name="samples"></a>サンプル

あらかじめ構築された拡張サービスを使用し、低フレーム レート ([HTTP 拡張プロセッサ](pipeline.md#http-extension-processor)) または高フレーム レート ([gRPC 拡張プロセッサ](pipeline.md#grpc-extension-processor)) で Video Analyzer の操作を行う例を紹介した、いずれかのクイックスタートを体験してみましょう。

## <a name="next-steps"></a>次の手順 

概念: [ビデオ記録](video-recording.md)
