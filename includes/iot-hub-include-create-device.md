---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 4a4ec51430242cc3e3d6a0b801f2b4be7858ab50
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2018
ms.locfileid: "51516093"
---
<!-- put the ## header in the file that includes this file -->

このセクションでは、IoT ハブの ID レジストリにデバイス ID を作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳しくは、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)の "ID レジストリ" に関するセクションをご覧ください 

1. IoT Hub ナビゲーション メニューの **[IoT デバイス]** を開き、**[追加]** をクリックして IoT Hub に新しいデバイスを登録します。

    ![ポータルでデバイス ID を作成する](./media/iot-hub-include-create-device/create-identity-portal.png)

1. 新しいデバイスの名前 (**myDeviceId** など) を入力し、**[保存]** をクリックします。 この操作で、IoT ハブの新しいデバイス ID が作成されます。

   ![新しいデバイスを追加する](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. デバイスが作成された後、**[IoT デバイス]** ウィンドウの一覧からデバイスを開きます。 後で使用するために **[接続文字列 --- 主キー]** をコピーします。

    ![デバイスの接続文字列](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)をご覧ください。
