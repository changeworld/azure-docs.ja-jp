---
title: ネットワーク プロキシ対応のデバイスを構成する - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge ランタイムおよびインターネット対応の任意の IoT Edge モジュールを構成して、プロキシ サーバー経由で通信する方法。
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a7d32c98b77568e02fa14b70e969eeb254989062
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53100477"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>IoT Edge デバイスを構成してプロキシ サーバー経由で通信する

IoT Edge デバイスでは、HTTPS 要求を送信して IoT Hub と通信します。 お使いのデバイスがプロキシ サーバーを使用するネットワークに接続されている場合、IoT Edge ランタイムを構成してサーバー経由で通信する必要があります。 また、プロキシ サーバーは、Edge Hub 経由でルーティングされない HTTP または HTTPS 要求を行った場合、個々 の IoT Edge モジュールに影響を及ぼす可能性があります。 

プロキシ サーバーを利用するように IoT Edge デバイスを構成するには、次の基本の手順に従います。 

1. お使いのデバイスに IoT Edge ランタイムをインストールします。 
2. プロキシ サーバーを使用するために、Docker デーモンと IoT Edge デーモンをデバイス上に構成します。
3. お使いのデバイス上の config.yaml ファイルに、edgeAgent プロパティを構成します。
4. 配置マニフェストに、IoT Edge ランタイムと他の IoT Edge モジュールの環境変数を設定します。 

## <a name="know-your-proxy-url"></a>プロキシの URL を確認する

デバイスで Docker デーモンと IoT Edge の両方を構成するには、使用しているプロキシ URL を知る必要があります。 

プロキシ URL の形式は、**protocol**://**proxy_host**:**proxy_port** です。 

* **protocol** は HTTP または HTTPS のいずれかです。 Docker デーモンはコンテナー レジストリの設定に応じてどちらのプロトコルでも構成できますが、IoT Edge デーモンとランタイム コンテナーは常に HTTPS を使用する必要があります。

* **proxy_host** は、プロキシ サーバーのアドレスです。 プロキシ サーバーで認証が必要な場合は、proxy_host の一部として、**user**:**password**@**proxy_host** の形式で資格情報を提供できます 

* **proxy_port** は、ネットワーク トラフィックにプロキシが応答するネットワーク ポートです。 

## <a name="install-the-runtime"></a>ランタイムをインストールする

Linux デバイス上に IoT Edge ランタイムをインストールしている場合、プロキシ サーバーを経由してインストール パッケージにアクセスするように、パッケージ マネージャーを構成します。 たとえば、[http-proxy を使用するように apt-get を設定](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy)します。 パッケージ マネージャーが構成されたら、通常どおり 「[Linux に Azure IoT Edge ランタイムをインストールする (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)」または「[Linux に Azure IoT Edge ランタイムをインストールする (x64)](how-to-install-iot-edge-linux.md)」の手順に従います。 

Windows デバイス上に IoT Edge ランタイムをインストールしている場合、プロキシ サーバーを経由してインストール パッケージにアクセスする必要があります。 Windows 設定でプロキシ情報を構成するか、またはインストール スクリプトにプロキシ情報を直接含めることができます。 次の Powershell スクリプトは、`-proxy` 引数を使用した Windows インストールの例です。

```powershell
. {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows
```

詳しい情報とインストール オプションについては、「[Windows に Azure IoT Edge をインストールして Windows コンテナーと共に使用する](how-to-install-iot-edge-windows-with-windows.md)」または「[Windows に Azure IoT Edge をインストールして Linux コンテナーと共に使用する](how-to-install-iot-edge-windows-with-linux.md)」を参照してください。

IoT Edge ランタイムがインストールされたら、次のセクションに進み、プロキシ情報を使ってランタイムを構成します。 

## <a name="configure-the-daemons"></a>デーモンを構成する

お使いの IoT Edge デバイスで実行中の Docker および IoT Edge デーモンは、プロキシ サーバーを使用するように構成される必要があります。 Docker デーモンは、コンテナー レジストリからコンテナー イメージをプルするための Web 要求を行います。 IoT Edge デーモンは、IoT Hub と通信するための Web 要求を行います。

### <a name="docker-daemon"></a>Docker デーモン

環境変数を使用して Docker デーモンを構成するには、Docker のドキュメントを参照してください。 ほとんどのコンテナー レジストリ (DockerHub および Azure Container Registry を含む) では HTTPS 要求をサポートしているので、設定する必要があるパラメーターは **HTTPS_PROXY** です。 トランスポート層セキュリティ (TLS) をサポートしていないレジストリからイメージをプルしている場合は、**HTTP_PROXY** パラメーターを設定する必要があります。 

お使いの Docker バージョンに該当する記事を選択します。 

* [Docker](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Docker for Windows](https://docs.docker.com/docker-for-windows/#proxies)

### <a name="iot-edge-daemon"></a>IoT Edge デーモン

IoT Edge デーモンは、Docker デーモンとほぼ同じ方法で構成されています。 IoT Edge が IoT Hub へ送信するすべての要求で、HTTPS が使用されます。 お使いのオペレーティング システムに基づいて、次の手順に従って、サービス用に環境変数を設定します。 

#### <a name="linux"></a> Linux

IoT Edge デーモンを構成するために、ターミナルでエディターを開きます。 

```bash
sudo systemctl edit iotedge
```

次のテキストを入力して、**\<proxy URL>** をお使いのプロキシ サーバーのアドレスとポートに置き換えます。 その後、保存して終了します。 

```text
[Service]
Environment="https_proxy=<proxy URL>"
```

サービス マネージャーを更新して、iotedge 用の新しい構成を選択します。

```bash
sudo systemctl daemon-reload
```

IoT Edge を再起動して、変更を有効にします。

```bash
sudo systemctl restart iotedge
```

環境変数が作成され、新しい構成が読み込まれたことを確認します。 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a> Windows

管理者として PowerShell ウィンドウを開き、次のコマンドを実行して、新しい環境変数でレジストリを編集します。 **\<proxy url>** をお使いのプロキシ サーバーのアドレスとポートに置き換えます。 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

IoT Edge を再起動して、変更を有効にします。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-edge-agent"></a>Edge エージェントを構成する

Edge エージェントは、任意の IoT Edge デバイス上で開始される最初のモジュールです。 最初は、IoT Edge config.yaml ファイルの情報に基づいて起動されます。 その後、Edge エージェントは IoT Hub に接続して、デバイスへの配置が必要な他のモジュールを宣言する配置マニフェストを取得します。

お使いの IoT Edge デバイス上で、config.yaml ファイルを開きます。 Linux システムの場合、このファイルは **/etc/iotedge/config.yaml** に配置されています。 Windows システムの場合、このファイルは **C:\ProgramData\iotedge\config.yaml** に配置されています。 構成ファイルは保護されているため、アクセスするには管理者権限が必要です。 Linux システムの場合、推奨されるテキスト エディターでファイルを開く前に `sudo` コマンドを使用します。 Windows の場合、Notepad などのテキスト エディターを開いて管理者として実行してから、ファイルを開きます。 

config.yaml ファイルで、**Edge Agent module spec** セクションを探します。 Edge エージェント定義には、環境変数を追加できる **env** パラメーターが組み込まれています。 

<!--
![edgeAgent definition](./media/how-to-configure-proxy-support/edgeagent-unedited.png)
-->

env パラメーターのプレースホルダ―である中かっこを削除して、新しい行に新しい変数を追加します。 YAML では、インデントはスペース 2 つであることに注意してください。 

```yaml
https_proxy: "<proxy URL>"
```

IoT Edge ランタイムでは既定で、AMQP を使用して IoT Hub と通信します。 一部のプロキシ サーバーでは、AMQP ポートをブロックします。 この場合は、WebSocket 経由で AMQP を使用するように、edgeAgent を構成することも必要になります。 2 番目の環境変数を追加します。

```yaml
UpstreamProtocol: "AmqpWs"
```

![環境変数を使用した edgeAgent 定義](./media/how-to-configure-proxy-support/edgeagent-edited.png)

config.yaml への変更を保存して、エディターを閉じます。 IoT Edge を再起動して、変更を有効にします。 

* Linux: 

   ```bash
   sudo systemctl restart iotedge
   ```

* Windows:

   ```powershell
   Restart-Service iotedge
   ```

## <a name="configure-deployment-manifests"></a>配置マニフェストを構成する  

プロキシ サーバーを利用するように IoT Edge デバイスが構成されたら、以降のすべての配置マニフェストでは環境変数も宣言する必要があります。 2 つのランタイム モジュール edgeAgent および edgeHub では常に、IoT Hub との通信を維持するようにプロキシ サーバーを構成する必要があります。 プロキシ サーバー経由で通信するように任意の IoT Edge モジュールを構成できますが、edgeHub 経由でメッセージをルーティングするモジュールやデバイス上の他のモジュールと通信するモジュールの場合は、その必要はありません。 

Azure portal を使用して、または手動で JSON ファイルを編集して、配置マニフェストを作成できます。 

### <a name="azure-portal"></a>Azure ポータル

**モジュールの設定**ウィザードを使用して IoT Edge デバイスの配置を作成する場合、どのモジュールにも、プロキシ サーバー接続の構成に使用できる **[環境変数]** セクションがあります。 

Edge エージェントおよび Edge Hub モジュールを構成するには、ウィザードの最初の手順で **[Edge ランタイムの詳細設定を構成する]** を選択します。 

![Edge ランタイムの詳細設定を構成する](./media/how-to-configure-proxy-support/configure-runtime.png)

Edge エージェントおよび Edge Hub モジュールの両方の定義に、**https_proxy** 環境変数を追加します。 お使いの IoT Edge デバイス上の config.yaml ファイルに **UpstreamProtocol** 環境変数を含めた場合は、Edge エージェント モジュールの定義にもこの環境変数を追加します。 

![https_proxy environment 変数を設定する](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

配置マニフェストに追加する他のモジュールはすべて、同じパターンに従います。 モジュール名およびイメージを設定するページには、環境変数のセクションがあります。

### <a name="json-deployment-manifest-files"></a>JSON 配置マニフェスト ファイル

Visual Studio Code のテンプレートを使用するか、または手動で JSON ファイルを作成して、IoT Edge デバイスの配置を作成した場合、各モジュール定義に環境変数を直接追加できます。 

次の JSON 形式を使用します。 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

環境変数が含まれる場合、モジュール定義は次の edgeHub 例のようになります。

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

お使いの IoT Edge デバイス上の config.yaml ファイルに **UpstreamProtocol** 環境変数を含めた場合は、Edge エージェント モジュールの定義にもこの環境変数を追加します。 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>次の手順

[IoT Edge ランタイム](iot-edge-runtime.md)のロールに関する詳細を確認する

「[Azure IoT Edge での一般的な問題と解決](troubleshoot.md)」でインストールと構成に関するエラーをトラブルシューティングする

