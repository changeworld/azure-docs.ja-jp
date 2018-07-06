---
title: Linux に Azure IoT Edge をインストールする方法 | Microsoft Docs
description: ARM32 上の Linux に Azure IoT Edge をインストールする手順
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: ad70fcc6b9779cb33772a3fce2fb11b4cec804ee
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37062600"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Linux に Azure IoT Edge ランタイムをインストールする (ARM32v7/armhf)

Azure IoT Edge ランタイムはすべての IoT Edge デバイスに展開されます。 これは 3 つのコンポーネントで構成されます。 **IoT Edge セキュリティ デーモン**は、Edge デバイス上のセキュリティ標準を提供し、維持します。 デーモンはブートのたびに開始し、IoT Edge エージェントを開始することでデバイスをブートストラップします。 **IoT Edge エージェント**は、IoT Edge ハブなど、Edge デバイス上のモジュールの展開と監視を容易にします。 **IoT Edge ハブ**は、IoT Edge デバイス上のモジュール間、およびデバイスと IoT ハブの間の通信を管理します。

この記事では、Linux ARM32v7/armhf Edge デバイス (たとえば、Raspberry Pi) 上に Azure IoT Edge ランタイムをインストールする手順について説明します。

>[!NOTE]
>Linux ソフトウェア リポジトリ内のパッケージは、各パッケージ (/usr/share/doc/*パッケージ名*) 内にあるライセンス条項の対象となります。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

## <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする

Azure IoT Edge は、[OCI と互換性のある][lnk-oci]コンテナー ランタイム (Docker など) に依存します。 Edge デバイスに Docker CE/EE が既にインストールされている場合は、引き続き Azure IoT Edge を使用して開発とテストを行うことができます。 

実稼働環境シナリオでは、以下の [Moby ベース][lnk-moby] エンジンを使用することを強くお勧めします。 これは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと完全に互換性があります。

以下のコマンドで、moby エンジンとコマンドライン インターフェイス (CLI) の両方がインストールされます。 CLI は開発には役立ちますが、実稼働環境には省略可能です。

```cmd/sh

# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f

```

## <a name="install-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールする

```cmd/sh
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンを構成する

`/etc/iotedge/config.yaml` にある構成ファイルを使用して、デーモンを構成できます。エッジ デバイスは、<!--[automatically via Device Provisioning Service][lnk-dps] or-->[デバイス接続文字列][lnk-dcs]を使用して手動で構成できます。

手動で構成する場合は、デバイス接続文字列を **config.yaml** の **provisioning** セクションに入力します

```yaml
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

*ファイルは既定で書き込み禁止になっています。編集するには`sudo` を使用する必要があります。次に例を示します。`sudo nano /etc/iotedge/config.yaml`*

構成にプロビジョニング情報を入力してから、デーモンを再起動します。

```cmd/sh
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

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
iotedge list
```

## <a name="next-steps"></a>次の手順

Edge ランタイムの正常なインストールに問題がある場合は、[トラブルシューティング][lnk-trouble]のページをご確認ください。

<!-- Links -->
[lnk-dcs]: ../iot-hub/quickstart-send-telemetry-dotnet.md#register-a-device
[lnk-dps]: how-to-simulate-dps-tpm.md
[lnk-oci]: https://www.opencontainers.org/
[lnk-moby]: https://mobyproject.org/
[lnk-trouble]: troubleshoot.md