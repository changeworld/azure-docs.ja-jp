---
title: 空間分析コンテナーをインストールして実行する方法 - Computer Vision
titleSuffix: Azure Cognitive Services
description: 空間分析コンテナーを使用すると、人物と距離を検出できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: 6ebc1831b990b540bcb9a3856c380c28142af536
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357115"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>空間分析コンテナー (プレビュー) をインストールして実行する

空間分析コンテナーを使用すると、リアルタイム ストリーミング ビデオを分析して、人物間の空間的な関係、彼らの動き、物体との相互作用を把握できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision リソースを作成"  target="_blank">Computer Vision リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * 空間分析コンテナーを実行するには、作成したリソースのキーとエンドポイントが必要です。 後でキーとエンドポイントを使用します。


### <a name="spatial-analysis-container-requirements"></a>空間分析コンテナーの要件

空間分析コンテナーを実行するには、[NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) を搭載したコンピューティング デバイスが必要です。 GPU アクセラレーションを備えた [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) を使用することをお勧めしますが、コンテナーは最小要件を満たす他のどのデスクトップ コンピューターでも実行できます。 このデバイスをホスト コンピューターと呼びます。

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)

Azure Stack Edge は、サービスとしてのハードウェア ソリューションであり、ネットワーク データ転送機能を備えた AI 対応のエッジ コンピューティング デバイスです。 準備とセットアップの詳細な手順については、[Azure Stack Edge のドキュメント](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)を参照してください。

#### <a name="desktop-machine"></a>[デスクトップ コンピューター](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>ハードウェアに最低限必要な条件

* 4 GB のシステム RAM
* 4 GB の GPU RAM
* 8 コア CPU
* NVIDIA Tesla T4 GPU x 1
* 20 GB の HDD 領域

#### <a name="recommended-hardware"></a>推奨ハードウェア

* 32 GB のシステム RAM
* 16 GB の GPU RAM
* 8 コア CPU
* NVIDIA Tesla T4 GPU x 2
* 50 GB の SSD 領域

この記事では、次のソフトウェア パッケージをダウンロードしてインストールします。 ホスト コンピューターによって以下を実行できる必要があります (手順については以下を参照してください)。

* [NVIDIA グラフィックス ドライバー](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)と [NVIDIA CUDA Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (マルチプロセス サービス) の構成。
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) と [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) ランタイム。

---

| 要件 | 説明 |
|--|--|
| カメラ | 空間分析コンテナーは、特定のカメラ ブランドと結び付いていません。 カメラ デバイスは、リアルタイム ストリーミング プロトコル (RTSP) と H.264 エンコードをサポートし、ホスト コンピューターからアクセスでき、15FPS の解像度 1080p でストリーミングできる必要があります。 |
| Linux OS | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) をホスト コンピューターにインストールする必要があります。  |


## <a name="request-approval-to-run-the-container"></a>コンテナーを実行するための承認を要求する

コンテナーを実行するための承認を要求するには、[要求フォーム](https://aka.ms/csgate)に記入して送信します。

このフォームでは、ユーザー、会社、コンテナーを使用するユーザー シナリオに関する情報が要求されます。 フォームを送信すると、そのフォームは Azure Cognitive Services チームによって確認されます。その後、チームから決定事項がメールで届きます。

> [!IMPORTANT]
> * このフォームでは、Azure サブスクリプション ID に関連付けられているメール アドレスを使用する必要があります。
> * コンテナーの実行に使用する Computer Vision リソースは、承認された Azure サブスクリプション ID で作成されている必要があります。

承認されると、Microsoft Container Registry (MCR) からコンテナーをダウンロードした後、そのコンテナーを実行できるようになります。これについては、記事の後半で説明します。

お使いの Azure サブスクリプションが承認されていない場合、コンテナーを実行することはできません。

## <a name="set-up-the-host-computer"></a>ホスト コンピューターを設定する

ホスト コンピューターには Azure Stack Edge デバイスを使用することをお勧めします。 別のデバイスを構成する場合は、 **[Desktop Machine]\(デスクトップ コンピューター\)** をクリックします。

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge デバイス](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Azure Stack Edge ポータルでコンピューティングを構成する 
 
空間分析によって、AI ソリューションを実行するのに Azure Stack Edge のコンピューティング機能が使用されます。 コンピューティング機能を有効にするには、次のことを確認してください。 

* Azure Stack Edge デバイスの[接続とアクティブ化](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)が済んでいること。 
* デバイスにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。  
* Kubernetes クラスターをデプロイするには、 [Azure portal](https://portal.azure.com/) で **ローカル UI** を使用して Azure Stack Edge デバイスを構成する必要があります。 
  1. Azure Stack Edge デバイスでコンピューティング機能を有効にします。 コンピューティングを有効にするには、デバイスの Web インターフェイスで **[コンピューティング]** ページに移動します。 
  2. コンピューティングを有効にする対象のネットワーク インターフェイスを選択してから、 **[有効にする]** をクリックします。 これにより、そのネットワーク インターフェイス上のデバイスに仮想スイッチが作成されます。
  3. Kubernetes テスト ノードの IP アドレスと Kubernetes 外部サービスの IP アドレスは空白のままにします。
  4. **[適用]** をクリックします。 この操作には 2 分ほどかかることがあります。 

![コンピューティングを構成する](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Edge コンピューティング ロールを設定し、IoT Hub リソースを作成する

[Azure portal](https://portal.azure.com/) で、お使いの Azure Stack Edge リソースに移動します。 **[概要]** ページまたはナビゲーション リストで、Edge コンピューティングの **[開始]** ボタンをクリックします。  **[Edge コンピューティングの構成]**  タイルで、 **[構成]** をクリックします。 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

 **[Edge コンピューティングの構成]**  ページで、既存の IoT ハブを選択するか、新しいものを作成します。 既定では、IoT Hub リソースの作成には Standard (S1) 価格レベルが使用されます。 Free レベルの IoT Hub リソースを使用するには、それを作成してから選択します。 IoT Hub リソースによって、Azure Stack Edge リソースで使用されるのと同じサブスクリプションとリソース グループが使用されます 

**[作成]** をクリックします。 IoT Hub リソースの作成は数分かかることがあります。 IoT Hub リソースが作成された後、 **[Edge コンピューティングの構成]** タイルが更新され、新しい構成が表示されます。 Edge コンピューティング ロールが構成されたことを確認するには、 **[コンピューティングの構成]**  タイルの  **[構成の表示]** を選択します。

Edge デバイスで Edge コンピューティング ロールが設定されると、2 つのデバイスが作成されます (IoT デバイスと IoT Edge デバイス)。 IoT Hub リソースでは、両方のデバイスを表示できます。 Azure IoT Edge ランタイムは、IoT Edge デバイス上で既に実行されています。            

> [!NOTE]
> * 現時点では、IoT Edge デバイスでサポートされているのは Linux プラットフォームのみです。 Azure Stack Edge デバイスのトラブルシューティングについては、[ロギングとトラブルシューティング](spatial-analysis-logging.md)の記事を参照してください。
> * プロキシサーバー経由で通信するように IoT Edge デバイスを構成する方法の詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](https://docs.microsoft.com/azure/iot-edge/how-to-configure-proxy-support#azure-portal)」を参照してください

###  <a name="enable-mps-on-azure-stack-edge"></a>Azure Stack Edge での MPS を有効にする 

1. Windows PowerShell セッションを管理者として実行します。 

2. Windows リモート管理サービスがクライアントで実行されていることを確認します。 PowerShell ターミナルで、次のコマンドを使用します。 
    
    ```powershell
    winrm quickconfig
    ```
    
    ファイアウォールの例外に関する警告が表示された場合は、ネットワーク接続の種類を確認し、[Windows リモート管理](https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management)のドキュメントを参照してください。

3. デバイスの IP アドレスに変数を割り当てます。 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. デバイスの IP アドレスをクライアントの信頼されたホスト一覧に追加するために、次のコマンドを使用します。 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. デバイスの Windows PowerShell セッションを開始します。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. パスワードの入力を求められたら、入力します。 ローカル Web UI へのサインインに使用するパスワードと同じものを使用してください。 既定のローカル Web UI パスワードは `Password1` です。

「`Start-HcsGpuMPS`」と入力して、デバイス上で MPS サービスを開始します。 

Azure Stack Edge デバイスのトラブルシューティングについては、[Azure Stack Edge デバイスのトラブルシューティング](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device)に関するページを参照してください。 

#### <a name="desktop-machine"></a>[デスクトップ コンピューター](#tab/desktop-machine)

使用するホスト コンピューターが Azure Stack Edge デバイスでない場合は、次の手順に従います。

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>NVIDIA CUDA Toolkit および Nvidia グラフィックス ドライバーをホスト コンピューターにインストールする

次の bash スクリプトを使用して、必要な Nvidia グラフィックス ドライバーと、CUDA Toolkit をインストールします。

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

コンピューターを再起動し、次のコマンドを実行します。

```bash
nvidia-smi
```

次の出力が表示されます。

![NVIDIA ドライバーの出力](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>ホスト コンピューターに Docker CE と nvidia-docker2 をインストールする

ホスト コンピューターに Docker CE をインストールします。

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

*nvidia-docker-2* ソフトウェア パッケージをインストールします。

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>ホスト コンピューター上で NVIDIA MPS を有効にする

> [!TIP]
> * お使いの GPU コンピューティング機能が 7.x より小さい (Volta より前) 場合は、MP をインストールしないでください。 詳細については、[CUDA の互換性](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title)に関するページを参照してください。 
> * ホスト コンピューターのターミナル ウィンドウから、MPS の手順を行います。 Docker コンテナー インスタンス内ではありません。

最適なパフォーマンスと使用率を得るには、[NVIDIA マルチプロセス サービス (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) 用にホスト コンピューターの GPU を構成します。 ホスト コンピューターのターミナル ウィンドウから、MPS の手順を行います。

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>ホスト コンピューター上で Azure IoT Edge を構成する

ホスト コンピューターに空間分析コンテナーをデプロイするには、Standard (S1) または Free (F0) 価格レベルを使用して、[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) サービスのインスタンスを作成します。 使用するホスト コンピューターが Azure Stack Edge である場合は、Azure Stack Edge リソースに使用しているのと同じサブスクリプションとリソース グループを使用します。

Azure CLI を使用して Azure IoT Hub のインスタンスを作成します。 必要に応じて、パラメーターを置き換えます。 または、[Azure portal](https://portal.azure.com/) 上で Azure IoT ハブを作成することもできます。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

ホスト コンピューターが Azure Stack Edge デバイスでない場合は、[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) バージョン 1.0.9 をインストールする必要があります。 次の手順に従って、正しいバージョンをダウンロードします。

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

生成された一覧をコピーします。
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Microsoft GPG 公開キーをインストールします。

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

デバイスのパッケージ リストを更新します。

```bash
sudo apt-get update
```

1\.0.9 リリースをインストールします。

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

次に、[接続文字列](https://docs.microsoft.com/azure/iot-edge/how-to-register-device#register-in-the-azure-portal)を使用して、ホスト コンピューターを IoT Edge デバイスとして IoT Hub インスタンスに登録します。

IoT Edge デバイスを Azure IoT ハブに接続する必要があります。 先ほど作成した IoT Edge デバイスから接続文字列をコピーする必要があります。 または、Azure CLI で以下のコマンドを実行することもできます。

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

ホスト コンピューター上で `/etc/iotedge/config.yaml` を編集するために開きます。 `ADD DEVICE CONNECTION STRING HERE` を接続文字列に置き換えます。 ファイルを保存して閉じます。 次のコマンドを実行して、ホスト コンピューター上の IoT Edge サービスを再起動します。

```bash
sudo systemctl restart iotedge
```

[Azure portal](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) または [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) から、空間分析コンテナーを IoT モジュールとしてホスト コンピューターにデプロイします。 ポータルを使用している場合は、イメージの URI を Azure Container Registry の場所に設定します。 

Azure CLI を使用して、以下の手順に従ってコンテナーをデプロイします。

---

### <a name="iot-deployment-manifest"></a>IoT 配置マニフェスト

複数のホスト コンピューターへのコンテナーの配置を効率化するために、配置マニフェスト ファイルを作成して、コンテナーの作成オプションと環境変数を指定できます。 GitHub に、[Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) および[その他のデスクトップ コンピューター](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)用の配置マニフェストの例があります。

次の表は、IoT Edge モジュールで使用されるさまざまな環境変数を示しています。 また、`spatialanalysis` の `env` 属性を使用して、上のリンク先の配置マニフェストで設定することもできます。

| 設定名 | 値 | 説明|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | 情報。詳細 | ログ レベルで 2 つの値のいずれかを選択します|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 変更しないでください|
| ARCHON_PERF_MARKER| false| パフォーマンス ログの場合は true に設定します。それ以外の場合は false に設定します| 
| ARCHON_NODES_LOG_LEVEL | 情報。詳細 | ログ レベルで 2 つの値のいずれかを選択します|
| OMP_WAIT_POLICY | PASSIVE | 変更しないでください|
| QT_X11_NO_MITSHM | 1 | 変更しないでください|
| API_KEY | API キー| この値は、Azure portal で Computer Vision リソースから収集します。 お使いのリソースの **[キーとエンドポイント]** セクションで見つけることができます。 |
| BILLING_ENDPOINT | エンドポイント URI| この値は、Azure portal で Computer Vision リソースから収集します。 お使いのリソースの **[キーとエンドポイント]** セクションで見つけることができます。|
| EULA | accept | コンテナーを実行するには、この値を *accept* に設定する必要があります |
| DISPLAY | :1 | この値は、ホスト コンピューター上の `echo $DISPLAY` の出力と同じである必要があります。 Azure Stack Edge デバイスにはディスプレイがありません。 この設定は適用されません|


> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

独自の設定と操作の選択によって [Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)または[デスクトップ コンピューター](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)用の配置マニフェストを更新したら、以下の [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) コマンドを使用して、コンテナーを IoT Edge モジュールとしてホスト コンピューターにデプロイできます。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json --subscription "<subscriptionId>"
```

|パラメーター  |説明  |
|---------|---------|
| `--hub-name` | Azure IoT ハブの名前。 |
| `--content` | デプロイ ファイルの名前。 |
| `--target-condition` | ホスト コンピューターの IoT Edge デバイス名。 |
| `-–subscription` | サブスクリプションの ID または名前。 |

このコマンドを実行すると、デプロイが開始されます。 Azure portal で Azure IoT Hub インスタンスのページに移動して、デプロイの状態を確認します。 デバイスへのコンテナー イメージのダウンロードが終了して実行が開始するまで、状態は " *417 – デバイスのデプロイ構造が設定されていません* " と表示されることがあります。

## <a name="validate-that-the-deployment-is-successful"></a>デプロイが成功したことを検証する

コンテナーが実行されていることを検証する方法は複数あります。 Azure portal の **[IoT Edge モジュールの設定]** で、Azure IoT Hub インスタンス内の空間分析モジュールの *[ランタイムの状態]* を見つけます。 *[ランタイムの状態]* の **[必要な値]** と **[報告された値]** が、 *[実行中]* であることを検証します。

![デプロイの検証の例](./media/spatial-analysis/deployment-verification.png)

デプロイが完了し、コンテナーが実行中になると、 **ホスト コンピューター** から Azure IoT ハブへのイベント送信が始まります。 `.debug` バージョンの操作を使用した場合は、配置マニフェストで構成した各カメラのビジュアライザー ウィンドウが表示されます。 これで、監視する回線とゾーンを配置マニフェストで定義し、指示に従って再度デプロイすることができます。 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>空間分析で実行される操作を構成する

接続されたカメラを使用するようにコンテナーを構成したり、操作を構成したりするためには、[空間分析操作](spatial-analysis-operations.md)を使用する必要があります。 構成するカメラ デバイスごとに、空間分析の操作によって、Azure IoT Hub のインスタンスに送信される JSON メッセージの出力ストリームが生成されます。

## <a name="redeploy-or-delete-the-deployment"></a>再デプロイする、またはデプロイを削除する

デプロイを更新する必要がある場合は、以前のデプロイが正常にデプロイされていることを確認することや、完了していない IoT Edge デバイスのデプロイを削除することが必要になります。 そうしないと、それらのデプロイが続行され、システムが正しくない状態のままになります。 Azure portal か、[Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment) を使用することができます。

## <a name="use-the-output-generated-by-the-container"></a>コンテナーによって生成された出力を使用する

コンテナーによって生成された出力の使用を開始する場合は、次の記事を参照してください。

*   選択したプログラミング言語の Azure Event Hub SDK を使用して、Azure IoT Hub エンドポイントに接続し、イベントを受信します。 詳細については、「[デバイスからクラウドへのメッセージを組み込みのエンドポイントから読み取る](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)」を参照してください。 
*   イベントを他のエンドポイントに送信したり、イベントを Azure Blob Storage に保存したりするには、Azure IoT ハブにメッセージ ルーティングを設定します。詳細については、[IoT Hub メッセージ ルーティング](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)に関するページを参照してください。 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>記録されたビデオ ファイルを使用して空間分析を実行する

空間分析は、記録されたビデオとライブ ビデオの両方で使用できます。 記録されたビデオに対して空間分析を使用するには、ビデオ ファイルを記録し、mp4 ファイルとして保存してみてください。 Azure に BLOB ストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 次に、Azure portal で次の BLOB ストレージ設定を更新します。
    1. **[安全な転送が必須]** を **[無効]** に変更
    2. **[Allow Blob public access]\(BLOB パブリック アクセスを許可する\)** を **[有効]** に変更

**[コンテナー]** セクションに移動し、新しいコンテナーを作成するか、既存のものを使用します。 次に、ビデオ ファイルをコンテナーにアップロードします。 アップロードしたファイルのファイル設定を展開し、 **[SAS の生成]** を選択します。 **[有効期限]** は、テスト期間に対応できる長さに設定してください。 **[許可されるプロトコル]** を *[HTTP]* に設定します ( *HTTPS* はサポートされていません)。

**[SAS トークンおよび URL を生成]** をクリックし、BLOB SAS の URL をコピーします。 先頭の `https` を `http` に置き換え、ビデオ再生をサポートするブラウザーで URL をテストします。

すべてのグラフについて、[Azure Stack Edge デバイス](https://go.microsoft.com/fwlink/?linkid=2142179)または別の[デスクトップ コンピューター](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)用の配置マニフェスト内の `VIDEO_URL` を、作成した URL に置き換えます。 `VIDEO_IS_LIVE` を `false` に設定し、更新されたマニフェストを使用して空間分析コンテナーを再デプロイします。 次の例を見てください。

空間分析モジュールによるビデオ ファイルの使用が開始され、継続的に自動再生されます。


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>トラブルシューティング

コンテナーの開始時または実行時に問題が発生した場合は、[テレメトリとトラブルシューティング](spatial-analysis-logging.md)に関するページを参照して、一般的な問題に対処する手順を確認してください。 この記事には、ログの生成と収集とシステムおよびシステム正常性の収集に関する情報も含まれています。

## <a name="billing"></a>課金

課金情報は、Azure アカウントの Computer Vision リソースを使用して空間分析コンテナーから Azure に送信されます。 パブリック プレビューでの空間分析は、現在、無料でご利用いただけます。 

Azure Cognitive Services コンテナーは、計測または課金エンドポイントに接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に課金エンドポイントに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーによって、お客様のデータ (解析対象のビデオや画像など) が Microsoft に送信されることはありません。


## <a name="summary"></a>まとめ

この記事では、空間分析コンテナーの概念とそのダウンロード、インストール、および実行のワークフローについて説明しました。 要約すると:

* 空間分析は、Docker 用の Linux コンテナーです。
* コンテナー イメージは Microsoft Container Registry からダウンロードされます。
* コンテナー イメージは、Azure IoT Edge で IoT モジュールとして実行されます。
* コンテナーを構成し、ホスト コンピューターにデプロイする方法。

## <a name="next-steps"></a>次のステップ

* [人数カウント Web アプリをデプロイする](spatial-analysis-web-app.md)
* [空間分析操作の構成](spatial-analysis-operations.md)
* [ロギングおよびトラブルシューティング](spatial-analysis-logging.md)
* [カメラの配置ガイド](spatial-analysis-camera-placement.md)
* [ゾーンとラインの配置ガイド](spatial-analysis-zone-line-placement.md)
