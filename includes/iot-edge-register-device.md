---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/25/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: bacafdc8f7fd8e206335f3be0a086df1c54f1081
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38746711"
---
お使いの IoT ハブと通信できるようにシミュレートされたデバイスのデバイス ID を作成します。 IoT Edge デバイスは、一般的な IoT デバイスとは異なる動作をし、別に管理できるため、IoT Edge デバイスであることを最初から宣言します。 

1. Azure Portal で、お使いの IoT ハブに移動します。
1. **[IoT Edge]** を選択してから **[IoT Edge デバイスの追加]** を選択します。

   ![IoT Edge デバイスの追加](./media/iot-edge-register-device/add-device.png)

1. シミュレートされたデバイスに一意のデバイス ID を付与します。
1. **[保存]** を選択して、お使いのデバイスを追加します。
1. デバイスの一覧から、お使いの新しいデバイスを選択します。
1. **[接続文字列 - 主キー]** の値をコピーして保存します。 次のセクションで、IoT Edge ランタイムを構成するときにこの値を使用します。 

