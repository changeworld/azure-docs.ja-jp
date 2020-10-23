---
title: 対称キーを使用したプロビジョニング - Azure IoT Edge | Microsoft Docs
description: インストール後、IoT Edge デバイスをその接続文字列を使用して対称キーでプロビジョニングし、IoT Hub に対して認証を行います
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: f5371539c1b45c14b519729c7c07003bf74847a0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043869"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Azure IoT Edge デバイスに対称キー認証を設定する

この記事では、IoT Hub に新しい IoT Edge デバイスを登録し、対称キーで認証するようにデバイスを構成する手順について説明します。

この記事の手順では、手動プロビジョニングと呼ばれるプロセスを順を追って説明します。ここでは、各デバイスをその IoT ハブに手動で接続します。 IoT Hub Device Provisioning Service を使用して自動的にプロビジョニングする方法もあります。これは、プロビジョニングするデバイスが多数ある場合に便利です。

<!--TODO: Add auto-provision info/links-->

手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**:IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提示されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**:2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスが IoT Hub に対する認証を行うときにその証明書を提示するため、IoT Hub ではそれらが拇印と一致することを確認できます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、対称キー認証を使用した登録とプロビジョニングのプロセスについて説明します。 X.509 証明書でデバイスを設定する方法については、「[Azure IoT Edge デバイスに X.509 証明書認証を設定する](how-to-manual-provision-x509.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事の手順に従う前に、デバイスに IoT Edge ランタイムをインストールしておく必要があります。 行っていない場合は、「[Azure IoT Edge ランタイムをインストールまたはアンインストールする](how-to-install-iot-edge.md)」の手順に従います。

## <a name="register-a-new-device"></a>新しいデバイスを登録する

IoT Hub に接続するすべてのデバイスには、cloud-to-device または device-to-cloud 通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

対称キー認証の場合、この情報は、IoT Hub から取得して IoT Edge デバイスに配置できる "*接続文字列*" に集約されます。

IoT Hub に新しい IoT Edge デバイスを登録し、その接続文字列を取得するために、好みに応じていくつかのツールを使用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure portal の前提条件

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを作成する

Azure portal の IoT Hub で、IoT Edge デバイスは、エッジ対応ではない IOT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。
   * 認証の種類として **[対称キー]** を選択します。
   * 認証キーを自動生成して、新しいデバイスをハブに接続するために、既定の設定を使用します。

1. **[保存]** を選択します。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを表示する

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![Azure portal を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Azure portal で接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. ポータルの **IoT Edge** ページで、IoT Edge デバイスの一覧からデバイス ID をクリックします。
2. **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のどちらかの値をコピーします。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Visual Studio Code の前提条件

* Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)
* [Visual Studio Code](https://code.visualstudio.com/)
* Visual Studio Code 用 [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="sign-in-to-access-your-iot-hub"></a>サインインして IoT ハブにアクセスする

Visual Studio Code 用の Azure IoT 拡張機能を使用して、IoT Hub で操作を実行できます。 これらの操作を行うには、Azure アカウントにサインインし、IoT Hub を選択する必要があります。

1. Visual Studio Code で**エクスプローラー** ビューを開きます。
1. エクスプローラーの下部で、 **[Azure IoT Hub]** セクションを展開します。

   ![[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\) セクションを展開する](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

1. **[Azure IoT Hub]** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Select IoT Hub]\(IoT ハブの選択\)** を選択します。
1. Azure アカウントにサインインしていない場合は、プロンプトに従ってサインインします。
1. Azure サブスクリプションを選択します。
1. IoT ハブを選択します。

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Visual Studio Code を使用して IoT Edge デバイスを作成する

1. VS Code エクスプローラーで、 **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクションを展開します。
1. **[Azure IoT Hub Devices]\(Azure IoT Hub デバイス\)** セクション ヘッダーで **[...]** をクリックします。 省略記号が表示されない場合は、ヘッダーをクリックするかポイントします。
1. **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。
1. 表示されたテキスト ボックスにデバイス ID を指定します。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

コマンドの結果が出力画面に表示されます。 デバイスの情報が出力されます。この情報には、指定した **deviceId** および IoT ハブに物理デバイスを接続するために使用できる **connectionString** が含まれます。

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Visual Studio Code を使用して IoT Edge デバイスを表示する

IoT ハブに接続するすべてのデバイスは、Visual Studio Code エクスプローラーの **[Azure IoT Hub]** セクションに表示されます。 IoT Edge デバイスとそれ以外のデバイスはアイコンで区別できるほか、各 IoT Edge デバイスには **$edgeAgent** モジュールと **$edgeHub** モジュールがデプロイされています。

![VS Code を使用して IoT ハブ内の IoT Edge デバイスをすべて表示する](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Visual Studio Code を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。

1. **[Azure IoT Hub]** セクションでデバイスの ID を右クリックします。
1. **[Copy Device Connection String]\(デバイス接続文字列のコピー\)** を選択します。

   接続文字列がクリップボードにコピーされます。

右クリック メニューから **[Get Device Info]\(デバイス情報の取得\)** を選択して、接続文字列を含むすべてのデバイス情報を出力ウィンドウに表示することもできます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを作成する

[az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

このコマンドには、3 つのパラメーターが含まれます。

* `--device-id` または `-d`:IoT ハブに対して一意のわかりやすい名前を指定します。
* `hub-name` または `-n`:IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`:デバイスが IoT Edge デバイスであることを宣言します。

   ![az iot hub device-identity create output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを表示する

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Azure CLI を使用して接続文字列を取得する

デバイスを設定する準備ができたら、物理デバイスを IoT ハブ内でのその ID にリンクする接続文字列が必要です。 [az iot hub device-identity show-connection-string](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) コマンドを使用すると、単一のデバイスの接続文字列が返されます。

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

`device-id` パラメーターの値は、大文字と小文字が区別されます。 接続文字列を囲む引用符はコピーしないでください。

---

## <a name="provision-an-iot-edge-device"></a>IoT Edge デバイスのプロビジョニング

IoT Edge デバイスに対して IoT Hub 内の ID と、認証に使用できる接続文字列を用意したら、この情報を使用してデバイス自体をプロビジョニングする必要があります。

Linux デバイスでは、config.yaml ファイルを編集して接続文字列を指定します。 Windows デバイスでは、PowerShell スクリプトを実行して接続文字列を指定します。

# <a name="linux"></a>[Linux](#tab/linux)

IoT Edge デバイスで構成ファイルを開きます。

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルのプロビジョニング構成を見つけ、「**Manual provisioning configuration using a connection string**」セクションをコメント解除します。 

**device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Linux コンテナーを使用している場合は、`-ContainerOs` パラメーターをフラグに追加します。 前に実行した `Deploy-IoTEdge` コマンドで選択したコンテナー オプションと一致させてください。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 入力を求められたら、前のセクションで取得したデバイス接続文字列を指定します。 このデバイス接続文字列により、物理デバイスと IoT Hub のデバイス ID が関連付けられ、認証情報が提供されます。

   デバイス接続文字列は次の形式をとります。疑問符は入れないでください。`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、「[Windows 上の IoT Edge 用の PowerShell スクリプト](reference-windows-scripts.md)」を参照してください。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。