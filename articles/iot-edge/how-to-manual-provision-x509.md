---
title: X.509 証明書によるプロビジョニング - Azure IoT Edge | Microsoft Docs
description: インストールしたら、IoT Edge デバイスにそのデバイス ID 証明書をプロビジョニングして、IoT Hub に対する認証を行います
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b1aa12bd73772b5d6332a36d749ec4d7d10d4026
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048187"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Azure IoT Edge デバイスに X.509 証明書認証を設定する

この記事では、IoT Hub に新しい IoT Edge デバイスを登録し、X.509 証明書で認証するようにデバイスを構成するステップについて説明します。

この記事のステップでは、手動プロビジョニングと呼ばれるプロセスを順を追って説明します。ここでは、各デバイスを IoT ハブに手動で接続します。 IoT Hub Device Provisioning Service を使用して自動的にプロビジョニングする方法もあります。これは、プロビジョニングするデバイスが多数ある場合に便利です。

<!--TODO: Add auto-provision info/links-->

手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**: IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**: 2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスが IoT Hub に対して認証されると、その証明書が提示され、それらが拇印と一致することが IoT Hub によって確認できます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、X.509 証明書認証を使用した登録とプロビジョニングのプロセスについて説明します。 対称キーでデバイスを設定する方法については、「[対称キー認証で Azure IoT Edge デバイスを設定する](how-to-manual-provision-symmetric-key.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

この記事のステップに従う前に、IoT Edge ランタイムがインストールされているデバイスを用意しておく必要があります。 ない場合は、「[Azure IoT Edge ランタイムをインストールまたはアンインストールする](how-to-install-iot-edge.md)」の手順に従います。

X.509 証明書を使用した手動プロビジョニングには、バージョン 1.0.10 以降の IoT Edge が必要です。

## <a name="create-certificates-and-thumbprints"></a>証明書と拇印を作成する



<!-- TODO -->

## <a name="register-a-new-device"></a>新しいデバイスを登録する

IoT Hub に接続するすべてのデバイスには、cloud-to-device または device-to-cloud 通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

X.509 証明書認証の場合、この情報は、デバイス ID 証明書から取得した "*拇印*" の形で提供されます。 これらの拇印は、サービスが接続時にデバイスを認識できるように、デバイスの登録時に IoT Hub に付与されます。

複数のツールを使用して IoT Hub に新しい IoT Edge デバイスを登録し、その証明書の拇印をアップロードできます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Azure portal の前提条件

Azure サブスクリプション内の無料または標準の [IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを作成する

Azure portal の IoT Hub で、IoT Edge デバイスは、エッジ対応ではない IOT デバイスとは別に作成および管理されます。

1. [Azure Portal](https://portal.azure.com) にサインインし、IoT Hub に移動します。

1. 左側のペインで、メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスを追加する]** を選択します。

   ![Azure portal から IoT Edge デバイスを追加する](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. **[デバイスの作成]** ページで、次の情報を指定します。

   * わかりやすいデバイス ID を作成します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
   * 認証の種類として **[X.509 自己署名済み]** を選択します。
   * プライマリとセカンダリの ID 証明書の拇印を指定します。 拇印の値は SHA-1 ハッシュの場合は 40 桁の 16 進数、SHA-256 ハッシュの場合は 64 桁の 16 進数です。

1. **[保存]** を選択します。

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Azure portal で IoT Edge デバイスを表示する

IoT ハブに接続するすべてのエッジ対応デバイスが、**IoT Edge** ページに一覧表示されます。

![IoT ハブ内のすべての IoT Edge デバイスを表示する](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプション内の [IoT ハブ](../iot-hub/iot-hub-create-using-cli.md)。
* ご使用の環境内の [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI のバージョンは、少なくとも 2.0.70 以降である必要があります。 検証するには、`az --version` を使用します。 このバージョンでは、az 拡張機能のコマンドがサポートされ、Knack コマンド フレームワークが導入されています。
* [Azure CLI 向け IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)。

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを作成する

[az iot hub device-identity create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) コマンドを使用して、IoT ハブに新しいデバイス ID を作成します。 次に例を示します。

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

このコマンドには、いくつかのパラメーターが含まれています。

* `--device-id` または `-d`: IoT ハブに対して一意のわかりやすい名前を指定します。 このデバイス ID は、次のセクションで使用するのでメモしておいてください。
* `hub-name` または `-n`: IoT ハブの名前を指定します。
* `--edge-enabled` または `--ee`: デバイスが IoT Edge デバイスであることを宣言します。
* `--auth-method` または `--am`: デバイスが使用する承認の種類を宣言します。 この例では、X.509 証明書の拇印を使用しています。
* `--primary-thumbprint` または `--ptp`: 主キーとして使用する X.509 証明書の拇印を指定します。
* `--secondary-thumbprint` または `--stp`: セカンダリ キーとして使用する X.509 証明書の拇印を指定します。

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Azure CLI を使用して IoT Edge デバイスを表示する

IoT ハブのすべてのデバイスを表示するには、[az iot hub device-identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) コマンドを使用します。 次に例を示します。

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

IoT ハブ内の IoT Edge デバイスのみを一覧表示するには、フラグ `--edge-enabled` または `--ee` を追加します。

IoT Edge デバイスとして登録されたすべてのデバイスのプロパティ **capabilities.iotEdge** は **true** に設定されます。

--- 

## <a name="configure-an-iot-edge-device"></a>IoT Edge デバイスの構成

IoT Edge デバイスに IoT Hub の ID が設定されたら、その ID 証明書だけでなく、クラウド ID でデバイスを構成する必要があります。

Linux デバイスでは、config.yaml ファイルを編集してこの情報を指定します。 Windows デバイスでは、PowerShell スクリプトを実行してこの情報を指定します。

# <a name="linux"></a>[Linux](#tab/linux)

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 

1. **Manual provisioning configuration using a connection string** セクションをコメントアウトします。

1. **Manual provisioning configuration using an X.509 identity certificate** セクションをコメント解除します。 **provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. 次のフィールドを更新します。

   * **iothub_hostname**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT hub name}.azure-devices.net` のようにします。
   * **device_id**: デバイスを登録したときに指定した ID。
   * **identity_cert**: デバイス上の ID 証明書の URI。 たとえば、`file:///path/identity_certificate.pem` のようにします。
   * **identity_pk**: 指定された ID 証明書の秘密キー ファイルの URI。 たとえば、`file:///path/identity_key.pem` のようにします。

1. ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

1. 構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. IoT Edge デバイスで、PowerShell を管理者として実行します。

2. [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Linux コンテナーを使用している場合は、`-ContainerOs` パラメーターをフラグに追加します。 前に実行した `Deploy-IoTEdge` コマンドで選択したコンテナー オプションと一致させてください。

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * オフラインまたは特定のバージョンのインストール用に IoTEdgeSecurityDaemon.ps1 スクリプトをデバイスにダウンロードした場合は、スクリプトのローカル コピーを必ず参照してください。

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. 入力を求められたら、次の情報を指定します。

   * **IotHubHostName**: デバイスの接続先となる IoT ハブのホスト名。 たとえば、`{IoT hub name}.azure-devices.net` のようにします。
   * **DeviceId**: デバイスを登録したときに指定した ID。
   * **X509IdentityCertificate**: デバイス上の ID 証明書の絶対パス。 たとえば、`C:\path\identity_certificate.pem` のようにします。
   * **X509IdentityPrivateKey**: 指定された ID 証明書の秘密キー ファイルの絶対パス。 たとえば、`C:\path\identity_key.pem` のようにします。

デバイスを手動でプロビジョニングする場合は、追加のパラメーターを使用して、プロセスを次のように変更できます。

* プロキシ サーバーを経由するようトラフィックを誘導する
* 特定の edgeAgent コンテナー イメージを宣言し、それがプライベート レジストリに存在する場合は資格情報を提供する

これらの追加パラメーターの詳細については、「[PowerShell scripts for IoT Edge on Windows](reference-windows-scripts.md)」(Windows 上の IoT Edge 用の PowerShell スクリプト) を参照してください。

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>次のステップ

[IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。