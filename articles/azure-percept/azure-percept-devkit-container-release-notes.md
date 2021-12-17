---
title: Azure Percept DK コンテナーのリリース ノート
description: Azure Percept DK コンテナーのリリースでの変更と修正に関する情報。
author: amiyouss
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: 5b4e67197e5d43e929ca01c8c81e297e134edf47
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271614"
---
# <a name="azure-percept-dk-container-release-notes"></a>Azure Percept DK コンテナーのリリース ノート

このページでは、Azure Percept DK コンテナーのリリースでの変更と修正に関する情報について説明します。

コンテナーの更新プログラムをダウンロードするには、[Azure Percept Studio](https://ms.portal.azure.com/#blade/AzureEdgeDevices/main/overview) に移動し、左側のナビゲーション ペインで [デバイス] を選択し、特定のデバイスを選択してから、[ビジョン] と [音声] タブを選択してコンテナーのダウンロードを開始します。 

## <a name="august-2108-release"></a>8 月 (2108) リリース

- Azureyemodule (mcr.microsoft.com/azureedgedevices/azureeyemodule:2108-1)
    - MyriadX Camera ファームウェアの更新に関する Intel の最新 (5 月) ドロップに更新されました。 
    - 入力ソースとして UVC (USB ビデオ クラス) カメラが有効になりました。 入力ソースとして UVC カメラを使用する方法については、[高度な開発に関する GitHub](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule#using-uvcusb-video-class-camera-as-input-source) を参照してください。 
    - H.264 生 RTSP ストリームを使用したときのモジュールのクラッシュを修正しました。

## <a name="june-2106-release"></a>6 月 (2106) リリース

- Azureyemodule
    - このリリースでは、ビデオ ストリームを使用する低速ニューラル ネットワークの推論の時間調整のサポートが追加されました。 ニューラル ネットワークの待機時間にほぼ等しい待機時間がビデオ ストリームに追加されますが、推論 (境界ボックスなど) は適切な場所のビデオに描画されます。 
    - この機能を有効にするには、IoT Hub Azure portal でモジュール ツインに `TimeAlignRTSP: true` を追加します。
- Azureearspeechclientmodule
    - Speech モジュールに [Speech 1.16 SDK](../cognitive-services/speech-service/devices-sdk-release-notes.md) が統合されました。これには音声トークンのサポートに対する修正が含まれ、低レベル ライブラリでの既定でサポートされるデバイスとして EAR SOM が統合されます。
    - セキュリティ MCU は削除されてもコーデックは削除されないときの、PnP 検出の問題を修正しました。
    - PTT/PTS ボタン関数での Speech サービスのタイムアウトに対処しました。
    - セキュリティに関する修正
        - 音声モジュールで TLS データを受信する範囲外の読み取り。
        - MCU とコーデックの USB ポートが、DFU の実行中に再公開されます。
        - JSON を解析するときの例外処理。
        - すべての強化されたコンパイラ セキュリティ フラグの有効化。
        - 音声モジュールで TLS データを受信する範囲外の読み取り。
        - SSL と libcurl の依存関係をバージョン管理し、脆弱なバージョンを防ぎます。
        - HTTPS の適用と curl 接続での TLS CA の固定。

## <a name="april-2104-release"></a>4 月 (2104) リリース

- Azureyemodule
    - IoT Hub のメッセージ形式を UTF-8 エンコード JSON に修正しました (以前は 64 ビット エンコード形式でした)。
    - Custom Vision 分類子のバグを修正しました (以前は、Custom Vision 分類子モデルが正しく動作しませんでした。出力テンソルの間違ったディメンションがクラスの信頼度として解釈され、信頼度に関係なく、常に 1 つのクラスが予測されました)。
    - Azure ビデオ アナライザーの統合のため、UDP ではなく TCP を使用するように H.264 が更新されました。

## <a name="next-steps"></a>次のステップ

- [更新戦略を決定する方法](./how-to-determine-your-update-strategy.md)
