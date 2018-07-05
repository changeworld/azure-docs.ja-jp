---
title: 新しい Azure IoT Edge デバイス (ポータル) の登録 | Microsoft Docs
description: Azure portal を使用して新しい IoT Edge デバイスを登録する
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b61594469df33e11c23c9cbe0b9542da374fefa3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034737"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Azure portal から新しい Azure IoT Edge デバイスを登録する

Azure IoT Edge で IoT デバイスを使用する前に、それらを IoT ハブに登録する必要があります。 デバイスを登録すると、Edge ワークロード用にデバイスを設定するために使用できる接続文字列を受け取ります。 

この記事では、Azure portal を使用して新しい IoT Edge デバイスを登録する方法を示します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。 

## <a name="create-a-device"></a>デバイスの作成

Azure portal で、IoT Edge デバイスは、IoT ハブに接続するがエッジ対応でないデバイスとは別に作成および管理されます。 

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。 
2. メニューから **[IoT Edge]** を選択します。
3. **[IoT Edge デバイスの追加]** を選択します。 
4. わかりやすいデバイス ID を指定します。 
5. **[保存]** を選択します。 

## <a name="view-all-devices"></a>すべてのデバイスを表示する

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。 

## <a name="retrieve-the-connection-string"></a>接続文字列の取得

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. ポータルの **IoT Edge** ページで、Edge デバイスの一覧からデバイス ID をクリックします。 
2. **接続文字列 - 主キー**または**接続文字列 - セカンダリ キー**の値をコピーします。 

## <a name="next-steps"></a>次の手順

[Azure portal でモジュールをデバイスにデプロイ](how-to-deploy-modules-portal.md)する方法を学習します