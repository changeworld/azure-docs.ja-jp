---
title: Azure Video Analyzer のクォータと制限事項 - Azure
description: この記事では、Azure Video Analyzer のクォータと制限事項について説明します。
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cad4ed3ffac3d9b8617150070579bd9bee5ad6b3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563133"
---
# <a name="video-analyzer-quotas-and-limitations"></a>Azure Video Analyzer のクォータと制限事項

この記事では、Azure Video Analyzer のクォータと制限事項について説明します。

## <a name="quotas-and-limitations---edge-module"></a>クォータと制限事項 - Edge モジュール

このセクションでは、Video Analyzer エッジ モジュールのクォータと制限事項を列挙します。

### <a name="maximum-period-of-disconnected-use"></a>切断された状態での最大使用期間

エッジ モジュールで、インターネット接続の一時的消失が維持されることがあります。 モジュールの切断状態が 36 時間を超えると、実行されていたすべてのライブ パイプラインが非アクティブ化されます。 その他のダイレクト メソッド呼び出しはすべてブロックされます。

エッジ モジュールを動作状態に復元するには、インターネット接続を再確立して、モジュールが Azure Video Analyzer アカウントと正常に通信できるようにする必要があります。

### <a name="maximum-number-of-live-pipelines"></a>ライブ パイプラインの最大数

モジュールあたり最大で 1000 個のライブパイプライン (`livePipelineSet` で作成) がサポートされています。

### <a name="maximum-number-of-pipeline-topologies"></a>パイプライン トポロジの最大数

モジュールあたり最大で 50 個のパイプライン トポロジ (`pipelineTopologySet` で作成) がサポートされています。

### <a name="limitations-on-pipeline-topologies"></a>パイプライン トポロジに関する制限事項

パイプライン トポロジで異なるノードをまとめて接続する方法に関する制限を次に示します。

* RTSP ソース
   * パイプライン トポロジごとに 1 つの RTSP ソースのみを使用できます。
* モーション検出プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサの下流で使用することはできません。
* シグナル ゲート プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサの上流で使用することはできません。
* オブジェクトトラッカーのプロセッサ
   * HTTP または gRPC 拡張プロセッサの直下の下流にある必要があります。 拡張プロセッサで、ライブ ビデオのすべてのフレームに AI モデルを適用してはなりません。
* ライン クロッシング プロセッサ
   * オブジェクト トラッカー プロセッサの直下の下流にある必要があります。
* ビデオ シンク 
   * RTSP ソースまたはシグナル ゲート プロセッサのすぐ下流にある必要があります。
* ファイル シンク
   * シグナル ゲート プロセッサのすぐに下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサ、またはモーション検出プロセッサの直下の下流にすることはできません。
* IoT Hub シンク
   * IoT Hub ソースの直下の下流にすることはできません。

### <a name="supported-cameras"></a>サポートされているカメラ
RTSP プロトコルをサポートする IP カメラのみを使用できます。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。

これらのカメラは、H.264 ビデオと AAC オーディオを使用するように構成する必要があります。 他のコーデックは現在サポートされていません。

Video Analyzer では、[インターリーブされた RTP ストリーム](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)でのみ RTSP がサポートされます。 このモードでは、RTP トラフィックは RTSP TCP 接続を通じてトンネリングされます。 UDP 経由の RTP トラフィックはサポートされていません。 

### <a name="support-for-video-ai"></a>ビデオ AI のサポート
HTTP または gRPC 拡張プロセッサは、外部 AI モジュールを使用した画像/ビデオ フレーム データの送信のみをサポートします。 したがって、オーディオ データに対する推論の実行はサポートされていません。 そのため、`inputs` の 1 つとして RTSP ソース ノードを持つパイプライン トポロジ内のプロセッサ ノードも、`outputSelectors` プロパティを使用して、ビデオのみがプロセッサに渡されるようにします。 例として、この[トポロジ](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-grpcExtension-video-sink/topology.json)を確認してください。

## <a name="quotas-and-limitations---live-and-batch-pipeline"></a>クォータと制限事項 - ライブおよびバッチ パイプライン

このセクションでは、Video Analyzer クラウド パイプラインのクォータと制限事項について説明します。 

### <a name="maximum-number-of-pipeline-topologies"></a>パイプライン トポロジの最大数 

Azure Video Analyzer アカウントあたり最大 5 つのパイプライン トポロジがサポートされています。 

### <a name="limits-on-concurrent-activation-of-live-pipelines"></a>ライブ パイプラインの同時アクティブ化に関する制限事項 

最大 10 個のライブ パイプラインは、5 分の時間枠でアクティブ化できます。また、最大で 10 個のパイプラインを任意の時点で `Activating` 状態にすることができます。

これらの制限は、パイプライン ジョブには適用されません。  

### <a name="maximum-live-pipelines-per-topology"></a>トポロジあたりの最大ライブ パイプライン数  

トポロジあたり最大 50 個のライブ パイプラインがサポートされています。  

### <a name="concurrent-low-latency-streaming-sessions"></a>同時実行の待機時間の短いストリーミング セッション  

アクティブなライブ パイプラインごとに、[待機時間の短いストリーム](playback-recordings-how-to.md#low-latency-streaming)を表示するクライアント アプリケーションが最大で 1 つあります。 別のクライアントが接続しようとすると、要求は拒否されます。  

### <a name="limitations-on-designing-pipeline-topologies"></a>パイプライン トポロジの設計に関する制限事項 

パイプライン トポロジで接続できるさまざまなノードと、それらの制限事項を次に示します。 

* RTSP ソース
   * パイプライン トポロジごとに 1 つの RTSP ソースのみを使用できます。
* ビデオ ソース
   * パイプライン トポロジごとに 1 つのビデオ ソースのみを使用できます。
   * [バッチの種類](pipeline.md#batch-pipeline)のパイプライン トポロジでのみ使用できます。 `archive` の種類のビデオ リソースのみを受け入れることができます。 
* エンコーダー プロセッサ 
   * パイプライン トポロジごとに、1 つのエンコーダー プロセッサのみが許可されます。
   * [バッチの種類](pipeline.md#batch-pipeline)のパイプライン トポロジでのみ使用できます。また、このようなトポロジでは、ビデオ シンク ノードの直近の上流にある必要があります。
   * これは、AAC コーデックを使用した h.264 コーデックとオーディオによるビデオのエンコードのみをサポートしています。
   * 録画されたビデオをダウンストリーム処理の目的の形式に変換するときに、ユーザーがエンコード プロパティを指定できるようにします。 2 種類: (a) システム プリセット (b) カスタム プリセット。 各プリセットに許可されている構成を次の表に示します。 
     
     | 構成       | システム プリセット        | カスタム プリセット |
     | -----------         | -----------          |----------- |
     | ビデオ エンコーダー ビットレート kbps      | ソースと同じ      | 200 から 16,000 kbps |
     | フレーム レート       | ソースと同じ      | 0 から 300 |
     | [高さ]    | ソースと同じ        | 1 から 4320 |
     | 幅    | ソースと同じ       | 1 から 8192 |
     | モード   | Pad        | Pad、PreserveAspectRatio、Stretch |     
     | オーディオ エンコーダー ビットレート kbps  | ソースと同じ        | 指定できる値: 96、112、128、160、192、224、256 |
     
* ビデオ シンク 
   *  パイプライン トポロジごとに 1 つのビデオ シンクのみが許可されます。
   *  RTSP ソースまたはエンコーダーのプロセッサ ノードから直近の下流のストリームである必要があります。 
   *  バッチの種類のパイプライン トポロジで使用した場合、出力として MP4 ファイルが生成されます。

### <a name="support-for-batch-video-export"></a>バッチ ビデオ エクスポートのサポート 

* セグメントの選択 
   * タイム シーケンスは、エクスポートするアーカイブ済みビデオの部分の開始タイムスタンプと終了タイムスタンプであり、UTC 時間で指定する必要があります。 
   * タイム シーケンスの最大時間 (終了タイムスタンプと開始タイムスタンプの間) は、24 時間以内である必要があります。 

### <a name="supported-cameras"></a>サポートされているカメラ

RTSP プロトコルをサポートする IP カメラのみを使用できます。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。 

これらのカメラは、H.264 ビデオと AAC オーディオを使用するように構成する必要があります。 他のコーデックは現在サポートされていません。 ビデオ エンコードのビットレートは、500 から 3000 Kbps の範囲で指定する必要があります。実際のインジェスト ビットレートがこのしきい値を超えた場合、インジェストは切断され、エクスポネンシャル バックオフに再接続されます。

Video Analyzer では、[インターリーブされた RTP ストリーム](https://datatracker.ietf.org/doc/html/rfc2326#section-10.12)でのみ RTSP がサポートされます。 このモードでは、RTP トラフィックは RTSP TCP 接続を通じてトンネリングされます。 UDP 経由の RTP トラフィックはサポートされていません。 

### <a name="support-for-video-ai"></a>ビデオ AI のサポート 

ライブ ビデオまたは録画済みビデオの分析は、現在サポートされていません。

## <a name="quotas-and-limitations---service"></a>クォータと制限事項 - Service

このセクションでは、Video Analyzer アカウントのクォータと制限事項を列挙します。

### <a name="limitations-on-service-operations-at-preview"></a>プレビュー時の Service 操作に関する制限事項

プレビュー リリースの時点では、Video Analyzer サービスで次はサポートされていません。

* あるサブスクリプションから別のサブスクリプションにアカウントを中断なく移行する機能。
* アカウントで複数のストレージ アカウントを使用する機能。
* Video Analyzer アカウントとは異なるリージョンの Storage アカウントを使用する機能。

### <a name="limits-on-video-resources"></a>ビデオ リソースに関する制限事項
プレビュー リリースの時点で、各 Video Analyzer アカウントは最大で 1 万個のビデオ リソースを持つことができます。 より高い制限が必要なビジネス ニーズがある場合は、Azure portal でサポート チケットを開きます。

### <a name="limits-on-access-policies"></a>アクセス ポリシーに関する制限事項
プレビュー リリースの時点で、各 Video Analyzer アカウントは最大で 20 個のアカウント ポリシーを持つことができます。

### <a name="limits-on-registered-edge-modules"></a>登録済みエッジ モジュールに関する制限事項
プレビュー リリースの時点で、各 Video Analyzer アカウントは、アカウントに登録された 最大で 1,000 個のエッジ モジュールを持つことができます。 より高い制限が必要なビジネス ニーズがある場合は、Azure portal でサポート チケットを開きます。

### <a name="limits-on-client-api-calls"></a>クライアント API 呼び出しに関する制限事項
プレビューリリースでは、クライアント API に次の制限事項が適用されています。

* 10 秒以内に最大で 50 個の要求
* 1時間あたり最大で 600 個の要求

## <a name="limitations---video-analyzer-player-widgets"></a>制限事項-Video Analyzer プレーヤー ウィジェット

プレビュー リリースの時点では、iOS デバイスでの再生はサポートされていません。

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
