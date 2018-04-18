---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>デバイス ID の作成

このセクションでは、[Azure Portal][lnk-azure-portal] を使用して、IoT ハブの ID レジストリにデバイス ID を作成します。 IoT hub に接続するデバイスは、あらかじめ ID レジストリに登録されている必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]の ID レジストリに関するセクションをご覧ください。 ポータルの **[IoT デバイス]** パネルを使用して、IoT Hub に識別させるために使用するデバイスに固有のデバイス ID とキーを生成します。 デバイス ID には大文字と小文字の区別があります。

1. [Azure Portal][lnk-azure-portal] にサインインしていることを確認します。

1. ジャンプバーで **[すべてのリソース]** をクリックし、該当する IoT ハブ リソースを探します。

    ![IoT ハブに移動][img-find-iothub]

1. IoT Hub リソースが開いたら、**[IoT デバイス]** ツールをクリックし、上部の **[追加]** をクリックします。 新しいデバイスの名前 (**myDeviceId** など) を入力し、**[保存]** をクリックします。

    ![ポータルでデバイス ID を作成する][img-create-device]

   この操作で、IoT ハブの新しいデバイス ID が作成されます。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. **[IoT デバイス]** のデバイス一覧で、新しく作成したデバイスをクリックし、**[接続文字列 --- 主キー]** の内容を書き留めます。

    ![デバイスの接続文字列][img-connection-string]

> [!NOTE]
> IoT Hub の ID レジストリには、IoT ハブに対するセキュリティで保護されたアクセスを有効にするためのデバイス ID のみが格納されます。 セキュリティ資格情報として使用するキーとデバイス ID、そして個々のデバイスについてアクセスを無効にすることのできる有効/無効フラグが格納されます。 その他デバイス固有のメタデータをアプリケーションで保存する必要がある場合は、アプリケーション固有のストアを使用する必要があります。 詳細については、[IoT Hub 開発者ガイド][lnk-devguide-identity]をご覧ください。

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

