---
title: Linux に Azure IoT Edge をインストールする方法 | Microsoft Docs
description: Linux に Azure IoT Edge をインストールする手順
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: kgremban
ms.openlocfilehash: 43f82341a3cc9d2163afd35e42864aaa7866b1b2
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035707"
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
```

### <a name="debian-9"></a>Debian 9

```cmd/sh
# Install repository configuration
curl https://packages.microsoft.com/config/debian/9/prod.list > ./microsoft-prod.list
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

# Install Microsoft GPG public key
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

## <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする 

Azure IoT Edge は、[OCI と互換性のある][lnk-oci]コンテナー ランタイム (Docker など) に依存します。 Edge デバイスに Docker CE/EE が既にインストールされている場合は、引き続き Azure IoT Edge を使用して開発とテストを行うことができます。 

実稼働環境シナリオでは、以下の [Moby ベース][lnk-moby] エンジンを使用することを強くお勧めします。 これは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ランタイムと完全に互換性があります。

*以下の手順で、moby エンジンとコマンドライン インターフェイス (CLI) の両方がインストールされます。CLI は開発には役立ちますが、実稼働環境には省略可能です。*

```cmd/sh
sudo apt-get update
sudo apt-get install moby-engine
sudo apt-get install moby-cli
```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンのインストール

下のコマンドでは、まだ存在しない場合は、**iothsmlib** の標準バージョンもインストールされます。

```cmd/sh
sudo apt-get update
sudo apt-get install iotedge
```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンの構成

`/etc/iotedge/config.yaml` にある構成ファイルを使用して、デーモンを構成できます。エッジ デバイスは、<!--[automatically via Device Provisioning Service][lnk-dps] or-->[デバイス接続文字列][lnk-dcs]を使用して手動で構成できます。

手動で構成する場合は、デバイス接続文字列を **config.yaml** の **provisioning** セクションに入力します。

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
