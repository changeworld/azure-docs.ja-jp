---
title: gRPC 拡張プロトコル - Azure
description: Azure Video Analyzer を使用すると、パイプライン拡張ノードを通じて処理機能を強化できます。 gRPC 拡張プロセッサを使用すると、高性能かつ構造化された gRPC ベースのプロトコルを使用した拡張シナリオが実現します。
ms.topic: reference
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: eea68be219ee44fdcc253cc03fc63ebcd7ce93c0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089889"
---
# <a name="use-the-grpc-extension-protocol"></a>gRPC 拡張プロトコルを使用する 

[!INCLUDE [header](includes/edge-env.md)]

Azure Video Analyzer を使用すると、[パイプライン拡張ノード](../pipeline-extension.md)を通じてパイプライン処理機能を強化できます。 gRPC 拡張プロセッサを使用すると、[高性能かつ構造化された gRPC ベースのプロトコル](../pipeline-extension.md#grpc-extension-processor)を使用した拡張シナリオが実現します。

この記事では、Video Analyzer モジュールとメッセージを処理して結果を返す gRPC サーバーの間でメッセージを送信する gRPC 拡張プロトコルの使用について説明します。 gRPC は、あらゆる環境で動作する最新のオープンソースの高パフォーマンス RPC フレームワークです。異なるプラットフォーム間、異なる言語間の通信がサポートされます。 gRPC トランスポート サービスでは、以下の間で HTTP/2 双方向ストリーミングを使用します。

* gRPC クライアント (Video Analyzer モジュール)
* gRPC サーバー (カスタム拡張機能)

gRPC セッションは、TCP (TLS) ポートを介した、gRPC クライアントから gRPC サーバーへの単一の接続です。
単一セッション: クライアントは、gRPC ストリーム セッションを介して、サーバーにメディア ストリーム記述子を [protobuf](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) メッセージとして送信し、その後にビデオ フレームを送信します。 サーバーはストリーム記述子を検証し、ビデオ フレームを分析して、推論結果を protobuf メッセージとして返します。

[推論メタデータ スキーマ オブジェクト モデル](inference-metadata-schema.md)に従って定義した事前確立済みのスキーマに従い、有効な JSON ドキュメントを使用して応答を返すことを強くお勧めします。 これにより、他のコンポーネントとの相互運用性、および推論メタデータを使用したビデオの記録や再生などのシナリオがより適切に実現します。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/grpc-external-srv.svg" alt-text="Azure Video Analyzer モジュール" lightbox="./media/grpc-extension-protocol/grpc-external-srv.svg":::

## <a name="implementing-grpc-protocol"></a>gRPC プロトコルの実装

### <a name="creating-a-grpc-connection"></a>gRPC 接続の作成

カスタム拡張機能では、次の gRPC サービスを実装する必要があります。

```
service MediaGraphExtension
    {
        rpc ProcessMediaStream(stream MediaStreamMessage) returns (stream MediaStreamMessage);
    }
```

これを呼び出すと、gRPC 拡張機能と Video Analyzer ライブ パイプラインの間を流れるメッセージの双方向ストリームが開きます。 このストリームで各パーティから送信される最初のメッセージには、後続の MediaSample で送信される情報を定義する MediaStreamDescriptor が含まれます。

たとえば、この拡張機能では、gRPC メッセージに埋め込まれた、416x416 rgb24 でエンコードされたフレームをカスタム拡張機能に送信することを示すメッセージ (ここでは JSON で表現) を送信できます。

```
 {
    "sequence_number": 1,
    "ack_sequence_number": 0,
    "media_stream_descriptor": 
    {
        "graph_identifier": 
        {
            "media_services_arm_id": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{video-analyzer-account-name}",
            "graph_instance_name": "{live-pipeline-name}",
            "graph_node_name": "{grpc-extension-node-name}"
        },
        "media_descriptor": 
        {
            "timescale": 90000,
            "video_frame_sample_format": 
            {
                "encoding": "RAW",
                "pixel_format": "RGB24",
                "dimensions": 
                {
                    "width": 416,
                    "height": 416
                },
                "stride_bytes": 1248
            }
        }
    }
}
```

カスタム拡張機能では、推論のみを返すことを示す次のメッセージを応答で送信します。

```
{
    "sequence_number": 1,
    "ack_sequence_number": 1,
    "media_stream_descriptor": 
    {
        "extension_identifier": "customExtensionName"    
    }
}
```

双方がメディア記述子を交換したら、Video Analyzer によって gRPC サーバーへのフレームの送信が開始されます。

> [!NOTE]
> gRPC サーバーは、任意のプログラミング言語を使用して実装できます。

### <a name="sequence-numbers"></a>シーケンス番号

gRPC 拡張機能ノードとカスタム拡張機能はどちらも、それらのメッセージに割り当てられた一連の個別のシーケンス番号を維持します。 これらのシーケンス番号は、1 から始まって単調に増加する必要があります。 メッセージが確認されていない場合、ack_sequence_number は無視されるおそれがあります。これは、最初のメッセージが送信されたときに発生する可能性があります。

対応するメッセージが完全に処理されたら、要求を確認する必要があります。 共有メモリ転送の場合、この確認は送信側が共有メモリを解放でき、受信側が共有メモリにアクセスしなくなることを示します。 送信側は、確認が完了するまで共有メモリを保持する必要があります。

### <a name="reading-embedded-content"></a>埋め込みコンテンツの読み取り

埋め込みコンテンツは、content_bytes フィールドを介して MediaSample メッセージから直接読み取ることができます。 データは MediaDescriptor に従ってエンコードされます。

### <a name="reading-content-from-shared-memory"></a>共有メモリからのコンテンツの読み取り

共有メモリを介してコンテンツを転送する場合、送信側は最初にセッションを確立するときに MediaStreamDescriptor に SharedMemoryBufferTransferProperties を含めます。 これは次のようになります (JSON で表現)。

```
{
  "handle_name": "inference_client_share_memory_2146989006636459346"
  "length_bytes": 20971520
}
```

次に、受信側はファイル /dev/shm/inference_client_share_memory_2146989006636459346 を開きます。 送信側は、このファイル内の特定の場所を参照する ContentReference を含む MediaSample メッセージを送信します。

```
{
    "timestamp": 143598615750000,
    "content_reference": 
    {
        "address_offset": 519168,
        "length_bytes": 173056
    }
}
```

その後、受信側はファイル内のこの場所からデータを読み取ります。

Video Analyzer エッジ モジュールが共有メモリを介して通信するには、コンテナーの IPC モードが正しく構成されている必要があります。 これはさまざまな方法で行うことができますが、推奨される構成をいくつか次に示します。

* ホスト デバイス上で実行されている gRPC 推論エンジンと通信する場合は、IPC モードをホストに設定する必要があります。
* 別の IoT Edge モジュールで実行されている gRPC サーバーと通信する場合、IPC モードは、Video Analyzer モジュールでは `shareable` に設定し、カスタム拡張機能では `container:avaedge` に設定する必要があります (Video Analyzer モジュールの名前が `avaedge` の場合)。

上記の最初のオプションを使用したデバイス ツインでは次のようになります。

```
"avaedge": 
{
  "version": "1.0",
  "type": "docker",
  "status": "running",
  "restartPolicy": "always",
  "settings": 
  {
    "image": "mcr.microsoft.com/media/video-analyzer:1",
    "createOptions": 
      "HostConfig": 
      {
        "IpcMode": "shareable"
      }
  }
}
```

IPC モードの詳細については、[IPC 設定 (--ipc)](https://docs.docker.com/engine/reference/run/#ipc-settings---ipc) に関する記事を参照してください。

## <a name="video-analyzer-grpc-extension-contract-definitions"></a>Video Analyzer gRPC 拡張機能のコントラクトの定義

このセクションでは、データ フローを定義する gRPC コントラクトを定義します。

### <a name="protocol-messages"></a>プロトコル メッセージ

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/grpc-extension-protocol/contract-definitions.png" alt-text="Video Analyzer のプロトコル メッセージ"  lightbox="./media/grpc-extension-protocol/contract-definitions.png":::

### <a name="client-authentication"></a>クライアント認証

カスタム拡張機能の実装側は、受信 gRPC 接続の信頼性を検証して、それらが gRPC 拡張機能ノードからのものであることを確認できます。 このノードは、検証対象となる要求ヘッダーのエントリを提供します。

ユーザー名およびパスワード資格情報を使用して、これを行うことができます。 gRPC 拡張機能ノードを作成する場合、資格情報は次のように指定します。

```
{
  "@type": " #Microsoft.VideoAnalyzer.GrpcExtension ",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": " #Microsoft.VideoAnalyzer.UnsecuredEndpoint ",
    "url": "tcp://customExtension:8081",
    "credentials": 
    {
      "@type": "#Microsoft. VideoAnalyzer.UsernamePasswordCredentials",
      "username": "username",
      "password": "password"
    }
  }
  // Other fields omitted
}
```

gRPC 要求の送信時に、次のヘッダーが要求メタデータに含まれ、HTTP 基本認証が模倣されます。

```
x-ms-authentication: Basic (Base64 Encoded username:password)
```

## <a name="configuring-inference-server-for-each-live-pipeline-over-grpc-extension"></a>gRPC 拡張機能経由で各ライブ パイプラインの推論サーバーを構成する

推論サーバーを構成するとき、推論サーバー内にパッケージされた AI モデルごとにノードを公開する必要はありません。 代わりにライブ パイプラインに対して、**GrpcExtension** ノードの `extensionConfiguration` プロパティを使用して、AI モデルの選択方法を定義できます。 実行中、この文字列が Video Analyzer から推論サーバーに渡され、推論サーバーは、それを使用して目的の AI モデルを呼び出すことができます。 この `extensionConfiguration property` は省略可能なプロパティであり、gRPC サーバーの実装に固有です。 このプロパティは、次のように使用できます。

```
{
  "@type": "#Microsoft.VideoAnalyzer.GrpcExtension",
  "name": "{moduleIdentifier}",
  "endpoint": 
  {
    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
    "url": "${grpcExtensionAddress}",
    "credentials": 
    {
      "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
      "username": "${grpcExtensionUserName}",
      "password": "${grpcExtensionPassword}"
    }
  },
    // Optional server configuration string. This is server specific 
  "extensionConfiguration": "{Optional extension specific string}",
  "dataTransfer": 
  {
    "mode": "sharedMemory",
    "SharedMemorySizeMiB": "75"
  }
    //Other fields omitted
}
```

## <a name="using-grpc-over-tls"></a>TLS 経由での gRPC の使用

推論に使用される gRPC 接続は、TLS を介してセキュリティ保護できます。 これは、Video Analyzer と推論エンジン間のネットワークのセキュリティを保証できない場合に役立ちます。 TLS により、gRPC メッセージに埋め込まれたすべてのコンテンツが暗号化されるため、フレームを高速で送信するときに追加の CPU オーバーヘッドが発生します。

`IgnoreHostname` および `IgnoreSignature` 検証オプションは gRPC ではサポートされていないため、推論エンジンが提示するサーバー証明書には、gRPC 拡張機能ノードのエンドポイント URL の IP アドレスおよびホスト名と完全に一致する共通名 (CN) が含まれている必要があります。

## <a name="next-steps"></a>次の手順

[推論メタデータ スキーマ](inference-metadata-schema.md)について学習する
