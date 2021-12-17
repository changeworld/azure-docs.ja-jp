---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 80328e4ec6ea06937a30c94a0e0f854e03eb81da
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851946"
---
## <a name="register-your-device"></a>デバイスを登録する

デバイスを登録するには、好みに応じて **Azure portal**、**Visual Studio Code**、または **Azure CLI** を使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal の IoT Hubで、IoT Edge デバイスは、Edge 対応ではない IoT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/iot-edge-register-device-symmetric/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、後で使用するのでメモしておいてください。
   * 認証の種類として **[対称キー]** を選択します。
   * 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。

1. **[保存]** を選択します。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT ハブで操作を実行できます。 これらの操作を機能させるには、Azure アカウントにサインインし、ハブを選択する必要があります。

1. Visual Studio Code で **エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/iot-edge-register-device-symmetric/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="register-a-new-device-with-visual-studio-code"></a>Visual Studio Code を使用して新しいデバイスを登録する

1. Visual Studio Code エクスプローラーで、 **[Azure IoT Hub]** セクションを展開します。
1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az iot hub device-identity create](/cli/azure/iot/hub/device-identity) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id device_id_here --hub-name hub_name_here --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* `--device-id` または `-d`: IoT ハブ内で一意のわかりやすい名前を指定します。
* `--hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`:デバイスが IoT Edge デバイスであることを宣言します。

   ![az iot hub device-identity create output](./media/iot-edge-register-device-symmetric/create-edge-device-cli.png)

---

デバイスが IoT Hub に登録されたので、IoT Edge ランタイムのインストールとプロビジョニングを完了するために使用する情報を取得します。

## <a name="view-registered-devices-and-retrieve-provisioning-information"></a>登録済みデバイスを表示し、プロビジョニング情報を取得する

対称キー認証を使用するデバイスでは、IoT Edge ランタイムのインストールとプロビジョニングを完了するために接続文字列が必要です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/iot-edge-register-device-symmetric/portal-view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

対称キーを使用して認証を行うデバイスでは、接続文字列をポータルでコピーできます。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/iot-edge-register-device-symmetric/view-devices.png)

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/iot/hub/device-identity) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name hub_name_here
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity connection-string show](/cli/azure/iot/hub/device-identity/connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device_id] --hub-name [hub_name]
   ```

>[!TIP]
>`connection-string show` コマンドは、Azure IoT 拡張機能のバージョン 0.9.8 で導入され、これによって、非推奨の `show-connection-string` コマンドが置き換えられました。 このコマンドの実行中にエラーが発生した場合は、拡張機能のバージョンが 0.9.8 以降に更新されていることを確認してください。 詳細情報と最新の更新プログラムについては、[Azure CLI 用の Microsoft Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)に関するページを参照してください。

`device-id` パラメーターの値は、大文字と小文字が区別されます。

デバイスで使用する接続文字列をコピーする場合は、接続文字列を囲んでいる引用符を含めないでください。

---
