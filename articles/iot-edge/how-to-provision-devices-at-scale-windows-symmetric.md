---
title: Windows で対称キーを使用して IoT Edge デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーの構成証明を使用して、Azure IoT Edge とデバイス プロビジョニング サービスに対して大規模に Windows デバイスのプロビジョニングをテストする
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a1abccf4a039a36f2969e6b32b8eca2478d07427
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845945"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-windows-using-symmetric-keys"></a>対称キーを使用して Windows で大規模に IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、対称キーを使用して 1 つ以上の Windows IoT Edge デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を確認してから先に進んでください。

>[!NOTE]
>Windows コンテナーを使用する Azure IoT Edge は、Azure IoT Edge のバージョン 1.2 以降サポートされなくなります。
>
>IoT Edge を Windows デバイスで実行するための新しい方法である [Azure IoT Edge for Linux on Windows](iot-edge-for-linux-on-windows.md) の使用を検討してください。
>
>Azure IoT Edge for Linux on Windows を使用する場合は、[同等のハウツーガイド](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)に記載されている手順に従います。

タスクは次のとおりです。

1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムをインストールし、IoT Hub に接続する。

対称キーの構成証明は、デバイス プロビジョニング サービス インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。 <!-- note links here; they will break -->

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

IoT Edge デバイスとなる物理デバイスまたは仮想 Windows デバイス。

各デバイスを識別するために、"*一意*" の **登録 ID** を定義する必要があります。 デバイスの MAC アドレス、シリアル番号、または何らかの固有の情報を使用できます。 この例では、MAC アドレスとシリアル番号の組み合わせを使用して、次のような登録 ID の文字列を形成します: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。 有効な文字は、小文字の英字、数字、ダッシュ (`-`) です。

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Windows H2 and content -->
[!INCLUDE [install-iot-edge-windows.md](../../includes/iot-edge-install-windows.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するための情報を使用してデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* 個別登録の **主キー**、またはグループ登録を使用するデバイスの [派生キー](#derive-a-device-key)のいずれか。

1. 管理者モードで PowerShell ウィンドウを開きます。 IoT Edge をインストールするときは、PowerShell (x86) ではなく、PowerShell の AMD64 セッションを必ず使用してください。

1. **Initialize-IoTEdge** コマンドを使用して、お使いのマシンに IoT Edge ランタイムを構成します。 このコマンドでは、Windows コンテナーを使用した手動プロビジョニングが既定で設定されます。そのため、`-DpsSymmetricKey` フラグを使用し、対称キー認証と共に自動プロビジョニングを使用します。

   `paste_scope_id_here`、`paste_registration_id_here`、および `paste_symmetric_key_here` のプレースホルダー値を、前に収集したデータに置き換えます。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId paste_scope_id_here -RegistrationId paste_registration_id_here -SymmetricKey paste_symmetric key_here
   ```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

デバイス プロビジョニング サービスで作成した個々の登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

デバイス プロビジョニング サービスで作成したグループ登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

IoT Edge が正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

IoT Edge サービスの状態を確認します。

```powershell
Get-Service iotedge
```

サービス ログを調べます。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

実行中のモジュールを一覧表示します。

```powershell
iotedge list
```

## <a name="next-steps"></a>次のステップ

デバイス プロビジョニング サービスの登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
