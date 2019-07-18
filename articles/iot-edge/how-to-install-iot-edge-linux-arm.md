---
title: Linux ARM32 に Azure IoT Edge をインストールする | Microsoft Docs
description: Raspberry PI のような ARM32 デバイス上の Linux に Azure IoT Edge をインストールする手順
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.openlocfilehash: f7004edf2bab0e22d4d1e4c1200d6e8b8ef729b3
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485945"
---
# <a name="install-azure-iot-edge-runtime-on-linux-arm32v7armhf"></a>Linux に Azure IoT Edge ランタイムをインストールする (ARM32v7/armhf)

Azure IoT Edge ランタイムを使用すると、デバイスを IoT Edge デバイスに変えることができます。 このランタイムは、Raspberry Pi のような小型デバイスにも、産業用サーバーのような大型デバイスにもデプロイすることができます。 IoT Edge ランタイムを使用してデバイスを構成すると、クラウドからデバイスへのビジネス ロジックのデプロイを開始できます。 

IoT Edge ランタイムの動作とランタイムに含まれるコンポーネントについては、「[Azure IoT Edge ランタイムとそのアーキテクチャの概要](iot-edge-runtime.md)」を参照してください。

この記事では、Linux ARM32v7/armhf IoT Edge デバイスに Azure IoT Edge ランタイムをインストールする手順について説明します。 たとえば、これらの手順は、Raspberry Pi デバイスに使用できます。 サポートされている ARM32 オペレーティング システムの一覧については、「[Azure IoT Edge のサポートされるシステム](support.md#operating-systems)」を参照してください。 

>[!NOTE]
>Linux ソフトウェア リポジトリ内のパッケージは、各パッケージ (/usr/share/doc/*パッケージ名*) 内にあるライセンス条項の対象となります。 パッケージを使用する前に、ライセンス条項をお読みください。 インストールし、パッケージを使用すると、これらの条項に同意したものと見なされます。 ライセンス条項に同意しない場合は、パッケージを使用しないでください。

## <a name="install-the-latest-version"></a>最新バージョンをインストールする

以下のセクションを使用して、最新バージョンの Azure IoT Edge サービスを Linux ARM デバイスにインストールします。 

### <a name="install-the-container-runtime"></a>コンテナー ランタイムをインストールする

Azure IoT Edge は、[OCI と互換性のある](https://www.opencontainers.org/)コンテナー ランタイムに依存します。 実稼働環境シナリオでは、以下の [Moby ベース](https://mobyproject.org/) エンジンを使用することを強くお勧めします。 これは、Azure IoT Edge で公式にサポートされている唯一のコンテナー エンジンです。 Docker CE/EE コンテナー イメージは、Moby ベースのランタイムと互換性があります。

以下のコマンドで、Moby ベースのエンジンとコマンドライン インターフェイス (CLI) の両方がインストールされます。 CLI は開発には役立ちますが、実稼働環境には省略可能です。

```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the moby-engine
curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb

# Download and install the moby-cli
curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb

# Run apt-get fix
sudo apt-get install -f
```

### <a name="install-the-iot-edge-security-daemon"></a>IoT Edge セキュリティ デーモンをインストールする

**IoT Edge セキュリティ デーモン**は、IoT Edge デバイス上のセキュリティ標準を提供し、維持します。 デーモンは起動のたびに開始され、IoT Edge ランタイムの残りの部分を開始することでデバイスをブートストラップします。 


```bash
# You can copy the entire text from this code block and 
# paste in terminal. The comment lines will be ignored.

# Download and install the standard libiothsm implementation
curl -L https://aka.ms/libiothsm-std-linux-armhf-latest -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb

# Download and install the IoT Edge Security Daemon
curl -L https://aka.ms/iotedged-linux-armhf-latest -o iotedge.deb && sudo dpkg -i ./iotedge.deb

# Run apt-get fix
sudo apt-get install -f
```

IoT Edge が正常にインストールされると、構成ファイルを更新するよう求められます。 「[Azure IoT Edge セキュリティ デーモンを構成する](#configure-the-azure-iot-edge-security-daemon)」の手順に従って、デバイスのプロビジョニングを完了します。 

## <a name="install-a-specific-version"></a>特定のバージョンをインストールする

特定のバージョンの Azure IoT Edge をインストールする場合は、IoT Edge GitHub リポジトリから直接コンポーネント ファイルを対象にすることができます。 前のセクションに示されている `curl` コマンドを使用して、すべての IoT Edge コンポーネントをデバイスに取得します。Moby エンジンと CLI、libiothsm、IoT Edge セキュリティ デーモンの順に取得します。 唯一の違いは、**aka.ms** の URL を、使用する各コンポーネントのバージョンを直接参照するリンクに置き換えることです。

[Azure IoT Edge リリース](https://github.com/Azure/azure-iotedge/releases)に移動し、対象とするリリース バージョンを見つけます。 そのバージョンの **[Assets]** セクションを展開し、IoT Edge デバイスのアーキテクチャに対応するファイルを選択します。 **iotedge** および **libiothsm** ファイルは、すべての IoT Edge リリースに含まれています。 **moby-engine** または **moby-cli** は、すべてのリリースに含まれているわけではありません。 Moby コンテナー エンジンがまだインストールされていない場合は、Moby コンポーネントが含まれているものが見つかるまで古いリリースを調べます。 

IoT Edge が正常にインストールされると、構成ファイルを更新するよう求められます。 次のセクションの手順に従って、デバイスのプロビジョニングを完了します。 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge セキュリティ デーモンを構成する

IoT Edge ランタイムを構成して、物理デバイスを Azure IoT ハブに存在するデバイス ID にリンクします。 

デーモンは、`/etc/iotedge/config.yaml` にある構成ファイルを使用して構成できます。 このファイルは既定で書き込み禁止になっているため、編集するには管理者特権が必要な場合があります。

IoT Hub によって提供されるデバイス接続文字列を使用して、1 つの IoT Edge デバイスを手動でプロビジョニングすることもできますし、 Device Provisioning Service を使用して、複数のデバイスを自動的にプロビジョニングすることもできます。これは、プロビジョニングするデバイスが多数ある場合に便利です。 目的にプロビジョニング方法に応じて、適切なインストール スクリプトを選択してください。 

### <a name="option-1-manual-provisioning"></a>オプション 1: 手動プロビジョニング

デバイスを手動でプロビジョニングするには、[デバイス接続文字列](how-to-register-device-portal.md)をデバイスに提供する必要があります。この文字列は、新しい IoT Edge デバイスを IoT ハブに登録することで作成できます。

構成ファイルを開きます。 

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルの provisioning セクションを見つけ、**manual** プロビジョニング モードをコメント解除します。 **device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。

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

ファイルを保存して閉じます。 

`CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>オプション 2: 自動プロビジョニング

デバイスを自動的にプロビジョニングするには、[Device Provisioning Service を設定し、デバイス登録 ID を取得](how-to-auto-provision-simulated-device-linux.md)します。 自動プロビジョニングは、トラステッド プラットフォーム モジュール (TPM) チップが搭載されているデバイスでのみ機能します。 たとえば、Raspberry Pi デバイスには、既定で TPM が搭載されていません。 

構成ファイルを開きます。 

```bash
sudo nano /etc/iotedge/config.yaml
```

ファイルの provisioning セクションを見つけ、**dps** プロビジョニング モードをコメント解除します。 **scope_id** と **registration_id** の値を、IoT Hub Device Provisioning Service と TPM を搭載した IoT Edge デバイスの値で更新します。 

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

ファイルを保存して閉じます。 

`CTRL + X`、`Y`、`Enter`

構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>インストールの成功を確認する

前のセクションで**手動構成**手順を使用した場合、IoT Edge ランタイムがデバイス上で正常にプロビジョニングおよび実行されている必要があります。 または、**自動構成**手順を使用した場合は、ランタイムが IoT ハブにデバイスを登録できるように、追加の手順を完了する必要があります。 次の手順については、[Linux 仮想マシンでのシミュレートされた TPM IoT Edge デバイスの作成とプロビジョニング](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)に関する記事をご覧ください。

以下を使用して、IoT Edge デーモンの状態を確認できます。

```bash
systemctl status iotedge
```

以下を使用して、デーモンのログを確認します。

```bash
journalctl -u iotedge --no-pager --no-full
```

また、以下を使用して、実行中のモジュールを一覧表示します。

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>ヒントと検索候補

`iotedge` コマンドの実行には、昇格された特権が必要です。 ランタイムをインストールしたら、マシンからサインアウトした後サインインし直して、自動的にアクセス許可を更新します。 それまでは、すべての `iotedge` コマンドの前に **sudo** を使用します。

リソースに制約のあるデバイスでは、[トラブルシューティング ガイド](troubleshoot.md#stability-issues-on-resource-constrained-devices)に示されているように、*OptimizeForPerformance* 環境変数を *false* に設定することを強くお勧めします。

ネットワークにプロキシ サーバーがある場合は、「[Configure an IoT Edge device to communicate through a proxy server](how-to-configure-proxy-support.md)」(プロキシ サーバー経由で通信するように IoT Edge デバイスを構成する) の手順に従ってください。

## <a name="uninstall-iot-edge"></a>IoT Edge をアンインストールする

IoT Edge のインストールを Linux デバイスから削除する場合は、コマンド ラインから次のコマンドを使用します。 

IoT Edge ランタイムを削除します。 

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge ランタイムが削除されると、作成したコンテナーは停止されますが、デバイスには依然として残っています。 すべてのコンテナーを表示して、どのコンテナーが残っているかを確認します。 

```bash
sudo docker ps -a
```

2 つのランタイム コンテナーを含め、デバイスからコンテナーを削除します。 

```bash
sudo docker rm -f <container name>
```

最後に、デバイスからコンテナー ランタイムを削除します。 

```bash 
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>次の手順

ランタイムがインストールされた IoT Edge デバイスがプロビジョニングされたら、次は [IoT Edge モジュールをデプロイ](how-to-deploy-modules-portal.md)できます。

IoT Edge ランタイムを正常にインストールできない場合は、[トラブルシューティング](troubleshoot.md#stability-issues-on-resource-constrained-devices)のページを参照してください。

IoT Edge の既存のインストールを最新バージョンに更新するには、「[IoT Edge セキュリティ デーモンおよびランタイムの更新](how-to-update-iot-edge.md)」を参照してください。
