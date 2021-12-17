---
title: 対称キーを使用して IoT Edge for Linux on Windows デバイスを作成およびプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーを使用した手動プロビジョニングを使用して、IoT Hub に 1 つの IoT Edge for Linux on Windows デバイスを作成およびプロビジョニングする
author: kgremban
ms.reviewer: v-tcassi
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: kgremban
monikerRange: iotedge-2018-06
ms.openlocfilehash: 17e40216eae6fee6e013139eb792b5746f0c5b40
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131577614"
---
# <a name="create-and-provision-an-iot-edge-for-linux-on-windows-device-using-symmetric-keys"></a>対称キーを使用して IoT Edge for Linux on Windows デバイスを作成してプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、IoT Edge for Linux on Windows デバイスを登録およびプロビジョニングするエンド ツー エンドの手順について説明します。

IoT ハブに接続するすべてのデバイスには、cloud-to-device または device-to-cloud の通信の追跡に使用されるデバイス ID があります。 デバイスをその接続情報で構成します。これには、IoT ハブ ホスト名、デバイス ID、IoT Hub に対して認証を行うためにデバイスで使用される情報が含まれます。

この記事の手順では、手動プロビジョニングと呼ばれるプロセスについて説明します。ここでは、1 つのデバイスを IoT ハブに接続します。 手動プロビジョニングの場合、IoT Edge デバイスの認証には 2 つのオプションがあります。

* **対称キー**: IoT Hub で新しいデバイス ID を作成すると、サービスによって 2 つのキーが作成されます。 いずれかのキーをデバイスに配置すると、認証時にそのキーが IoT Hub に提供されます。

  この認証方法は比較的すばやく開始できますが、それほど安全ではありません。

* **X.509 自己署名**: 2 つの X.509 ID 証明書を作成し、デバイスに配置します。 IoT Hub で新しいデバイス ID を作成するときは、両方の証明書の拇印を指定します。 デバイスでは IoT Hub に対して認証を行うときに、1 つの証明書が提示され、その証明書がその拇印と一致することが IoT によって検証されます。

  この認証方法はより安全であり、運用環境のシナリオの場合に推奨されます。

この記事では、認証方法として対称キーを使用する方法について説明します。 X.509 証明書を使用する場合は、「[x.509 証明書を使用して IoT Edge for Linux on Windows デバイスを作成およびプロビジョニングする](how-to-provision-single-device-linux-on-windows-x509.md)」を参照してください。

> [!NOTE]
> 設定するデバイスが多数あり、それぞれを手動でプロビジョニングしたくない場合は、次の記事のいずれかを使用して、IoT Edge が IoT Hub Device Provisioning Service でどのように動作するかを確認してください。
>
> * [X.509 証明書を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
> * [TPM を使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
> * [対称キーを使用して IoT Edge デバイスを大規模に作成およびプロビジョニングする](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

## <a name="prerequisites"></a>前提条件

この記事では、IoT Edge デバイスを登録し、IoT Edge for Linux on Windows をインストールする方法について説明します。 これらのタスクには、実行するために使用されるさまざまな前提条件とユーティリティがあります。 次に進む前に、すべての前提条件が満たされていることを確認してください。

<!-- Device registration prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-register-device.md](../../includes/iot-edge-prerequisites-register-device.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Register your device and View provisioning information H2s and content -->
[!INCLUDE [iot-edge-register-device-symmetric.md](../../includes/iot-edge-register-device-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

クラウド ID と認証情報を使用してデバイスを設定する準備ができました。

対称キーを使用してデバイスをプロビジョニングするには、デバイスの **接続文字列** が必要です。

Windows Admin Center または管理者特権の PowerShell セッションを使用して、デバイスをプロビジョニングできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

管理者特権の PowerShell セッションで、ターゲット デバイスに対して次のコマンドを実行します。 プレースホルダー テキストは実際の値に置き換えてください。

```powershell
Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
```

`Provision-EflowVM` コマンドの詳細については、「[IoT Edge for Linux on Windows 用の PowerShell 関数](reference-iot-edge-for-linux-on-windows-functions.md#provision-eflowvm)」を参照してください。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Connection String (Manual)]\(接続文字列 (手動)\)** を選択します。

1. デバイスの登録後、IoT Hub から取得した **デバイスの接続文字列** を指定します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニングする\)** を選択します。

   ![デバイスの接続文字列を貼り付けた後、[Provisioning with the selected method]\(選択した方法でプロビジョニング\) を選択する](./media/how-to-provision-single-device-linux-on-windows-symmetric/provisioning-with-selected-method-connection-string.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されるはずです。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-configuration"></a>構成が成功したことを確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. PowerShell セッションで次のコマンドを使用して、お使いの IoT Edge for Linux on Windows 仮想マシンにログインします。

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >仮想マシンへの SSH 接続が許可されるアカウントは、それを作成したユーザーだけです。

1. ログインすると、次の Linux コマンドを使用して、実行中の IoT Edge モジュールの一覧を確認できます。

   ```bash
   sudo iotedge list
   ```

1. IoT Edge サービスのトラブルシューティングを行う必要がある場合は、次の Linux コマンドを使用します。

    1. サービスのログを取得します。

       ```bash
       sudo journalctl -u iotedge
       ```

    2. `check` ツールを使用して、デバイスの構成と接続の状態を確認します。

       ```bash
       sudo iotedge check
       ```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Windows Admin Center で接続されているデバイスの一覧から IoT Edge デバイスを選択して、それに接続します。

1. デバイスの概要ページに、デバイスに関するいくつかの情報が表示されます。

   * **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** セクションに、デバイスで実行中のモジュールが表示されます。 IoT Edge サービスが初めて開始されたときは、**edgeAgent** モジュールだけが実行中であると表示されるはずです。 edgeAgent モジュールが既定で実行され、デバイスにデプロイする追加モジュールのインストールと起動を支援します。

   * **[IoT Edge Status]\(IoT Edge の状態\)** セクションにはサービスの状態が表示され、 **[active (running)]\(アクティブ (実行中)\)** と報告されるはずです。

---

新しい IoT Edge デバイスを作成すると、Azure portal で状態コード `417 -- The device's deployment configuration is not set` が表示されます。 これは正常な状態であり、デバイスにモジュールをデプロイできる状態であることを表します。

## <a name="next-steps"></a>次のステップ

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
