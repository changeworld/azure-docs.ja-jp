---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: ffd5da239f8e271a8c9b2aaf3f6d5fd9f885c79c
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2018
ms.locfileid: "49400113"
---
## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、[Azure portal](https://portal.azure.com) を使用して、IoT ハブの ID レジストリにデバイス ID を作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub デベロッパー ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)に関するページの "ID レジストリ" のセクションを参照してください。ポータルの **[IoT デバイス]** パネルを使用して、デバイスが IoT Hub に対して自身を識別するために使用する固有のデバイス ID とキーを生成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure ポータル](https://portal.azure.com)

2. **[すべてのリソース]** を選択し、該当する IoT Hub リソースを探します。

3. IoT Hub リソースが開いたら、**[IoT デバイス]** ツールをクリックし、上部の **[追加]** をクリックします。 

    ![ポータルでデバイス ID を作成する](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

4. 新しいデバイスの名前 (**myDeviceId** など) を入力し、**[保存]** をクリックします。 この操作で、IoT ハブの新しいデバイス ID が作成されます。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![新しいデバイスを追加する](./media/iot-hub-get-started-create-device-identity-portal/create-a-device.png)

5. デバイス一覧で、新しく作成したデバイスをクリックし、後で使用するために **[接続文字列 --- 主キー]** をコピーします。

    ![デバイスの接続文字列](./media/iot-hub-get-started-create-device-identity-portal/device-details.png)

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)をご覧ください。
