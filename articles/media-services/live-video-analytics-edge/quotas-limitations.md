---
title: Live Video Analytics on IoT Edge のクォータと制限 - Azure
description: この記事では、Live Video Analytics on IoT Edge のクォータと制限について説明します。
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90529232"
---
# <a name="quotas-and-limitations"></a>クォータと制限

この記事では Live Video Analytics on IoT Edge モジュールのクォータと制限を列挙します。

## <a name="maximum-period-of-disconnected-use"></a>切断された状態での最大使用期間

エッジ モジュールでは、ネットワーク接続が一時的に失われることがあります。 モジュールの切断状態が 36 時間を超えている場合は、実行されていたすべてのグラフ インスタンスが非アクティブ化され、さらなるダイレクト メソッド呼び出しがブロックされます。

エッジ モジュールを動作状態に戻すには、ネットワーク接続を復元し、モジュールが Azure Media Service アカウントと正常に通信できる必要があります。

## <a name="maximum-number-of-graph-instances"></a>グラフ インスタンスの最大数

モジュールあたり最大 1000 個のグラフ インスタンス (GraphInstanceSet を使用して作成される) を使用できます。

## <a name="maximum-number-of-graph-topologies"></a>グラフ トポロジの最大数

モジュールあたり最大 50 個のグラフ トポロジ (GraphTopologySet を使用して作成される) を使用できます。

## <a name="limitations-on-graph-topologies-at-preview"></a>プレビュー時のグラフ トポロジに関する制限事項

プレビュー リリースでは、メディア グラフ トポロジで相互に接続できる各種ノードに制限があります。

* RTSP ソース
   * グラフ トポロジごとに 1 つの RTSP ソースのみが許可されます。
* フレーム レート フィルター プロセッサ
   * RTSP ソースまたはモーション検出プロセッサのすぐ下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサの下流で使用することはできません。
   * モーション検出プロセッサの上流に存在することはできません。
* HTTP 拡張プロセッサ
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
* gRPC 拡張プロセッサ
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
* モーション検出プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
   * このようなプロセッサは、グラフ トポロジごとに最大 1 つ許可されます。
   * HTTP または gRPC 拡張プロセッサの下流で使用することはできません。
* シグナル ゲート プロセッサ
   * RTSP ソースのすぐ下流にある必要があります。
* 資産シンク 
   * RTSP ソースまたはシグナル ゲート プロセッサのすぐ下流にある必要があります。
* ファイル シンク
   * シグナル ゲート プロセッサのすぐに下流にある必要があります。
   * HTTP または gRPC 拡張プロセッサまたはモーション検出プロセッサのすぐ下流に存在することはできません。
* IoT Hub シンク
   * IoT Hub ソースのすぐ下流に存在することはできません。

モーション検出プロセッサ ノードとフィルター レート プロセッサ ノードの両方が使用されている場合、それらは RTSP ソース ノードにつながる同じノード チェーン内に存在する必要があります。

## <a name="limitations-on-media-service-operations-at-preview"></a>プレビュー時の Media Service 操作に関する制限事項

プレビュー リリースの時点では、Live Video Analytics on IoT Edge では次がサポートされていません。

* あるサブスクリプションから別のサブスクリプションにメディア サービス アカウントを中断なく移行する機能。
* メディア サービス アカウントで複数のストレージ アカウントを使用する機能。
* 再起動せずに、モジュールの目的のプロパティ内のサービス プリンシパル情報を動的に変更する機能。

RTSP プロトコルをサポートする IP カメラのみを使用できます。 RTSP をサポートする IP カメラは、[ONVIF 準拠製品](https://www.onvif.org/conformant-products)のページで見つけることができます。 プロファイル G、S、または T に準拠しているデバイスを探します。

さらに、これらのカメラは、H.264 ビデオと AAC オーディオを使用するように構成する必要があります。 他のコーデックは現在サポートされていません。 

## <a name="next-steps"></a>次のステップ

[概要](overview.md)
