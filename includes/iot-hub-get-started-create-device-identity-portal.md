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
ms.openlocfilehash: 1df3e188b71b8fa2d5223bad8bc5914513e26286
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2018
ms.locfileid: "34371203"
---
## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、[Azure Portal][lnk-azure-portal] を使用して、IoT ハブの ID レジストリにデバイス ID を作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の ID レジストリに関するセクションをご覧ください。 ポータルの **[IoT デバイス]** パネルを使用して、IoT Hub に識別させるために使用するデバイスに固有のデバイス ID とキーを生成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure Portal][lnk-azure-portal] にサインインします。

1. **[すべてのリソース]** を選択し、該当する IoT Hub リソースを探します。

1. IoT Hub リソースが開いたら、**[IoT デバイス]** ツールをクリックし、上部の **[追加]** をクリックします。 

    ![ポータルでデバイス ID を作成する][img-add-device]

1. 新しいデバイスの名前 (**myDeviceId** など) を入力し、**[保存]** をクリックします。 この操作で、IoT ハブの新しいデバイス ID が作成されます。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

   ![新しいデバイスを追加する][img-create-device]

1. デバイス一覧で、新しく作成したデバイスをクリックし、後で使用するために **[接続文字列 --- 主キー]** をコピーします。

    ![デバイスの接続文字列][img-connection-string]

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-add-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png
[img-create-device]:./media/iot-hub-get-started-create-device-identity-portal/add-device.png

<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

