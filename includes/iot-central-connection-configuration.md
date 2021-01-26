---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017534"
---
後でこのチュートリアルの中でサンプル デバイス アプリケーションを実行するときに、次の構成値が必要になります。

* ID スコープ: IoT Central アプリケーションで、 **[管理] > [デバイス接続]** に移動します。 **ID スコープ** の値を書き留めておきます。
* グループ プライマリ キー: IoT Central アプリケーションで、 **[管理] > [デバイス接続] > [SAS-IoT-Devices]** に移動します。 Shared Access Signature の **[プライマリ キー]** 値を書き留めておきます。

Cloud Shell を使用して、取得したグループ SAS キーからデバイス キーを生成します。

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

生成されたデバイス キーを書き留めておきます。このキーは、後でこのチュートリアルの中で使用します。
