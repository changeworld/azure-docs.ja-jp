---
title: HTTP 拡張プロトコル - Azure
description: Azure Video Analyzer を使用すると、パイプライン拡張ノードを通じて処理機能を強化できます。 HTTP 拡張プロセッサでは、HTTP プロトコルを使用して拡張性シナリオを実現します。この場合、パフォーマンスや最適なリソース使用率が主要な懸念事項ではありません。
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 217848284743b0bcf9e5f805d04b0deb43a587f4
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563190"
---
# <a name="use-the-http-extension-protocol"></a>HTTP 拡張プロトコルの使用 

[!INCLUDE [header](includes/edge-env.md)]

Azure Video Analyzer を使用すると、[パイプライン拡張](../pipeline-extension.md)ノードを通じて処理機能を強化できます。 HTTP 拡張プロセッサ ノードでは、HTTP 拡張プロトコルを使用して拡張性シナリオを実現します。この場合、パフォーマンスや最適なリソース使用率が主要な懸念事項ではありません。 この記事では、このプロトコルを使用して Video Analyzer と HTTP REST エンドポイントの間でメッセージを送信する方法について説明します。これは通常、AI 推論サーバーにラップされます。

HTTP コントラクトは、次の 2 つのコンポーネント間で定義されます。

* HTTP サーバー
* Video Analyzer モジュールは HTTP クライアントとして機能します。

## <a name="http-contract"></a>HTTP 契約

### <a name="request"></a>Request

Video Analyzer モジュールから HTTP サーバーへの要求は次のようになります。

|キー|値|
|---|---|
|POST|https://hostname/optional-path?optional-query|
|Accept|application/json|
|承認| Basic、Digest、ベアラー (カスタム ヘッダーのサポートを通じて)|
|Content-Type|  image/jpeg<br/>image/png<br/>image/bmp<br/>image/x-raw|
|Content-Length 本文の長さ (バイト単位)   ||
|User-Agent |Azure Media Services|
|Body|  サポートされているコンテンツ タイプの 1 つでバイナリ エンコードされたイメージ バイト。|
```

### Example

```html
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

### <a name="response"></a>Response

推論サーバーから Video Analyzer モジュールへの応答は次のようになります。

|キー|   値|
|---|----|
|状態コード|  200 OK - 推論の結果が見つかりました<br/>204 No Content - AI によって結果が見つかりませんでした<br/>400 Bad Request - 想定されていません<br/>500 Internal Server Error - 想定されていません<br/>503 Server Busy - Video Analyzer は、"Retry-After" ヘッダーに基づいて、またはヘッダーが存在しない場合は既定の時間に基づいてバックオフされます。|
|Content-Type   |application/json|
|Content-Length |本文の長さ (バイト単位)|
|Body|  単一の "inferences" プロパティを備えた JSON オブジェクト。|

### <a name="example"></a>例

```html
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2021 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

[推論メタデータ スキーマ オブジェクト モデル](inference-metadata-schema.md)に従って定義した事前に定義されたスキーマに従って、有効な JSON ドキュメントを使用して応答を返すことをお勧めします。 スキーマに準拠することで、Video Analyzer の他のコンポーネントとの相互運用性が確保されます。Video Analyzer では、[ここに](record-stream-inference-data-with-video.md)示すように、ライブ ビデオ内のオブジェクトを追跡し、再生中にビデオに推論メタデータをオーバーレイする機能が必要になります。

モジュールによってコンテンツ タイプが "application/json" でない応答が返される場合、Video Analyzer では、メッセージが base 64 コンテンツとしてエンコードされ、非透過の JSON ペイロードとしてシリアル化されます。

モジュールによってコンテンツ タイプが "application/json" の応答が返されたものの、JSON スキーマが上記の推定メタデータ スキーマに従っていない場合、メッセージ ペイロードはパイプラインを介して転送されますが、相互運用性は低下します。

> [!NOTE]
> 推論サーバーにより任意の画像についての結果を生成されない場合、応答本文が空の HTTP 204 状態コード (No Content) が返されます。 Video Analyzer はこれを空の結果として認識し、パイプライン全体でイベントを転送しません。

## <a name="next-steps"></a>次の手順

[gRPC 拡張プロトコルについて読む](grpc-extension-protocol.md)
