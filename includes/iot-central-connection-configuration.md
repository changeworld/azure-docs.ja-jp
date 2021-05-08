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
ms.openlocfilehash: e4f9fd537d7743a5bbb9d129b21c4bf0a529d32d
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491160"
---
後でこのチュートリアルの中でサンプル デバイス アプリケーションを実行するときに、次の構成値が必要になります。

* ID スコープ: IoT Central アプリケーションで、 **[管理] > [デバイス接続]** に移動します。 **ID スコープ** の値を書き留めておきます。
* グループ プライマリ キー: IoT Central アプリケーションで、 **[管理] > [デバイス接続] > [SAS-IoT-Devices]** に移動します。 Shared Access Signature の **[プライマリ キー]** 値を書き留めておきます。

Cloud Shell を使用して、取得したグループ プライマリ キーからデバイス キーを生成します。

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key --device-id sample-device-01 --pk <the group primary key value>
```

生成されたデバイス キーを書き留めておきます。このキーは、後でこのチュートリアルの中で使用します。
