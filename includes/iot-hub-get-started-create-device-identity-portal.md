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
ms.openlocfilehash: 935e2b9e861a889bef48c1d7ba119ab694cddfb3
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094211"
---
## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、[Azure portal](https://portal.azure.com) を使用して、IoT ハブの ID レジストリにデバイス ID を作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub デベロッパー ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)に関するページの "ID レジストリ" のセクションを参照してください。ポータルの **[IoT デバイス]** パネルを使用して、デバイスが IoT Hub に対して自身を識別するために使用する固有のデバイス ID とキーを生成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure ポータル](https://portal.azure.com)

1. **[すべてのリソース]** を選択し、該当する IoT Hub リソースを探します。

1. IoT Hub リソースが開いたら、**[IoT デバイス]** ツールをクリックし、上部の **[追加]** をクリックします。 

    ![ポータルでデバイス ID を作成する](./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png)

1. 新しいデバイスの名前 (**myDeviceId** など) を入力し、**[保存]** をクリックします。 この操作で、IoT ハブの新しいデバイス ID が作成されます。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![新しいデバイスを追加する](./media/iot-hub-get-started-create-device-identity-portal/add-device.png)

1. デバイス一覧で、新しく作成したデバイスをクリックし、後で使用するために **[接続文字列 --- 主キー]** をコピーします。

    ![デバイスの接続文字列](./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png)

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド](../articles/iot-hub/iot-hub-devguide-identity-registry.md)をご覧ください。
