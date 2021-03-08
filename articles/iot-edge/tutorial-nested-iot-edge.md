---
title: チュートリアル - IoT Edge デバイスの階層を作成する - Azure IoT Edge
description: このチュートリアルでは、ゲートウェイを使用する IoT Edge デバイスの階層構造を作成する方法について説明します。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: a7f82ec5a4ef918b1bc7ab0fd6813199c0a1d772
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366394"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>チュートリアル:IoT Edge デバイスの階層を作成する (プレビュー)

階層レイヤーに編成された複数のネットワークに Azure IoT Edge ノードをデプロイします。 階層内の各レイヤーは、その下のレイヤーにあるデバイスからのメッセージと要求を処理するゲートウェイ デバイスです。

>[!NOTE]
>この機能には、Linux コンテナーを実行している IoT Edge バージョン 1.2 (パブリック プレビュー段階) が必要です。

最上位レイヤーだけがクラウドに接続できるように、デバイスの階層構造を構築できます。下位レイヤーは、その上下に隣接するレイヤーとだけ通信できます。 このネットワーク レイヤー化は、ほとんどの産業用ネットワークの基礎であり、[ISA-95 標準](https://en.wikipedia.org/wiki/ANSI/ISA-95)に準拠しています。

このチュートリアルの目的は、運用環境をシミュレートする IoT Edge デバイスの階層を作成することです。 最後には、階層を通じてコンテナー イメージをダウンロードし、[シミュレートされた温度センサー モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)を、インターネットにアクセスできない下位レイヤー デバイスにデプロイします。

この目標を達成するために、このチュートリアルでは、IoT Edge デバイスの階層を作成し、IoT Edge ランタイム コンテナーをデバイスにデプロイして、デバイスをローカルで構成する手順について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * IoT Edge デバイスの階層でリレーションシップを作成して定義します。
> * 階層内のデバイスで IoT Edge ランタイムを構成します。
> * デバイス階層全体で一貫した証明書をインストールします。
> * 階層内のデバイスにワークロードを追加します。
> * API プロキシ モジュールを使用して、下位レイヤー デバイスから単一のポートを介して HTTP トラフィックを安全にルーティングします。

このチュートリアルでは、以下のネットワーク レイヤーを定義します。

* **最上位レイヤー**: このレイヤーの IoT Edge デバイスは、クラウドに直接接続できます。

* **下位レイヤー**: このレイヤーの IoT Edge デバイスは、クラウドに直接接続できません。 データを送受信するには、1 つまたは複数の中間 IoT Edge デバイスを経由する必要があります。

このチュートリアルでは、わかりやすくするために 2 つのデバイスから成る階層を採用しています。 1 つのデバイス (**topLayerDevice**) は、階層の最上位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できます。 このデバイスは、**親デバイス** とも呼ばれます。 もう 1 つのデバイス (**lowerLayerDevice**) は、階層の下位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できません。 このデバイスは、**子デバイス** とも呼ばれます。 運用環境に合わせて、さらに下位レイヤー デバイスを追加することもできます。 追加の下位レイヤー デバイスの構成は、**lowerLayerDevice** の構成に従います。

## <a name="prerequisites"></a>前提条件

IoT Edge デバイスの階層を作成するには、以下のものが必要です。

* インターネットに接続できるコンピューター (Windows または Linux)。
* 有効なサブスクリプションがある Azure アカウント。 [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT 拡張機能 v0.10.6 以降がインストールされている Azure CLI v2.3.1。 このチュートリアルでは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用します。 Azure Cloud Shell に馴染みのない方は、[クイックスタートで詳細を確認](./quickstart-linux.md#prerequisites)してください。
* IoT Edge デバイスとして構成する 2 つの Linux デバイス。 使用可能なデバイスがない場合は、次のコマンドのプレースホルダー テキストを置き換えて 2 回実行することで、2 つの Azure 仮想マシンを作成できます。

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* 8000、443、5671、8883 の各インバウンド ポートが開放されていること。
  * 8000: API プロキシを介して Docker コンテナー イメージをプルする際に使用されます。
  * 443:親エッジ ハブと子エッジ ハブ間で REST API 呼び出しに使用されます。
  * 5671、8883: AMQP と MQTT に使用されます。

  詳細については、「[Azure portal を使用して仮想マシンへのポートを開く方法](../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。

このシナリオは、[産業用 IoT 向け Azure IoT Edge サンプル](https://aka.ms/iotedge-nested-sample)の手順で試してみることもできます。このサンプルでは、事前構成済みのデバイスとしての Azure 仮想マシンをデプロイし、工場環境をシミュレートします。

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge デバイス階層を構成する

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge デバイスの階層を作成する

最初の手順である IoT Edge デバイスの作成は、Azure portal または Azure CLI を使用して行うことができます。 このチュートリアルでは、2 つの IoT Edge デバイス (**topLayerDevice** とその子の **lowerLayerDevice**) の階層を作成します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://ms.portal.azure.com/) で、お使いの IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. **[+ IoT Edge デバイスの追加]** を選択します。 このデバイスは最上位レイヤーのデバイスであるため、それに適した一意のデバイス ID を入力してください。 **[保存]** を選択します。

1. もう一度、 **[+ IoT Edge デバイスの追加]** を選択します。 このデバイスは下位レイヤーのエッジ デバイスであるため、それに適した一意のデバイス ID を入力してください。

1. **[親デバイスの設定]** を選択し、デバイスの一覧で最上位レイヤーのデバイスを選択して、 **[OK]** を選択します。 **[保存]** を選択します。

   ![下位レイヤー デバイスの親を設定する](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、ハブに IoT Edge デバイスを作成します。 このデバイスは最上位レイヤー デバイスであるため、それに適した一意のデバイス ID を入力してください。

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 次のコマンドを入力して、子 IoT Edge デバイスを作成し、デバイス間に親子リレーションシップを作成します。

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

各 IoT Edge デバイスの接続文字列をメモしておきます。 これらは後で使用します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://ms.portal.azure.com/) で、IoT Hub の **[IoT Edge]** セクションに移動します。

1. デバイスの一覧で、いずれかのデバイスのデバイス ID をクリックします。

1. **[プライマリ接続文字列]** フィールドの **[コピー]** を選択し、任意の場所に記録しておきます。

1. 他のすべてのデバイスに対して、手順を繰り返します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で、各デバイスについて次のコマンドを入力してデバイスの接続文字列を取得し、任意の場所に記録しておきます。

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>証明書の作成

[ゲートウェイのシナリオ](iot-edge-as-gateway.md)のデバイスにはいずれも、それらの間にセキュリティで保護された接続を設定するための共通の証明書が必要です。 次の手順に従って、このシナリオの両方のデバイスにデモ証明書を作成します。

Linux デバイスでデモ証明書を作成するには、生成スクリプトを複製し、bash でローカルに実行されるようにスクリプトを設定する必要があります。

1. デモ用の証明書を生成するスクリプトが含まれている IoT Edge git リポジトリを複製します。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. 作業するディレクトリに移動します。 すべての証明書ファイルとキー ファイルがこのディレクトリに作成されます。

1. 構成ファイルとスクリプト ファイルを、複製した IoT Edge リポジトリから作業ディレクトリにコピーします。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. ルート CA 証明書と 1 つの中間証明書を作成します。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   このスクリプト コマンドではいくつかの証明書ファイルとキー ファイルが作成されますが、ここでは次のファイルを **ゲートウェイ階層のルート CA 証明書** として使用しています。

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. 次のコマンドを使用して、2 セットの IoT Edge デバイス CA 証明書と秘密キーを作成します。1 つのセットは最上位レイヤー デバイス用で、もう 1 つのセットは下位レイヤー デバイス用です。 それぞれを区別するために、CA 証明書に覚えやすい名前を指定します。

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   このスクリプト コマンドではいくつかの証明書ファイルとキー ファイルが作成されますが、ここでは各 IoT Edge デバイスで次の証明書とキーのペアを使用し、config.yaml ファイルで参照しています。

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

各デバイスには、ルート CA 証明書のコピーと、独自のデバイス CA 証明書および秘密キーのコピーが必要です。 証明書を各デバイスに移動するには、USB ドライブか、[セキュア ファイル コピー](https://www.ssh.com/ssh/scp/)を使用できます。

1. 証明書を転送した後、各デバイスのルート CA をインストールします。

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   このコマンドにより、/etc/ssl/certs に証明書が 1 つ追加された旨が出力されます。

### <a name="install-iot-edge-on-the-devices"></a>デバイスに IoT Edge をインストールする

両方のデバイスで以下の手順に従って IoT Edge をインストールします。

1. デバイスのオペレーティング システムに対応するリポジトリ構成をインストールします。

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. 生成されたリストを sources.list.d ディレクトリにコピーします。

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Microsoft GPG 公開キーをインストールします。

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```
   
1. デバイスのパッケージ リストを更新します。

   ```bash
   sudo apt-get update
   ```

1. Moby エンジンをインストールします。

   ```bash
   sudo apt-get install moby-engine
   ```

1. hsmlib と IoT Edge デーモンをインストールします。 他の Linux ディストリビューションのアセットを確認するには、[GitHub リリースにアクセス](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)します。 <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0_rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する

両方のデバイスで次の手順に従って IoT Edge ランタイムを構成します。 デバイスの IoT Edge ランタイムの構成は次の 4 つの手順から成り、すべてを IoT Edge 構成ファイルの編集することによって行います。

1. デバイスの接続文字列を構成ファイルに追加して、各デバイスを手動でプロビジョニングします。

1. デバイス CA 証明書、デバイス CA 秘密キー、およびルート CA 証明書を参照するように構成ファイルを更新して、デバイスの証明書の設定を完了します。

1. IoT Edge エージェントを使用してシステムのブートストラップを実行します。

1. **最上位レイヤー** デバイスの **hostname** パラメーターを更新し、**下位レイヤー** デバイスの **hostname** パラメーターと **parent_hostname** パラメーターの両方を更新します。

これらの手順を完了し、IoT Edge サービスを再起動して、デバイスを構成します。

1. 各デバイスで、IoT Edge 構成ファイルを開きます。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. ファイルのプロビジョニング構成を見つけ、「**Manual provisioning configuration using a connection string**」セクションをコメント解除します。

1. **device_connection_string** の値を IoT Edge デバイスからの接続文字列で更新します。 他のプロビジョニング セクションがすべてコメント アウトされていることを確認します。**provisioning:** の行に先行する空白文字がなく、入れ子になった項目が 2 つの空白でインデントされていることを確認します。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >クリップボードの内容を Nano に貼り付けるには、`Shift+Right Click` キーまたは `Shift+Insert` キーを押します。

1. **certificates** セクションを探します。 3 つの証明書フィールドのコメントを解除して、前のセクションで作成し、IoT Edge デバイスに移動した証明書を参照するように更新します。 ファイルの URI パスを指定します。これは、`file:///<path>/<filename>` の形式を取ります。

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. **最上位レイヤー** デバイスで、**hostname** パラメーターを探します。 IoT Edge デバイスの完全修飾ドメイン名 (FQDN) または IP アドレスになるように、値を更新します。 いずれかの値を選択して、階層内のデバイス全体で一貫して使用します。

   ```yml
   hostname: <device fqdn or IP>
   ```

1. **下位レイヤー** の IoT Edge デバイスでは、親デバイスの FQDN または IP を参照するように構成ファイルを更新します。これは、親デバイスの **hostname** フィールドの内容と一致させます。 **最上位レイヤー** の IoT Edge デバイスでは、このパラメーターをコメントアウトしたままにしておきます。

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > 階層に下位レイヤーが複数ある場合は、*parent_hostname* フィールドをすぐ上のレイヤーにあるデバイスの FQDN に更新します。

1. **最上位レイヤー** のデバイスの場合は、**agent** yaml セクションを探し、イメージの値を IoT Edge エージェントの適切なバージョンに更新します。 ここでは、Azure Container Registry の最上位レイヤーの IoT Edge エージェントに、使用可能な IoT Edge エージェント イメージのパブリック プレビュー バージョンを指定します。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. **下位レイヤー** の IoT Edge デバイスでは、イメージ値のドメイン名を親デバイスの FQDN または IP を参照するように更新し、その後に API プロキシ ポートの 8000 を付加します。 API プロキシ モジュールは、次のセクションで追加します。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. ファイルを保存して閉じます。

   `CTRL + X`, `Y`, `Enter`

1. 構成ファイルにプロビジョニング情報を入力した後、デーモンを再起動します。

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>最上位レイヤーのデバイスにモジュールをデプロイする

IoT Edge ランタイムの構成を完了し、ワークロードをデプロイするための残りの手順は、Azure portal または Azure CLI を使用してクラウドから実行します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure ポータル](https://ms.portal.azure.com/)で次の操作を行います。

1. IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. デバイスの一覧で、**最上位レイヤー** のエッジ デバイスのデバイス ID をクリックします。

1. 上部のバーで **[モジュールの設定]** を選択します。

1. 歯車アイコンの横の **[ランタイムの設定]** を選択します。

1. **[Edge Hub]** の下の [イメージ] フィールドに、「`mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2`」と入力します。

   ![Edge Hub のイメージを編集する](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Edge Hub モジュールに、以下の環境変数を追加します。

    | 名前 | [値] |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Edge Hub の環境変数を編集する](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. **[Edge エージェント]** の下の [イメージ] フィールドに、「`mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2`」と入力します。 **[保存]** を選択します。

1. 最上位レイヤー デバイスに Docker レジストリ モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[IoT Edge モジュール]** を選択します。 Docker レジストリ モジュールに名前 `registry` を指定し、イメージ URI に「`registry:latest`」と入力します。 次に、環境変数を追加し、Microsoft コンテナー レジストリのローカル レジストリ モジュールを参照するオプションを作成します。そこからコンテナー イメージがダウンロードされ、それらのイメージが registry:5000 で提供されます。

1. 環境変数のタブで、次の環境変数の名前と値のペアを入力します。

    | 名前 | [値] |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. コンテナーの作成オプションのタブで、次の JSON を入力します。

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. 次に、最上位レイヤー デバイスに API プロキシ モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[Marketplace モジュール]** を選択します。 `IoT Edge API Proxy` を検索し、モジュールを選択します。 IoT Edge API プロキシはポート 8000 を使用し、既定ではポート 5000 で `registry` という名前のレジストリ モジュールを使用するように構成されています。

1. **[確認および作成]** 、 **[作成]** の順に選択して、デプロイを完了します。 インターネットにアクセスできる最上位レイヤー デバイスの IoT Edge ランタイムは、IoT Edge ハブおよび IoT Edge エージェント用の **パブリック プレビュー** 構成をプルして実行します。

   ![エッジ ハブ、エッジ エージェント、レジストリ モジュール、および API プロキシ モジュールを含むデプロイを完了する](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、deployment.json ファイルを作成します。

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. 以下の JSON の内容を deployment.json にコピーし、保存して閉じます。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 次のコマンドを入力して、最上位レイヤーのエッジ デバイスへのデプロイを作成します。

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>下位レイヤーのデバイスにモジュールをデプロイする

クラウドから **下位レイヤー** のデバイスにワークロードをデプロイする作業には、Azure portal と Azure CLI のどちらも利用できます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

[Azure ポータル](https://ms.portal.azure.com/)で次の操作を行います。

1. IoT Hub に移動します。

1. 左側のペインのメニューで、 **[デバイスの自動管理]** の下にある **[IoT Edge]** を選択します。

1. IoT Edge デバイスの一覧で、下位レイヤーのデバイスのデバイス ID をクリックします。

1. 上部のバーで **[モジュールの設定]** を選択します。

1. 歯車アイコンの横の **[ランタイムの設定]** を選択します。

1. **[Edge Hub]** の下の [イメージ] フィールドに、「`$upstream:8000/azureiotedge-hub:1.2.0-rc2`」と入力します。

1. Edge Hub モジュールに、以下の環境変数を追加します。

    | 名前 | [値] |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. **[Edge エージェント]** の下の [イメージ] フィールドに、「`$upstream:8000/azureiotedge-agent:1.2.0-rc2`」と入力します。 **[保存]** を選択します。

1. 温度センサー モジュールを追加します。 **[+ 追加]** を選択し、ドロップダウンで **[Marketplace モジュール]** を選択します。 `Simulated Temperature Sensor` を検索し、モジュールを選択します。

1. **[IoT Edge モジュール]** で、先ほど追加した `Simulated Temperature Sensor` モジュールを選択し、そのイメージ URI を、`$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` を参照するように更新します。

1. **[保存]** 、 **[確認および作成]** 、 **[作成]** の順に選択し、デプロイを完了します。

   ![エッジ ハブ、エッジ エージェント、およびシミュレートされた温度センサーを含むデプロイを完了する](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](https://shell.azure.com/) で次のコマンドを入力して、deployment.json ファイルを作成します。

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. 以下の JSON の内容を deployment.json にコピーし、保存して閉じます。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 次のコマンドを入力して、下位レイヤーのエッジ デバイスへのモジュールの設定デプロイを作成します。

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## IotEdge check

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc2
```
   
`azureiotedge-diagnostics` の値は、レジストリ モジュールにリンクされたコンテナー レジストリからプルされます。 このチュートリアルでは、既定値の https://mcr.microsoft.com: に設定しています。

| 名前 | [値] |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

プライベート コンテナー レジストリを使用する場合は、コンテナー レジストリにすべてのイメージ (IoTEdgeAPIProxy、edgeAgent、edgeHub、diagnostics など) が存在することを確認してください。    
    
## <a name="view-generated-data"></a>生成されたデータを表示する

プッシュした **Simulated Temperature Sensor** モジュールは、サンプル環境データを生成します。 送信されるメッセージには、周囲の温度と湿度、機械の温度と圧力、タイムスタンプが含まれます。

[Visual Studio Code 用の Azure IoT Hub の拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)を使用して、IoT ハブに到着したメッセージを監視することができます。

それらのメッセージを [Azure Cloud Shell](https://shell.azure.com/) から確認することもできます。

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除できます。

リソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. IoT Edge のテスト リソースを含んだリソース グループの名前を選択します。 

3. リソース グループに含まれるリソースの一覧を確認します。 それらすべてを削除する場合は、**[リソース グループの削除]** を選択します。 一部だけを削除する場合は、削除する各リソースをクリックして個別に削除してください。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、2 つの IoT Edge デバイスをゲートウェイとして構成し、一方をもう一方の親デバイスとして設定しました。 次に、ゲートウェイを介してコンテナー イメージを子デバイスにプルする方法を示しました。

Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [モジュールとして Azure Machine Learning モデルをデプロイする](tutorial-deploy-machine-learning.md)
