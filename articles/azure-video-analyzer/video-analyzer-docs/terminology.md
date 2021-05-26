---
title: Azure Video Analyzer の用語
description: この記事では、Azure Video Analyzer の用語について概説します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 05/10/2021
ms.openlocfilehash: b29e2c788654a5445c0e14c00ad64690c405b003
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387816"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer の用語

この記事では、[Azure Video Analyzer](overview.md) に関連する用語について概説します。

## <a name="pipeline-topology"></a>パイプライン トポロジ

[パイプライン トポロジ](pipeline.md)を使用して、メディアのキャプチャ元、処理方法、および結果の配信先を定義できます。 これにより、ソース、プロセッサ、およびシンク ノードで構成されるパイプラインを定義し、それを使用してライブ ビデオ分析アプリケーションを作成することができます。 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) とは、リアルタイム ストリーミング プロトコルのことです。 これは、リアルタイム プロパティを使用してデータの配信を制御するためのアプリケーション レベルのプロトコルです。 RTSP により、オーディオやビデオなどのリアルタイム データのオンデマンド配信を制御できるようにする拡張可能なフレームワークが提供されます。 Video Analyzer では、RTSP をサポートする IP カメラからのビデオの取得、分析、記録が[サポート](pipeline.md#rtsp-source)されています。


## <a name="recording"></a>記録中

セキュリティ カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、その後モバイルおよびブラウザー アプリで表示するためにそれを格納するプロセスを指します。 ビデオ記録は、[連続ビデオ記録](continuous-video-recording.md)と[イベントベースのビデオ記録](event-based-video-recording-concept.md)に分類できます。 これらの詳細については、[ビデオ記録](video-recording.md)の概念のページを参照してください。

## <a name="video"></a>ビデオ

Video Analyzer アカウントを作成する場合は、Azure ストレージ アカウントを関連付ける必要があります。 Video Analyzer を使用すると、カメラからライブ ビデオを記録し、そのデータをディスクまたはクラウド ストレージに保持できます。 後者の場合、データはストレージ アカウント内のコンテナーに BLOB として格納されます。 ビデオは、そのような BLOB コンテナーにマップされる Video Analyzer アカウント内のリソースです。 そのようなビデオ リソースに関連付けられているすべてのコンテンツは、対応するコンテナーに BLOB として格納され、Video Analyzer はメタデータ (名前、説明、作成時刻など) を保持します。

Video Analyzer を使用すると、ビデオ リソースを作成し、既存のビデオにデータを追加できます。 これにより、継続的なイベントベースのビデオ記録と再生 (エッジ デバイスでのビデオ キャプチャ、Azure Media Services への記録、Video Analyzer ストリーミング機能による再生) を行えるようになります。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) は言語に依存しない高性能なリモート プロシージャ コール (RPC) フレームワークです。 通信の基礎となるメッセージ交換形式として、[Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) を介したセッションベースの構造化スキーマを使用しています。

## <a name="streaming"></a>ストリーム

Video Analyzer を使用すると、[HTTP ライブ ストリーミング (HLS)](https://developer.apple.com/streaming/) や [MPEG-DASH](https://dashif.org/about/) などの業界標準の HTTP ベースのメディア ストリーミング プロトコルを使用して、ビデオ記録をクライアントにストリームできます。 [Azure Video Analyzer プレーヤー ウィジェット](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) (Web コンポーネント) を使用して、ビデオ リソースを再生できます。 また、HLS は、[JW Player](https://www.jwplayer.com/)、[hls.js](https://github.com/video-dev/hls.js/)、[VideoJS](https://videojs.com/)、[Google’s Shaka Player](https://github.com/google/shaka-player) などの Web プレーヤーでサポートされています。さらに、Android の [Exoplayer](https://github.com/google/ExoPlayer) や iOS の [AV Foundation](https://developer.apple.com/av-foundation/) を使用してモバイル アプリでネイティブにレンダリングすることもできます。 同様に、MPEG-DASH は[こちらのページにあるクライアントの一覧](https://dashif.org/clients/)でサポートされています。

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) とは、ビデオ管理システムのことです。 このようなシステムは、CCTV カメラの構成と制御、それらからのビデオのキャプチャと記録に使用されます。 これらのシステムでは、記録されたビデオを再生するクライアント アプリケーションも提供されます。

## <a name="next-steps"></a>次の手順

- [パイプライン](pipeline.md)に関する説明を確認します。
