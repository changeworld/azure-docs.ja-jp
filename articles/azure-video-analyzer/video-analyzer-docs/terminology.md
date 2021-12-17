---
title: Azure Video Analyzer の用語
description: この記事では、Azure Video Analyzer の用語について概説します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9010ae81606bcd84d37debc4c702a0bc53e328dd
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846959"
---
# <a name="azure-video-analyzer-terminology"></a>Azure Video Analyzer の用語

この記事では、[Azure Video Analyzer](overview.md) に関連する用語について概説します。

## <a name="pipeline-topology"></a>パイプライン トポロジ

[パイプライン トポロジ](pipeline.md)を使用して、メディアのキャプチャ元、処理方法、および結果の配信先を定義できます。 これにより、ソース、プロセッサ、およびシンク ノードで構成されるパイプラインを定義し、それを使用してライブ ビデオ分析アプリケーションを作成することができます。 

## <a name="rtsp"></a>RTSP

[RTSP](https://tools.ietf.org/html/rfc2326) とは、リアルタイム ストリーミング プロトコルのことです。 これは、リアルタイム プロパティを使用してデータの配信を制御するためのアプリケーション レベルのプロトコルです。 RTSP により、オーディオやビデオなどのリアルタイム データのオンデマンド配信を制御できるようにする拡張可能なフレームワークが提供されます。 Video Analyzer では、RTSP をサポートする IP カメラからのビデオの取得、分析、記録が[サポート](pipeline.md#rtsp-source)されています。

## <a name="vms"></a>VMS

[VMS](https://en.wikipedia.org/wiki/Video_management_system) とは、ビデオ管理システムのことです。 このようなシステムは、CCTV カメラの構成と制御、それらからのビデオのキャプチャと記録に使用されます。 これらのシステムでは、記録されたビデオを再生するクライアント アプリケーションも提供されます。

## <a name="recording"></a>記録中

セキュリティ カメラ用のビデオ管理システムのコンテキストでは、ビデオ記録とは、カメラからビデオをキャプチャし、その後モバイルおよびブラウザー アプリで表示するためにそれを格納するプロセスを指します。 ビデオ記録は、[連続ビデオ記録](continuous-video-recording.md)と[イベントベースのビデオ記録](event-based-video-recording-concept.md)に分類できます。 これらの詳細については、[ビデオ記録](video-recording.md)の概念のページを参照してください。

## <a name="streaming"></a>ストリーム

Video Analyzer を使用すると、[HTTP ライブ ストリーミング (HLS)](https://developer.apple.com/streaming/) や [MPEG-DASH](https://dashif.org/about/) などの業界標準の HTTP ベースのメディア ストリーミング プロトコルを使用して、ビデオ記録をクライアントにストリームできます。 [Azure Video Analyzer プレーヤー ウィジェット](https://github.com/Azure/video-analyzer/blob/main/widgets/readme.md) (Web コンポーネント) を使用して、ビデオ リソースを再生できます。 また、HLS は、[JW Player](https://www.jwplayer.com/)、[hls.js](https://github.com/video-dev/hls.js/)、[VideoJS](https://videojs.com/)、[Google’s Shaka Player](https://github.com/google/shaka-player) などの Web プレーヤーでサポートされています。さらに、Android の [Exoplayer](https://github.com/google/ExoPlayer) や iOS の [AV Foundation](https://developer.apple.com/av-foundation/) を使用してモバイル アプリでネイティブにレンダリングすることもできます。 同様に、MPEG-DASH は[こちらのページにあるクライアントの一覧](https://dashif.org/tools/clients/)でサポートされています。

## <a name="exporting"></a>エクスポート

セキュリティ カメラ用の VM のコンテキストでは、ビデオのエクスポートとは、ビデオ記録の選択した部分を取得し、外部で共有可能な別のファイル (通常は[MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) ファイル) を作成するプロセスを指します。 たとえば、ビデオ記録には、スタッフによる迅速な行動によって安全性のインシデントが防止された映像が含まれる場合があります。 記録のその部分は、今後のトレーニング セッションで使用するために MP4 ファイルにエクスポートできます。

## <a name="video"></a>ビデオ

ビデオは、記録、再生、ストリーミング、エクスポートなどの VM 機能を有効にする、Video Analyzer アカウントのリソースです。 ビデオは、RTSP カメラから記録することも、既存の記録されたビデオの一部をエクスポートすることによって作成することもできます。 記録されたビデオは、Video Analyzer プレーヤー ウィジェットやその他の互換性のあるプレーヤーを使用してストリーミングおよび表示できます。 エクスポートされたビデオは MP4 ファイルとしてダウンロードできます。

RTSP カメラからの記録に Video Analyzer を使用する場合は、そのビデオ リソースをそのカメラに関連付ける必要があります。 そのカメラのビデオをそのビデオ リソースに継続的に記録することも、イベントに基づいて散発的に記録することもできます。Video Analyzer では、既存のビデオ リソースへのデータの追加をサポートしています。 ただし、そのためには、RTSP カメラのプロパティ (解像度、フレーム レートなど) が変更されないようにする必要があります。 カメラの設定を変更する必要がある場合は、新しいビデオ リソースへの記録に切り替える必要があります。

Video Analyzer アカウントを作成する場合は、Azure ストレージ アカウントを関連付ける必要があります。 ストレージ アカウントのコンテナーに BLOB として保存された、記録済みとエクスポート済みの両方のビデオ。 そのようなビデオ リソースに関連付けられているすべてのコンテンツは、対応するコンテナーに BLOB として格納され、Video Analyzer はメタデータ (名前、説明、作成時刻など) を保持します。

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/docs/guides/) は言語に依存しない高性能なリモート プロシージャ コール (RPC) フレームワークです。 通信の基礎となるメッセージ交換形式として、[Protocol Buffers 3](https://developers.google.com/protocol-buffers/docs/proto3) を介したセッションベースの構造化スキーマを使用しています。

## <a name="low-latency-streaming"></a>低遅延ストリーミング

低遅延ビデオ ストリーミングは、VM システムの便利な機能です。 Video Analyzer では、約 2 秒の遅延でライブ ビデオをストリーミングできます。 待機時間とは、カメラの前でイベントが発生した瞬間と、そのイベントが再生デバイスで表示されたときの間の遅延を意味します。

## <a name="next-steps"></a>次の手順

- [パイプライン](pipeline.md)に関する説明を確認します。
