---
title: Linux に Azure IoT Edge をインストールする方法 | Microsoft Docs
description: Linux に Azure IoT Edge をインストールする手順
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: kgremban
ms.openlocfilehash: be8fb801acfb625685604a302051d813bfd97939
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325304"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Linux に Azure IoT Edge ランタイムをインストールする (x64)

Azure IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイス上のセキュリティ標準を提供し、維持します。 デーモンはブートのたびに開始し、IoT Edge エージェントを開始することでデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。

この記事では、Linux x64 (Intel/AMD) Edge デバイスに Azure IoT Edge ランタイムをインストールする手順を示します。

>[!NOTE]
>Linux ソフトウェア リポジトリ内のパッケージは、各パッケージ (/usr/share/doc/*パッケージ名*) 内にあるライセンス条項の対象となります。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

## <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft キーとソフトウェア リポジトリ フィードを登録する

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/

# Perform apt upgrade
sudo apt-get upgrade
```

## <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする 

Azure IoT Edge は、[OCI と互換性のある][lnk-oci]コンテナー ランタイムに依存します。 実稼働環境シナリオでは、以下の [Moby ベース][lnk-moby] エンジンを使用することを強くお勧めします。 これは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと互換性があります。

apt-get を更新します。

```bash
sudo apt-get update
```

Moby エンジンをインストールします。 

```bash
sudo apt-get install moby-engine
```

Moby コマンドライン インターフェイス (CLI) をインストールします。 CLI は開発には役立ちますが、実稼働環境には省略可能です。

```bash
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

下のコマンドでは、まだ存在しない場合は、**iothsmlib** の標準バージョンもインストールされます。

```bash
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンの構成

デーモンは、`/etc/iotedge/config.yaml` にある構成ファイルを使用して構成できます。 このファイルは既定で書き込み禁止になっています。編集するには管理者特権が必要な場合があります。

```bash
sudo nano /etc/iotedge/config.yaml
```

Edge デバイスは、[デバイスの接続文字列][lnk-dcs]を使用して手動で構成することも、[Device Provisioning Service を介して自動的に][lnk-dps]構成することもできます。

* 手動構成の場合は、**manual** プロビジョニング モードのコメントを解除します。 **device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning: 
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

* 自動構成の場合は、**dps** プロビジョニング モードのコメントを解除します。 **scope_id** と **registration_id** の値を、IoT Hub DPS インスタンスと TPM を搭載した IoT Edge デバイスの値で更新します。 

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning: 
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

構成にプロビジョニング情報を入力してから、デーモンを再起動します。

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

前のセクションで**手動構成**手順を使用した場合、IoT Edge ランタイムがデバイス上で正常にプロビジョニングおよび実行されている必要があります。 **自動構成**手順を使用した場合は、ランタイムが IoT ハブにデバイスを登録できるように、追加の手順を完了する必要があります。 次の手順については、[Linux 仮想マシンでのシミュレートされた TPM Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)に関する記事をご覧ください。

以下を使用して、IoT Edge デーモンの状態を確認できます。

```cmd/sh
systemctl status iotedge
```

以下を使用して、デーモンのログを確認します。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

また、以下を使用して、実行中のモジュールを一覧表示します。

```cmd/sh
sudo iotedge list
```

## <a name="next-steps"></a>次の手順

Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング][lnk-trouble]のページを調べてください。

<!-- Links -->
[lnk-dcs]: how-to-register-device-portal.md
[lnk-dps]: how-to-auto-provision-simulated-device-linux.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md
