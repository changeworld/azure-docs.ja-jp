---
title: チュートリアル - IoT Edge デバイスの階層を作成する - Azure IoT Edge
description: このチュートリアルでは、ゲートウェイを使用する IoT Edge デバイスの階層構造を作成する方法について説明します。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728502"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>チュートリアル:IoT Edge デバイスの階層を作成する

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

階層レイヤーに編成された複数のネットワークに Azure IoT Edge ノードをデプロイします。 階層内の各レイヤーは、その下のレイヤーにあるデバイスからのメッセージと要求を処理するゲートウェイ デバイスです。

最上位レイヤーだけがクラウドに接続できるように、デバイスの階層構造を構築できます。下位レイヤーは、その上下に隣接するレイヤーとだけ通信できます。 このネットワーク レイヤー化は、ほとんどの産業用ネットワークの基礎であり、[ISA-95 標準](https://en.wikipedia.org/wiki/ANSI/ISA-95)に準拠しています。

このチュートリアルの目的は、単純化した運用環境をシミュレートする IoT Edge デバイスの階層を作成することです。 最後には、階層を通じてコンテナー イメージをダウンロードし、[シミュレートされた温度センサー モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)を、インターネットにアクセスできない下位レイヤー デバイスにデプロイします。

この目標を達成するために、このチュートリアルでは、IoT Edge デバイスの階層を作成し、IoT Edge ランタイム コンテナーをデバイスにデプロイして、デバイスをローカルで構成する手順について説明します。 このチュートリアルでは、自動構成ツールを使用して次のことを行います。

> [!div class="checklist"]
>
> * IoT Edge デバイスの階層でリレーションシップを作成して定義します。
> * 階層内のデバイスで IoT Edge ランタイムを構成します。
> * デバイス階層全体で一貫した証明書をインストールします。
> * 階層内のデバイスにワークロードを追加します。
> * [IoT Edge API プロキシ モジュール](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)を使用して、下位レイヤー デバイスから単一のポートを介して HTTP トラフィックを安全にルーティングします。

>[!TIP]
>このチュートリアルには、IoT Edge の入れ子機能を紹介する手動の手順と自動の手順が混在しています。
>
>IoT Edge デバイスの階層が完全に自動的に設定されるようすを確認したい場合は、スクリプト化された[産業用 IoT 向け Azure IoT Edge サンプル](https://aka.ms/iotedge-nested-sample)に従ってください。 このスクリプト化されたシナリオでは、工場環境をシミュレートするための構成済みのデバイスとして Azure 仮想マシンがデプロイされます。
>
>IoT Edge デバイスの階層を手動で作成および管理する手順の詳細を確認したい場合は、[IoT Edge デバイス ゲートウェイ階層の使用法ガイド](how-to-connect-downstream-iot-edge-device.md)を参照してください。

このチュートリアルでは、以下のネットワーク レイヤーを定義します。

* **最上位レイヤー**: このレイヤーの IoT Edge デバイスは、クラウドに直接接続できます。

* **下位レイヤー**: 最上位レイヤーより下にあるレイヤーの IoT Edge デバイスは、クラウドに直接接続できません。 データを送受信するには、1 つまたは複数の中間 IoT Edge デバイスを経由する必要があります。

このチュートリアルでは、簡潔にするため 2 つのデバイスから成る階層を使用します (下図参照)。 1 つのデバイス (**最上位レイヤー デバイス**) は、階層の最上位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できます。 このデバイスは、**親デバイス** とも呼ばれます。 もう 1 つのデバイス (**下位レイヤー デバイス**) は、階層の下位レイヤーにあるデバイスを表します。これは、クラウドに直接接続できません。 必要に応じて、さらに下位レイヤー デバイスを追加して運用環境を表すことができます。 下位レイヤーのデバイスは、**子デバイス** とも呼ばれます。

![2 つのデバイス (最上位レイヤー デバイスと下位レイヤー デバイス) が含まれるチュートリアルの階層の構造](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>前提条件

IoT Edge デバイスの階層を作成するには、以下のものが必要です。

* インターネットに接続できるコンピューター (Windows または Linux)。
* 有効なサブスクリプションがある Azure アカウント。 [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
* Azure の Free レベルまたは Standard レベルの [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)。
* Azure IoT 拡張機能 v0.10.6 以降がインストールされた、Azure CLI v2.3.1 を使用した Azure Cloud Shell の Bash シェル。 このチュートリアルでは、[Azure Cloud Shell](../cloud-shell/overview.md) を使用します。 Azure Cloud Shell に馴染みのない方は、[クイックスタートで詳細を確認](./quickstart-linux.md#prerequisites)してください。
  * Azure CLI モジュールと拡張機能の現在のバージョンを確認するには、[az version](/cli/azure/reference-index?#az_version) を実行します。
* 階層内の各デバイスの IoT Edge デバイスとして構成する Linux デバイス。 このチュートリアルでは 2 つのデバイスを使用します。 使用できるデバイスがない場合は、以下のコマンドを使用して、階層内の各デバイスに Azure 仮想マシンを作成できます。

   次のコマンドのプレースホルダー テキストを置き換えて 2 回 (仮想マシンごとに 1 回) 実行してください。 それぞれの仮想マシンには、その名前としても使用される一意の DNS プレフィックスが必要です。 この DNS プレフィックスは、`[a-z][a-z0-9-]{1,61}[a-z0-9]` という正規表現に従う必要があります。

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   この仮想マシンでは、ユーザーの認証に SSH キーが使用されます。 SSH キーの作成と使用に慣れていない場合は、[Azure の Linux VM に使用する SSH の公開キーと秘密キーの組に関する手順](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)に従ってください。

   IoT Edge バージョン 1.2 はこの ARM テンプレートと共にプレインストールされるため、そのアセットを仮想マシンに手動でインストールする必要はありません。 IoT Edge を自分のデバイスにインストールする場合は、[Azure IoT Edge for Linux (バージョン 1.2) のインストール](how-to-install-iot-edge.md)に関するページまたは [IoT Edge のバージョン 1.2 への更新](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)に関するページを参照してください。

   この ARM テンプレートを使用して正常に仮想マシンが作成されると、仮想マシンの `SSH` ハンドルと完全修飾ドメイン名 (`FQDN`) が出力されます。 各仮想マシンの SSH ハンドルと FQDN (または IP アドレス) は、この後の手順の構成で使用するため、この情報を記録しておいてください。 サンプル出力を下の図に示します。

   >[!TIP]
   >IP アドレスと FQDN は、Azure portal でも確認できます。 IP アドレスについては、仮想マシンの一覧に移動し、 **[パブリック IP アドレス] フィールド** に注目します。 FQDN については、各仮想マシンの概要ページに移動し、 **[DNS 名]** フィールドを探します。

   ![仮想マシンが作成されるときに、その SSH ハンドルを含んだ JSON が出力される](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* 最下位レイヤー デバイスを除くすべてのデバイスで、8000、443、5671、8883 の各ポートが受信用に開放されていることを確認します。
  * 8000: API プロキシを介して Docker コンテナー イメージをプルする際に使用されます。
  * 443:親エッジ ハブと子エッジ ハブ間で REST API 呼び出しに使用されます。
  * 5671、8883: AMQP と MQTT に使用されます。

  詳細については、「[Azure portal を使用して仮想マシンへのポートを開く方法](../virtual-machines/windows/nsg-quickstart-portal.md)」を参照してください。

## <a name="configure-your-iot-edge-device-hierarchy"></a>IoT Edge デバイス階層を構成する

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>IoT Edge デバイスの階層を作成する

IoT Edge デバイスにより、階層のレイヤーが構成されます。 このチュートリアルでは、2 つの IoT Edge デバイス (**最上位レイヤー デバイス** とその子の **下位レイヤー デバイス**) の階層を作成します。 必要に応じて、追加の子デバイスを作成できます。

IoT Edge デバイスの階層を作成して構成するには、`iotedge-config` ツールを使用します。 このツールでは、さまざまな手順を自動化して 2 つにまとめることで、階層の構成を簡素化します。

1. 次のように、クラウドの構成を設定し、各デバイスの構成を準備する。

   * IoT ハブにデバイスを作成する
   * デバイス間の通信を承認するための親子関係を設定する
   * 各デバイスが相互に安全な通信を確立するための証明書チェーンを生成する
   * 各デバイスの構成ファイルを生成する

1. 次のように、各デバイスの構成をインストールする。

   * 各デバイスに証明書をインストールする
   * 各デバイスに構成ファイルを適用する

`iotedge-config` ツールでは、IoT Edge デバイスへのモジュールのデプロイも自動的に実行されます。

`iotedge-config` ツールを使用して階層の作成と構成を行うには、Azure CLI で以下の手順に従います。

1. [Azure Cloud Shell](https://shell.azure.com/) で、チュートリアルのリソース用にディレクトリを作成します。

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. 構成ツールと構成テンプレートのリリースをダウンロードします。

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   これで、チュートリアル ディレクトリに `iotedge_config_cli_release` フォルダーが作成されます。

   デバイス階層の作成に使用されるテンプレート ファイルは、`~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` にある `iotedge_config.yaml` ファイルです。 同じディレクトリ内の `deploymentLowerLayer.json` は、**下位レイヤー デバイス** にデプロイするモジュールについての命令を含んだ JSON デプロイ ファイルです。 `deploymentTopLayer.json` ファイルも同じですが、これは **最上位レイヤー デバイス** 用です。各デバイスにデプロイされるモジュールが異なるためです。 `device_config.toml` ファイルは、IoT Edge デバイスの構成のためのテンプレートです。階層内のデバイスに対する構成バンドルを自動的に生成する目的で使用されます。

   `iotedge-config` ツールのソース コードとスクリプトを確認したい場合は、[GitHub の Azure-Samples リポジトリ](https://github.com/Azure-Samples/iotedge_config_cli)を参照してください。

1. チュートリアルの構成テンプレートを開き、実際の情報に基づいて編集します。

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   **iothub** セクションの `iothub_hostname` フィールドと `iothub_name` フィールドに実際の情報を設定します。 この情報は、Azure portal に表示される IoT ハブの概要ページで確認できます。

   省略可能な **certificates** セクションの各フィールドには、証明書とキーの絶対パスを設定できます。 これらのフィールドを空のままにした場合、自己署名テスト証明書がスクリプトによって自動的に生成されます。 ゲートウェイのシナリオにおける証明書の使い方に詳しくない場合は、[使用法ガイドの証明書に関するセクション](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)を参照してください。

   **configuration** セクションの `template_config_path` は、デバイス構成の作成に使用する `device_config.toml` テンプレートのパスです。 `default_edge_agent` フィールドでは、下位レイヤー デバイスがどの Edge Agent イメージをどこからプルするかを決定します。

   運用環境のシナリオでは、**edgedevices** セクションで階層ツリーを編集して必要な構造を反映することができます。 このチュートリアルの目的では、既定のツリーをそのまま使用します。 デバイスごとに `device_id` フィールドが存在し、そこでデバイスに名前を付けることができます。 また、そのデバイスのデプロイ JSON のパスを指定する `deployment` フィールドもあります。

   IoT Edge デバイスは、Azure portal または Azure Cloud Shell から手動で IoT ハブに登録することもできます。 その方法については、[IoT Edge デバイスの登録方法に関するガイド](how-to-register-device.md)を参照してください。

   親子関係は手動で定義することもできます。 詳細については、使用法ガイドの「[ゲートウェイ階層を作成する](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy)」セクションを参照してください。

   ![階層の定義は、構成ファイルの edgedevices セクションで行うことができる](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. ファイルを保存して閉じます。

   `CTRL + S`, `CTRL + Q`

1. チュートリアル リソースのディレクトリに、構成バンドルの出力ディレクトリを作成します。

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. `iotedge_config_cli_release` ディレクトリに移動し、ツールを実行して IoT Edge デバイスの階層を作成します。

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   `--output` フラグを指定すると、このツールでは、選択したディレクトリにデバイス証明書、証明書バンドル、ログ ファイルが作成されます。 `-f` フラグを設定すると、エラーを回避し、IoT ハブをクリーンな状態に保つために、IoT ハブにある既存の IoT Edge デバイスが自動的に検索され、削除されます。

   この構成ツールにより、IoT Edge デバイスが作成され、そのデバイス間に親子関係が設定されます。 必要に応じて、デバイスで使用するための証明書が作成されます。 デプロイ JSON のパスを指定した場合、デバイスに対するデプロイが自動的に作成されますが、これは必須ではありません。 最後に、デバイスの構成バンドルが生成されて、出力ディレクトリに格納されます。 この構成ツールによって実行される手順を詳しく調べるには、出力ディレクトリにあるログ ファイルを参照してください。

   ![スクリプトを実行すると、階層のトポロジが表示される](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

スクリプトからのトポロジの出力に問題がないことを再確認します。 階層が正しく構成されたら、先に進むことができます。

### <a name="configure-the-iot-edge-runtime"></a>IoT Edge ランタイムを構成する

構成手順では、デバイスのプロビジョニングに加えて、前に作成した証明書を使用して、階層内のデバイス間に信頼された通信を確立します。 この手順では、階層のネットワーク構造の確立も開始します。 最上位レイヤー デバイスはインターネットに接続することができ、クラウドからランタイム用のイメージをプルできます。一方、下位レイヤー デバイスは、最上位レイヤー デバイス経由でルーティングされて、これらのイメージにアクセスします。

IoT Edge ランタイムを構成するには、セットアップ スクリプトによって作成された構成バンドルをデバイスに適用する必要があります。 **最上位レイヤー デバイス** と **下位レイヤー デバイス** では構成が若干異なるため、各デバイスに適用する構成ファイルがどのデバイスのものかに注意してください。

1. 各デバイスには、それに対応する構成バンドルが必要です。 構成バンドルを各デバイスに移動するには、USB ドライブまたは[セキュア ファイル コピー](https://www.ssh.com/ssh/scp/)を使用できます。

   必ず、正しい構成バンドルを各デバイスに送信してください。

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   `:~` は、構成フォルダーが仮想マシン上のホーム ディレクトリに配置されることを意味します。

1. 仮想マシンにログオンして、構成バンドルをデバイスに適用します。

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. 各デバイスで、構成バンドルを解凍します。 最初に、zip をインストールしておく必要があります。

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. 各デバイスに、構成バンドルを適用します。

   ```bash
   sudo ./install.sh
   ```

   **最上位レイヤー デバイス** では、ホスト名の入力を求めるプロンプトが表示されます。 **下位レイヤー デバイス** では、ホスト名と親のホスト名の入力を求められます。 それぞれのプロンプトで適切な IP または FQDN を入力してください。 どちらを使用してもかまいません。ただし、その選択にはデバイス間で一貫性を持たせてください。 インストール スクリプトの出力結果を以下の図に示します。

   デバイスの構成ファイルに対する変更内容の詳細を知りたい場合は、[使用法ガイドの「デバイスで IoT Edge を構成する」セクション](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)を参照してください。

  ![構成バンドルをインストールすると、デバイス上の config.toml ファイルが更新され、すべての IoT Edge サービスが自動的に再起動されます。](./media/tutorial-nested-iot-edge/configuration-install-output.png)

上記の手順を正しく完了した場合は、デバイスが正しく構成されていることを確認できます。

デバイスで構成および接続チェックを実行します。 **最上位レイヤー デバイス** の場合:

   ```bash
   sudo iotedge check
   ```

**下位レイヤー デバイス** の場合は、診断イメージを手動でコマンドに渡す必要があります。

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

**最上位レイヤー デバイス** では、出力には複数の合格した評価が表示されることが予想されます。 ログ ポリシーに関する警告のほか、ご利用のネットワークによっては DNS ポリシーに関する警告が表示されることもあります。

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

各デバイスの構成が正しいことを確認したら、続行することができます。

## <a name="deploy-modules-to-your-devices"></a>モジュールをデバイスにデプロイする

デバイスへのモジュールのデプロイは、デバイスの作成時に自動的に生成されました。 **最上位レイヤー デバイスと下位レイヤー デバイス** の作成後、そのデプロイ JSON が `iotedge-config-cli` ツールによって入力されました。 各デバイスで IoT Edge ランタイムを構成する間、モジュールのデプロイは保留状態になりました。 ランタイムの構成後、**最上位レイヤー デバイス** に対するデプロイが開始されました。 それらのデプロイが完了した後、**下位レイヤー デバイス** は、**IoT Edge API プロキシ** モジュールを使用して、その必要なイメージをプルすることに成功しています。

[Azure Cloud Shell](https://shell.azure.com/) で、**最上位レイヤー デバイス** のデプロイ JSON を見れば、デバイスにデプロイされたモジュールを把握することができます。

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

**最上位レイヤー デバイス** は、ランタイム モジュールである **IoT Edge エージェント** と **IoT Edge ハブ** に加え、**Docker レジストリ** モジュールと **IoT Edge API プロキシ** モジュールを受け取ります。

**Docker レジストリ** モジュールは、既存の Azure Container Registry を参照します。 この場合、`REGISTRY_PROXY_REMOTEURL` の参照先は Microsoft Container Registry です。 `createOptions` では、通信はポート 5000 で行われることがわかります。

**IoT Edge API プロキシ** モジュールは、HTTP 要求を他のモジュールにルーティングします。これによって下位レイヤー デバイスはストレージに対してコンテナー イメージをプルしたり、BLOB をプッシュしたりできるようになります。 このチュートリアルでは、その通信にポート 8000 を使用しています。さらに、Docker コンテナー イメージ pull request を、ポート 5000 で **Docker レジストリ** モジュールにルーティングするように構成されています。 また、Blob Storage のアップロード要求は、ポート 11002 で AzureBlobStorageonIoTEdge モジュールにルーティングされます。 **IoT Edge API プロキシ** モジュールとその構成方法の詳細については、モジュールの [使用法ガイド](how-to-configure-api-proxy-module.md)を参照してください。

このようなデプロイを Azure portal または Azure Cloud Shell で作成する方法については、[使用法ガイドの最上位レイヤー デバイスに関するセクション](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)を参照してください。

[Azure Cloud Shell](https://shell.azure.com/) で、**下位レイヤー デバイス** のデプロイ JSON を見れば、デバイスにデプロイされたモジュールを把握することができます。

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

`systemModules` では、**下位レイヤー デバイス** のランタイム モジュールが、**最上位レイヤー デバイス** のように `mcr.microsoft.com` からではなく、`$upstream:8000` からプルするように設定されていることがわかります。 **下位レイヤー デバイス** は、クラウドから直接イメージをプルすることができないため、ポート 8000 で Docker イメージ要求を **IoT Edge API プロキシ** モジュールに送信します。 **下位レイヤー デバイス** にデプロイされたもう 1 つのモジュール、**Simulated Temperature Sensor** モジュールも、そのイメージ要求を `$upstream:8000` に対して行います。

このようなデプロイを Azure portal または Azure Cloud Shell で作成する方法については、[使用法ガイドの下位レイヤー デバイスに関するセクション](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)を参照してください。

モジュールの状態は、次のコマンドを使用して確認できます。

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   このコマンドは、edgeAgent の報告されるプロパティすべてを出力します。 デバイスの状態を監視する際に役立つものとしては、"*runtime status (ランタイムの状態)* "、"*runtime start time (ランタイムの開始時刻)* "、"*runtime last exit time (ランタイムの最後の終了時刻)* "、"*runtime restart count (ランタイムの再起動回数)* " が挙げられます。

モジュールの状態は、[Azure portal](https://ms.portal.azure.com/) で確認することもできます。 実際の IoT ハブの **[IoT Edge]** セクションに移動して、デバイスとモジュールを確認してください。

モジュールのデプロイに問題がなければ、次に進むことができます。

## <a name="view-generated-data"></a>生成されたデータを表示する

プッシュした **Simulated Temperature Sensor** モジュールは、サンプル環境データを生成します。 送信されるメッセージには、周囲の温度と湿度、機械の温度と圧力、タイムスタンプが含まれます。

それらのメッセージを [Azure Cloud Shell](https://shell.azure.com/) から確認することもできます。

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>トラブルシューティング

構成の確認やエラーのトラブルシューティングには、`iotedge check` コマンドを実行します。

子のマシンがインターネットに直接アクセスできなくても、入れ子になった階層で `iotedge check` を実行できます。

下位レイヤーから `iotedge check` を実行すると、このプログラムは、ポート 443 を使用して親からイメージをプルしようとします。

このチュートリアルではポート 8000 を使用しているため、それを指定する必要があります。

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

`azureiotedge-diagnostics` の値は、レジストリ モジュールにリンクされたコンテナー レジストリからプルされます。 このチュートリアルでは、既定値の https://mcr.microsoft.com: に設定しています。

| 名前 | 値 |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

プライベート コンテナー レジストリを使用する場合は、コンテナー レジストリにすべてのイメージ (IoTEdgeAPIProxy、edgeAgent、edgeHub、Simulated Temperature Sensor、diagnostics) が存在することを確認してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

課金されないようにするために、ローカル構成と、この記事で作成した Azure リソースを削除できます。

リソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. IoT Edge のテスト リソースを含んだリソース グループの名前を選択します。 

3. リソース グループに含まれるリソースの一覧を確認します。 それらすべてを削除する場合は、**[リソース グループの削除]** を選択します。 一部だけを削除する場合は、削除する各リソースをクリックして個別に削除してください。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、2 つの IoT Edge デバイスをゲートウェイとして構成し、一方をもう一方の親デバイスとして設定しました。 次に、IoT Edge API プロキシ モジュールを使用して、ゲートウェイ経由でコンテナー イメージを子デバイスにプルする方法を示しました。 詳細については、[プロキシ モジュールの使用に関する使用法ガイド](how-to-configure-api-proxy-module.md)を参照してください。

ゲートウェイを使用して IoT Edge デバイスの階層を作成する方法の詳細については、[ダウンストリーム IoT Edge デバイスの接続に関する使用法ガイド](how-to-connect-downstream-iot-edge-device.md)を参照してください。

Azure IoT Edge がビジネス ソリューションを作成する方法をさらに確認するには、他のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [モジュールとして Azure Machine Learning モデルをデプロイする](tutorial-deploy-machine-learning.md)
