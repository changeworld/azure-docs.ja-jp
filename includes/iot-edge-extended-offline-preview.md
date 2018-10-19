---
title: インクルード ファイル
description: IoT Edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004681"
---
## <a name="enabling-extended-offline-operation-preview"></a>拡張オフライン操作 (プレビュー) を有効にする
Edge ランタイムの [v1.0.2 リリース](https://aka.ms/edge102)以降では、Edge デバイスとそれに接続しているダウンストリーム デバイスを、拡張オフライン操作用に構成できます。 

この機能により、ローカル モジュールまたはダウンストリーム デバイスは、必要に応じて Edge デバイスと再認証を行うことができ、IoT Hub から切断された場合でもメッセージとメソッドを使用して相互に通信できます。 この機能の詳細とスコープについては、こちらの[ブログ投稿](https://aka.ms/iot-edge-offline)および[概念記事](../articles/iot-edge/offline-capabilities.md)をご覧ください。

ゲートウェイのシナリオでオフライン拡張を有効にするには、Edge デバイスとそれに接続するダウンストリーム デバイスの間に親子関係を確立します。

1. IoT Hub ポータルの Edge デバイス詳細ブレードで、上部コマンド バーの **[Manage Child Devices (preview)]\(子デバイスの管理 (プレビュー)\)** ボタンをクリックします。

1. **[+ 追加]** ボタンをクリックします。

1. デバイスの一覧から子デバイスを選択し、右矢印を使用して子として追加します。

1. **[OK]** をクリックして確定します。

1. Edge デバイスの詳細の **[モジュールの設定]** 画面で、**[Edge ランタイムの詳細設定を構成する]** をクリックし、**Edge ハブ**の環境変数に **UpstreamProtocol** エントリ (値は **MQTT**) を追加します。 同じ環境変数と値を、**Edge エージェント**にも追加します。 

1. **[保存]** をクリックし、**[次へ]** を 2 回クリックした後で必ず変更を **[送信]** します。

Edge デバイスとその子デバイスが、拡張オフライン操作に対して有効になります。  