---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3e07cbe5b8b90ff236b9cdc0d12f31af601a253a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842701"
---
## <a name="register-your-device"></a>デバイスを登録する

デバイスを登録するには、好みに応じて **Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/iot-edge-register-device-x509/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、後で使用するのでメモしておいてください。
   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * プライマリとセカンダリの ID 証明書の拇印を指定します。 拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

現在、Visual Studio Code 用の Azure IoT 拡張機能では、X.509 証明書を使用したデバイス登録はサポートされていません。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/iot/hub/device-identity) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled --auth-method x509_thumbprint --primary-thumbprint primary_SHA_thumbprint_here --secondary-thumbprint secdonary_SHA_thumbprint_here
   ```

このコマンドには、いくつかのパラメーターが含まれています。

* `--device-id` または `-d`: IoT ハブに対して一意のわかりやすい名前を指定します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
* `hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`: デバイスが IoT Edge デバイスであることを宣言します。
* `--auth-method` または `--am`: デバイスが使用する承認の種類を宣言します。 この例では、X.509 証明書の拇印を使用しています。
* `--primary-thumbprint` または `--ptp`: 主キーとして使用する X.509 証明書の拇印を指定します。
* `--secondary-thumbprint` または `--stp`: セカンダリ キーとして使用する X.509 証明書の拇印を指定します。

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する情報を取得します。

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>登録済みデバイスを表示し、プロビジョニング情報を取得する

X.509 証明書認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために、IoT Hub 名、デバイス名、証明書ファイルが必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/iot-edge-register-device-x509/portal-view-devices.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Visual Studio Code による X.509 証明書へのデバイスの登録はサポートされていませんが、必要に応じて IoT Edge デバイスを表示することはできます。

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/iot-edge-register-device-x509/view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

---
