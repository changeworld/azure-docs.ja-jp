---
title: Linux on Windows で対称キーを使用して IoT Edge デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: デバイス プロビジョニング サービスで対称キーの構成証明を使用し、Azure IoT Edge に対する Linux on Windows デバイスの大規模プロビジョニングをテストします
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b11313c7ff443226352558ffb172c3bbf59964b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852125"
---
# <a name="create-and-provision-iot-edge-for-linux-on-windows-devices-at-scale-using-symmetric-keys"></a>対称キーを使用して IoT Edge for Linux on Windows デバイスを大規模に作成してプロビジョニングする 

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、対称キーを使用して 1 つ以上の [IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を確認してから先に進んでください。

タスクは次のとおりです。

1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムがインストールされた Linux 仮想マシンをデプロイし、それを IoT Hub に接続する。

対称キーの構成証明は、デバイス プロビジョニング サービス インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

<!-- IoT Edge for Linux on Windows installation prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-linux-on-windows.md](../../includes/iot-edge-prerequisites-linux-on-windows.md)]

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge for Linux on Windows H2 and content -->
[!INCLUDE [install-iot-edge-linux-on-windows.md](../../includes/iot-edge-install-linux-on-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するための情報を使用してデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* 個別登録の **主キー**、またはグループ登録を使用するデバイスの [派生キー](#derive-a-device-key)のいずれか。

PowerShell または Windows Admin Center を使用して、IoT Edge デバイスをプロビジョニングできます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell の場合は、プレースホルダーの値をご自身の値で更新して、次のコマンドを実行します。

```powershell
Provision-EflowVm -provisioningType DpsSymmetricKey -scopeId PASTE_YOUR_ID_SCOPE_HERE -registrationId PASTE_YOUR_REGISTRATION_ID_HERE -symmKey PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE
```

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Windows Admin Center の場合は、次の手順に従います。

1. **[Azure IoT Edge device provisioning]\(Azure IoT Edge デバイスのプロビジョニング\)** ペインで、プロビジョニング方法のドロップダウンから **[Symmetric Key (DPS)]\(対称キー (DPS)\)** を選択します。

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの DPS インスタンスに移動します。

1. DPS スコープ ID、デバイス登録 ID、および登録の主キーまたは派生キーを Windows Admin Center の各フィールドに入力します。

1. **[Provisioning with the selected method]\(選択した方法でプロビジョニング\)** を選択します。

   ![対称キーのプロビジョニングに必要なフィールドを入力した後、選択した方法でのプロビジョニングを選択する (PNG)。](./media/how-to-provision-devices-at-scale-linux-on-windows-symmetric/provisioning-with-selected-method-symmetric-key.png)

1. プロビジョニングが完了したら、 **[完了]** を選択します。 メイン ダッシュボードに自動的に戻ります。 これで、種類が `IoT Edge Devices` の新しいデバイスが一覧に表示されます。 IoT Edge デバイスを選択して、それに接続できます。 **[Overview]\(概要\)** ページでは、デバイスの **[IoT Edge Module List]\(IoT Edge モジュール一覧\)** と **[IoT Edge Status]\(IoT Edge の状態\)** を見ることができます。

---

## <a name="verify-successful-installation"></a>インストールの成功を確認する

IoT Edge for Linux on Windows が IoT Edge デバイスに正常にインストールされ、構成されたことを確認します。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

デバイス プロビジョニング サービスで作成した個々の登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

デバイス プロビジョニング サービスで作成したグループ登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

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

デバイス プロビジョニング サービスの登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。

次のこともできます。

* [IoT Edge モジュールのデプロイ](how-to-deploy-modules-portal.md)に進んで、デバイスにモジュールをデプロイする方法を確認します。
* [IoT Edge for Linux on Windows 仮想マシンで証明書を管理](how-to-manage-device-certificates.md)し、ホスト OS から Linux 仮想マシンにファイルを転送する方法について確認します。
* [プロキシ サーバー経由で通信するように IoT Edge デバイスを構成](how-to-configure-proxy-support.md)する方法について確認します。
