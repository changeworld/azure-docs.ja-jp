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
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264271"
---
## <a name="enabling-extended-offline-operation-preview"></a>拡張オフライン操作 (プレビュー) を有効にする
Edge ランタイムの [v1.0.4 リリース](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)では、Edge デバイスとそれに接続しているダウンストリーム デバイスを、拡張オフライン操作用に構成できます。 

この機能により、ローカル モジュールまたはダウンストリーム デバイスは、必要に応じて Edge デバイスと再認証を行うことができ、IoT Hub から切断された場合でもメッセージとメソッドを使用して相互に通信できます。 この機能の詳細とスコープについては、こちらの[ブログ投稿](https://aka.ms/iot-edge-offline)および[概念記事](../articles/iot-edge/offline-capabilities.md)をご覧ください。

ゲートウェイのシナリオでオフライン拡張を有効にするには、Edge デバイスとそれに接続するダウンストリーム デバイスの間に親子関係を確立します。

1. IoT Hub ポータルの Edge デバイス詳細ブレードで、上部コマンド バーの **[Manage Child Devices (preview)]\(子デバイスの管理 (プレビュー)\)** ボタンをクリックします。

1. **[+ 追加]** ボタンをクリックします。

1. デバイスの一覧から子デバイスを選択し、右矢印を使用して子として追加します。

1. **[OK]** をクリックして確定します。

Edge デバイスとその子デバイスが、拡張オフライン操作に対して有効になります。  