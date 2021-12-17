---
title: Linux で対称キーを使用して IoT Edge デバイスを作成してプロビジョニングする - Azure IoT Edge | Microsoft Docs
description: 対称キーの構成証明を使用して、Azure IoT Edge とデバイス プロビジョニング サービスに対して大規模に Linux デバイスのプロビジョニングをテストする
author: kgremban
ms.author: kgremban
ms.reviewer: v-tcassi
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a0b1a73c45c5c08eaf3c172900ade531c6fedda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505948"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-symmetric-key"></a>対称キーを使用して Linux で大規模に IoT Edge デバイスを作成およびプロビジョニングする

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

この記事では、対称キーを使用して 1 つ以上の Linux IoT Edge デバイスを自動プロビジョニングするエンド ツー エンドの手順について説明します。 Azure IoT Edge デバイスは、[Azure IoT Hub デバイス プロビジョニング サービス](../iot-dps/index.yml) (DPS) を使用して自動プロビジョニングできます。 自動プロビジョニングの処理に慣れていない場合は、[プロビジョニングの概要](../iot-dps/about-iot-dps.md#provisioning-process)を確認してから先に進んでください。

タスクは次のとおりです。

1. 単一のデバイスの **個別登録** を作成するか、一連のデバイスに対して **グループ登録** を作成する。
1. IoT Edge ランタイムをインストールし、IoT Hub に接続する。

:::moniker range=">=iotedge-2020-11"
>[!TIP]
>簡素化されたエクスペリエンスのために、[Azure IoT Edge 構成ツール](https://github.com/azure/iot-edge-config)をお試しください。 現在パブリック プレビュー中のこのコマンド ライン ツールは、デバイスに IoT Edge をインストールし、DPS と対称キーの構成証明を使用してプロビジョニングします。
:::moniker-end

対称キーの構成証明は、デバイス プロビジョニング サービス インスタンスを使用してデバイスを認証する簡単なアプローチです。 この構成証明の方法では、初めてデバイスのプロビジョニングを行う開発者または厳密なセキュリティ要件がない開発者に対して、"Hello world" エクスペリエンスを提示します。 [TPM](../iot-dps/concepts-tpm-attestation.md) または [X.509 証明書](../iot-dps/concepts-x509-attestation.md)を使用するデバイス構成証明は、より安全であり、さらに厳格なセキュリティ要件に対して使用する必要があります。

## <a name="prerequisites"></a>前提条件

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>IoT Edge のインストール

IoT Edge デバイスとなる物理デバイスまたは仮想 Linux デバイス。

各デバイスを識別するために、一意の **登録 ID** を定義する必要があります。 デバイスの MAC アドレス、シリアル番号、または何らかの固有の情報を使用できます。 この例では、MAC アドレスとシリアル番号の組み合わせを使用して、次のような登録 ID の文字列を形成します: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。 有効な文字は、小文字の英字、数字、ダッシュ (`-`) です。

<!-- Create a DPS enrollment using symmetric keys H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-symmetric.md](../../includes/iot-edge-create-dps-enrollment-symmetric.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>クラウド ID を使用してデバイスをプロビジョニングする

ランタイムがデバイスにインストールされたら、デバイス プロビジョニング サービスと IoT Hub に接続するための情報を使用してデバイスを構成します。

次の情報を用意しておきます。

* DPS の **ID スコープ** 値
* 作成したデバイス **登録 ID**
* 個別登録の **主キー**、またはグループ登録を使用するデバイスの [派生キー](#derive-a-device-key)のいずれか。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成セクションを見つけます。 DPS 対称キーのプロビジョニング行のコメントを解除し、他にもプロビジョニング行があれば、それらのコメントが解除されていることを確認します。

   `provisioning:` の行の先頭には空白文字を入れず、入れ子の項目には 2 つの空白でインデントする必要があります。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "PASTE_YOUR_SCOPE_ID_HERE"
     attestation:
       method: "symmetric_key"
       registration_id: "PASTE_YOUR_REGISTRATION_ID_HERE"
       symmetric_key: "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. `scope_id`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

1. 必要に応じて、`always_reprovision_on_startup` または `dynamic_reprovisioning` の行を使用して、デバイスの再プロビジョニング動作を構成します。 起動時に再プロビジョニングするようにデバイスが設定されている場合、常に最初に DPS を使用してプロビジョニングが試行され、失敗した場合はプロビジョニングのバックアップにフォールバックします。 動的に再プロビジョニングするようにデバイスが設定されている場合、再プロビジョニング イベントが検出されると、IoT Edge が再起動し、再プロビジョニングされます。 詳細については、「[IoT Hub デバイスの再プロビジョニングの概念](../iot-dps/concepts-device-reprovision.md)」を参照してください。

1. デバイスで行ったすべての構成の変更が反映されるように、IoT Edge ランタイムを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. IoT Edge のインストールの一部として提供されるテンプレート ファイルに基づいて、デバイスの構成ファイルを作成します。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. IoT Edge デバイスで構成ファイルを開きます。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. ファイルの **プロビジョニング** セクションを見つけます。 対称キーによる DPS のプロビジョニング行をコメント解除し、他にもプロビジョニング行があれば、それらもコメント アウトするようにします。

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "PASTE_YOUR_SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "PASTE_YOUR_REGISTRATION_ID_HERE"

   symmetric_key = "PASTE_YOUR_PRIMARY_KEY_OR_DERIVED_KEY_HERE"
   ```

1. `id_scope`、`registration_id`、`symmetric_key` の値を DPS およびデバイス情報で更新します。

   対称キー パラメーターは、インライン キー、ファイル URI、または PKCS#11 URI の値を受け入れることができます。 使用している形式に基づいて、1 つの対称キー行だけをコメント解除します。

   任意の PKCS#11 URI を使用する場合は、構成ファイルで **PKCS#11** セクションを見つけ、PKCS#11 構成に関する情報を提供します。

1. config.toml ファイルを保存して閉じます。

1. 加えた構成の変更を IoT Edge に適用します。

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>インストールの成功を確認する

ランタイムが正常に起動されたら、IoT Hub にアクセスし、デバイスに IoT Edge モジュールを展開できます。

# <a name="individual-enrollment"></a>[個別加入](#tab/individual-enrollment)

デバイス プロビジョニング サービスで作成した個々の登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成した個々の登録の詳細を開きます。 登録の状態が **割り当て** られており、デバイス ID が表示されています。

# <a name="group-enrollment"></a>[グループ登録](#tab/group-enrollment)

デバイス プロビジョニング サービスで作成したグループ登録が使用されたことを確認できます。 Azure portal でデバイス プロビジョニング サービス インスタンスに移動します。 作成したグループ登録の詳細を開きます。 **[登録レコード]** タブにアクセスして、そのグループに登録されているすべてのデバイスを表示します。

---

IoT Edge が正常にインストールされ、起動されたことを確認するには、デバイスで次のコマンドを使用します。

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

IoT Edge サービスの状態を確認します。

```cmd/sh
systemctl status iotedge
```

サービス ログを調べます。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

実行中のモジュールを一覧表示します。

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge サービスの状態を確認します。

```cmd/sh
sudo iotedge system status
```

サービス ログを調べます。

```cmd/sh
sudo iotedge system logs
```

実行中のモジュールを一覧表示します。

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>次のステップ

デバイス プロビジョニング サービスの登録プロセスでは、新しいデバイスをプロビジョニングするときに、デバイス ID とデバイス ツイン タグを同時に設定できます。 これらの値を使用して、個々のデバイスまたはデバイス グループをデバイスの自動管理で使用できます。 使用方法については、「[Azure Portal を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-at-scale.md)」または「[Azure CLI を使用した大規模な IoT Edge モジュールの展開と監視](how-to-deploy-cli-at-scale.md)」を参照してください。
