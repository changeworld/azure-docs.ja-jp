---
title: Azure Digital Twins を使用した OPC UA データの取り込み
titleSuffix: Azure Digital Twins
description: Azure OPC UA データを Azure Digital Twins に取り込む手順
author: danhellem
ms.author: dahellem
ms.date: 8/27/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 41ae76350ef5483323edd0a5b244c3b64e08c674
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263922"
---
# <a name="ingesting-opc-ua-data-with-azure-digital-twins"></a>Azure Digital Twins を使用した OPC UA データの取り込み

[OPC 統合アーキテクチャ (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/) は、製造領域向けのプラットフォームに依存しないサービス指向アーキテクチャです。 デバイスからテレメトリ データを取得するために使用されます。 

OPC UA サーバーのデータを Azure Digital Twins に送るには、異なるデバイスに複数のコンポーネントをインストールし、いくつかのカスタム コードと設定を構成する必要があります。 

この記事では、これらすべての要素を接続して OPC UA ノードを Azure Digital Twins に取り込む方法について説明します。 このガイダンスを足掛かりにして、独自のソリューションを構築することもできます。

> [!NOTE]
> この記事では、OPC UA ノードの DTDL への変換については取り上げません。 OPC UA サーバーから既存の Azure Digital Twins へのテレメトリの取り込みのみを取り上げます。 OPC UA データから DTDL モデルを生成する場合は、[UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) および [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL) リポジトリを参照してください。

## <a name="prerequisites"></a>前提条件

この記事を完了する前に、次の前提条件を完了してください。
* **サンプル リポジトリのダウンロード**: この記事では、[OPC UA to Azure Digital Twins GitHub リポジトリ](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)の [DTDL モデル](concepts-models.md) ファイルと Azure 関数本体を使用します。 まず、サンプル リポジトリをコンピューターにダウンロードします。 リポジトリの **[コード]** ボタンを選択して、リポジトリを複製するか、.zip ファイルとしてコンピューターにダウンロードできます。

    :::image type="content" source="media/how-to-ingest-opcua-data/download-repo.png" alt-text="コードを複製またはダウンロードする手順を強調表示した GitHub の Digital Twins サンプル リポジトリを示すスクリーンショット。" lightbox="media/how-to-ingest-opcua-data/download-repo.png":::
    
    リポジトリを .zip としてダウンロードする場合は、必ずそれを解凍してファイルを抽出してください。
* **Visual Studio のダウンロード**: この記事では、Visual Studio を使用して Azure 関数を発行します。 Visual Studio の最新バージョンは、[Visual Studio のダウンロード](https://visualstudio.microsoft.com/downloads/) ページからダウンロードできます。

## <a name="architecture"></a>アーキテクチャ

このソリューションに含められるコンポーネントを次に示します。

 :::image type="content" source="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png" alt-text="OPC UA to Azure Digital Twins のアーキテクチャを示す図" lightbox="media/how-to-ingest-opcua-data/opcua-to-adt-diagram-1.png":::    

| コンポーネント | 説明 |
| --- | --- |
| OPC UA サーバー | OPC UA データをシミュレートする [Prosys](https://www.prosysopc.com/products/opc-ua-simulation-server/) または [Kepware](https://www.kepware.com/en-us/products/#KEPServerEX) の OPC UA サーバー。 |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) | IoT Edge は、ローカルの Linux ゲートウェイ デバイスにインストールされる IoT Hub サービスです。 OPC Publisher モジュールを実行して IoT Hub にデータを送信するために必要です。 |
| [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) | このコンポーネントは、Azure Industrial IoT チームによって構築された IoT Edge モジュールです。 このモジュールでは、OPC UA サーバーに接続し、ノード データを Azure IoT Hub に送信します。 |
| [Azure IoT Hub](../iot-hub/about-iot-hub.md) | OPC Publisher では、OPC UA テレメトリを Azure IoT Hub に送信します。 そこから、Azure 関数を使用してデータを処理し、Azure Digital Twins に取り込むことができます。 |
| Azure Digital Twins | 現実世界のモノ、場所、ビジネス プロセス、人のデジタル表現を作成できるプラットフォーム。 |
| [Azure 関数](../azure-functions/functions-overview.md) | カスタム Azure 関数を使用して、Azure IoT Hub 内の適切なツインへのテレメトリのフローと Azure Digital Twins のプロパティを処理します。 |

## <a name="set-up-edge-components"></a>エッジ コンポーネントの設定

最初の手順では、エッジのデバイスとソフトウェアを設定します。 設定するエッジ コンポーネントを次の順序で示します。
1. [OPC UA シミュレーション サーバー](#set-up-opc-ua-server)
1. [IoT Hub と IoT Edge デバイス](#set-up-iot-edge-device)
1. [ゲートウェイ デバイス](#set-up-gateway-device)

このセクションでは、それぞれの設定について簡単に説明します。 

これらの各要素のインストールの詳細については、次のリソースを参照してください。
* [Azure IoT Edge に OPC Publisher をインストールするためのステップバイステップ ガイド](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Linux に IoT Edge をインストールする](../iot-edge/how-to-provision-single-device-linux-symmetric.md) 
* [GitHub の OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher)
* [OPC Publisher の構成](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)

### <a name="set-up-opc-ua-server"></a>OPC UA サーバーの設定

この記事では、実際の OPC UA サーバーを実行している物理デバイスにアクセスする必要はありません。 代わりに、無料の [Prosys OPC UA シミュレーション サーバー](https://www.prosysopc.com/products/opc-ua-simulation-server/)を Windows VM にインストールして、OPC UA データを生成できます。 このセクションでは、この設定手順について説明します。

使用したい物理 OPC UA デバイスまたは別の OPC UA シミュレーション サーバーが既にある場合は、次の「[IoT Edge デバイスの設定](#set-up-iot-edge-device)」に進むことができます。

#### <a name="create-windows-10-virtual-machine"></a>Windows 10 仮想マシンの作成

Prosys ソフトウェアには、シンプルな仮想リソースが必要です。 [Azure portal](https://portal.azure.com) を使用して、次の仕様を満たす [Windows 10 仮想マシン (VM) を作成](../virtual-machines/windows/quick-create-portal.md)します。
* [可用性オプション]: **[インフラストラクチャ冗長は必要ありません]**
* **[イメージ]** : [Windows 10 Pro、バージョン 2004 - Gen2]
* **[サイズ]** : [Standard_B1s - 1 vCPU、1 GiB メモリ]

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png" alt-text="Windows 仮想マシン設定の [基本] タブが表示された Azure portal のスクリーンショット。" lightbox="media/how-to-ingest-opcua-data/create-windows-virtual-machine-1.png":::

VM には、インターネット経由で到達できる必要があります。 このチュートリアルでは、単純化するため、すべてのポートを開き、VM にパブリック IP アドレスを割り当てることができます。 仮想マシン設定の **[ネットワーク]** タブで、これを行うことができます。

:::image type="content" source="media/how-to-ingest-opcua-data/create-windows-virtual-machine-2.png" alt-text="Windows 仮想マシン設定の [ネットワーク] タブが表示された Azure portal のスクリーンショット。":::

> [!WARNING]
> 運用ソリューションでは、セキュリティ リスクが発生する可能性があるため、インターネットに対してすべてのポートを開くことはお勧めしません。 場合によっては、お使いの環境に合わせて他のセキュリティ戦略を検討する必要があります。

次の手順で使用する **パブリック IP 値** を収集します。
 
VM 設定を終了します。

#### <a name="install-opc-ua-simulation-software"></a>OPC UA シミュレーション ソフトウェアのインストール

新しい Windows 仮想マシンから、[Prosys OPC UA シミュレーション サーバー](https://www.prosysopc.com/products/opc-ua-simulation-server/)をインストールします。

ダウンロードとインストールが完了したら、サーバーを起動します。 OPC UA サーバーが起動するまで、しばらく時間がかかる場合があります。 準備が完了すると、[サーバーの状態] が **[実行中]** と表示されます。

次に、 **[接続アドレス (UA TCP)]** の値をコピーします。 それを後で使用できるように安全な場所に貼り付けます。 貼り付けた値のうち、アドレスのコンピューター名の部分を、次のように先ほどの VM の **パブリック IP** に置き換えます。 

`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`

この更新した値は、この記事で後ほど使用します。

最後に、 **[オブジェクト]** タブを選択し、Objects::FolderType および Simulation::FolderType フォルダーを展開して、既定でサーバーに用意されているシミュレーション ノードを表示します。 シミュレーション ノードがそれぞれの固有の `NodeId` 値と共に表示されます。 

発行するシミュレーション ノードの `NodeId` 値をキャプチャします。 これらの ID は、この記事の後半でこれらのノードからデータをシミュレートするため必要になります。

> [!TIP]
> [Azure IoT Edge に OPC Publisher をインストールするためのステップ バイ ステップ ガイド](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher)の「OPC UA サービスが実行され、到達可能であることを確認する」手順に従って、OPC UA サーバーがアクセス可能であることを確認します。

#### <a name="verify-completion"></a>完了の確認

このセクションでは、データをシミュレートするための OPC UA サーバーを設定しました。 次のチェックリストを完了したことを確認してください。

> [!div class="checklist"]
> * Prosys シミュレーション サーバーが設定され、実行されている
> * UA TCP 接続アドレス (`opc.tcp://<ip-address>:53530/OPCUA/SimulationServer`) をコピーした
> * 発行するシミュレーション ノードの `NodeId` のリストをキャプチャした 

### <a name="set-up-iot-edge-device"></a>IoT Edge デバイスの設定

このセクションでは、IoT Hub インスタンスと IoT Edge デバイスを設定します。 

まず、[Azure IoT Hub インスタンスを作成](../iot-hub/iot-hub-create-through-portal.md)します。 この記事では、**F1 - Free** レベルでインスタンスを作成できます。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-hub.png" alt-text="IoT Hub のプロパティが表示された Azure portal のスクリーンショット。":::

Azure IoT Hub インスタンスを作成した後、インスタンスの左側のナビゲーション メニューから **[IoT Edge]** を選択し、 **[IoT Edge デバイスの追加]** を選択します。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-1.png" alt-text="Azure portal で IoT Edge デバイスを追加する画面のスクリーンショット。":::

プロンプトに従って新しいデバイスを作成します。 

デバイスが作成されたら、 **[プライマリ接続文字列]** または **[セカンダリ接続文字列]** のいずれかの値をコピーします。 この値は、後でエッジ デバイスを設定するときに必要になります。

:::image type="content" source="media/how-to-ingest-opcua-data/iot-edge-2.png" alt-text="IoT Edge デバイスの接続文字列が表示された Azure portal のスクリーンショット。":::

#### <a name="verify-completion"></a>完了の確認

このセクションでは、ゲートウェイ デバイスを作成する準備として、IoT Edge と IoT Hub を設定しました。 次のチェックリストを完了したことを確認してください。
> [!div class="checklist"]
> * IoT Hub インスタンスが作成されている。
> * IoT Edge デバイスがプロビジョニングされている。

### <a name="set-up-gateway-device"></a>ゲートウェイ デバイスの設定

OPC UA サーバーのデータを IoT Hub に取り込むには、OPC Publisher モジュールを使用して IoT Edge を実行するデバイスが必要です。 OPC Publisher では、その後、OPC UA ノードの更新をリッスンし、テレメトリを JSON 形式で IoT Hub に発行します。

#### <a name="create-ubuntu-server-virtual-machine"></a>Ubuntu サーバー仮想マシンの作成

[Azure portal](https://portal.azure.com) を使用して、次の仕様を満たす Ubuntu サーバー仮想マシンを作成します。
* [可用性オプション]: **[インフラストラクチャ冗長は必要ありません]**
* **[イメージ]** : [Ubuntu Server 18.04 LTS - Gen1]
* **[サイズ]** : [Standard_B1ms - 1 vCPU、2 GiB メモリ]
    - 既定のサイズ (Standard_b1s – 1 vCPU、1 GiB メモリ) は、RDP 用としてはパフォーマンスが低すぎます。 2 GiB メモリに更新すると、より優れた RDP エクスペリエンスが提供されます。

:::image type="content" source="media/how-to-ingest-opcua-data/ubuntu-virtual-machine.png" alt-text="Ubuntu 仮想マシンの設定が表示された Azure portal のスクリーンショット。":::

> [!NOTE]
> Ubuntu VM に RDP 接続する場合は、[xrdp をインストールして、Ubuntu でリモート デスクトップを使用するように構成する](../virtual-machines/linux/use-remote-desktop.md)手順に従います。

#### <a name="install-iot-edge-container"></a>IoT Edge コンテナーのインストール

[Linux に IoT Edge をインストールする](../iot-edge/how-to-provision-single-device-linux-symmetric.md)手順に従います。

インストールが完了したら、次のコマンドを実行してインストールの状態を確認します。

```bash
admin@gateway:~$ sudo iotedge check
```

このコマンドでは、いくつかのテストを実行し、インストールの準備ができているかどうかを確認します。

#### <a name="install-opc-publisher-module"></a>OPC Publisher モジュールのインストール

次に、OPC Publisher モジュールをゲートウェイ デバイスにインストールします。 

まず、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.iotedge-opc-publisher) からモジュールを入手します。

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-1.png" alt-text="Azure Marketplace の OPC Publisher を示すスクリーンショット。":::

次に、[OPC Publisher GitHub リポジトリ](https://github.com/Azure/iot-edge-opc-publisher)に記載されているインストール手順に従って、モジュールを Ubuntu VM にインストールします。

[コンテナー作成オプションを指定](https://github.com/Azure/iot-edge-opc-publisher#specifying-container-create-options-in-the-azure-portal)する手順では、必ず次の json を追加してください。

```JSON
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=/appdata/publishednodes.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

:::image type="content" source="media/how-to-ingest-opcua-data/opc-publisher-2.png" alt-text="OPC Publisher のコンテナー作成オプションを示すスクリーンショット。":::

>[!NOTE]
>上記の作成オプションは、ほとんどの場合、変更なしで動作しますが、これまでの記事のガイダンスとは異なる独自のゲートウェイ デバイスを使用する場合は、状況に合わせて設定を調整する必要があります。

残りのプロンプトに従ってモジュールを作成します。 

約 15 秒後、ゲートウェイ デバイスで `iotedge list` コマンドを実行すると、IoT Edge デバイスで実行されているすべてのモジュールが一覧表示されます。 OPCPublisher モジュールが稼働していることがわかります。

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-list.png" alt-text="IoT Edge の一覧の結果を示すスクリーンショット。":::

最後に、`/iiotedge` ディレクトリに移動して *publishednodes.json* ファイルを作成します。 ファイル内の ID は、[前に OPC サーバーから収集した](#install-opc-ua-simulation-software) `NodeId` 値と一致する必要があります。 ファイルは、次のようになります。

```JSON
[
    {
        "EndpointUrl": "opc.tcp://20.185.195.172:53530/OPCUA/SimulationServer",
        "UseSecurity": false,
        "OpcNodes": [
            {
                "Id": "ns=3;i=1001"
            },
            {
                "Id": "ns=3;i=1002"
            },
            {
                "Id": "ns=3;i=1003"
            },
            {
                "Id": "ns=3;i=1004"
            },
            {
                "Id": "ns=3;i=1005"
            },
            {
                "Id": "ns=3;i=1006"
            }
        ]
    }
]
```

変更内容を *publishednodes.json* ファイルに保存します。

次に、次のコマンドを実行します。

```bash
sudo iotedge logs OPCPublisher -f
```

このコマンドを実行すると、OPC Publisher のログが出力されます。 すべてが正しく構成され、実行されている場合は、次のようなスクリーンショットが表示されます。

:::image type="content" source="media/how-to-ingest-opcua-data/iotedge-logs.png" alt-text="ターミナルの IoT Edge ログを示すスクリーンショット。左側に診断情報フィールドの列があり、右側に値の列があります。":::

これで、データが OPC UA サーバーから IoT Hub に送られているはずです。

Azure IoT Hub へのメッセージ フローを監視するには、次のコマンドを使用します。

```azurecli-interactive
az iot hub monitor-events -n <iot-hub-instance> -t 0
```

> [!TIP]
> [Azure IoT Explorer](../iot-fundamentals/howto-use-iot-explorer.md) を使用して IoT Hub メッセージを監視することもできます。

#### <a name="verify-completion"></a>完了の確認

このセクションでは、OPC UA サーバーからデータを受信する IoT Edge を実行するゲートウェイ デバイスを設定しました。 次のチェックリストを完了したことを確認してください。
> [!div class="checklist"]
> * Ubuntu サーバー VM が作成された。
> * IoT Edge が Ubuntu VM 上にインストールされ、存在している。
> * OPC Publisher モジュールがインストールされた。
> * *publishednodes.json* ファイルが作成され、構成された。
> * OPC Publisher モジュールが実行されており、テレメトリ データが IoT Hub に送られている。

次の手順では、このテレメトリ データを Azure Digital Twins に取り込みます。

## <a name="set-up-azure-digital-twins"></a>Azure Digital Twins を設定する

OPC UA サーバーから Azure IoT Hub にデータが送られるようになったので、次の手順として、Azure Digital Twins を設定して構成します。 

この例では、1 つのモデルと 1 つのツイン インスタンスを使用して、シミュレーション サーバーのプロパティを照合します。 

>[!TIP]
>より多くのモデルとツインを含むより複雑なシナリオに関心がある場合は、[OPC UA to Azure Digital Twins GitHub リポジトリ](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)にあるチョコレート工場の例を参照してください。

### <a name="create-azure-digital-twins-instance"></a>Azure Digital Twins インスタンスの作成

まず、「[インスタンスと認証を設定する](how-to-set-up-instance-portal.md)」のガイダンスを使用して、新しい Azure Digital Twins インスタンスをデプロイします。

### <a name="upload-model-and-create-twin"></a>モデルのアップロードとツインの作成

次に、モデルとツインをインスタンスに追加します。 インスタンスにアップロードするモデル ファイルは、「[前提条件](#prerequisites)」セクションでダウンロードしたサンプル プロジェクトの一部であり、*Simulation Example/simulation-dtdl-model.json* にあります。

[Azure Digital Twins Explorer](concepts-azure-digital-twins-explorer.md) を使用してシミュレーション モデルをアップロードし、**simulation-1** という新しいツインを作成できます。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-digital-twins-explorer.png" alt-text="シミュレーション モデルと simulation-1 ツインが表示された Azure Digital Twins Explorer のスクリーンショット。":::

### <a name="verify-completion"></a>完了の確認

このセクションでは、モデルとツインを含む Azure Digital Twins インスタンスを設定しました。 次のチェックリストを完了したことを確認してください。
> [!div class="checklist"]
> * Azure Digital Twins インスタンスがデプロイされた。
> * シミュレーション モデルが Azure Digital Twins インスタンスにアップロードされた。
> * シミュレーション モデルから simulation-1 ツインが作成された。

## <a name="set-up-azure-function"></a>Azure 関数の設定

OPC UA ノードから IoT Hub にデータが送信され、Azure Digital Twins インスタンスでデータを受信できるようになったので、Azure Digital Twins の正しいツインとプロパティにデータをマップして保存する必要があります。 このセクションでは、Azure 関数と *opcua-mapping.json* ファイルを使用してこれを設定します。

このセクションのデータ フローには、次の手順が関与します。

1. Azure 関数で、イベント サブスクリプションを使用して、IoT Hub から送信されたメッセージを受信します。
1. この Azure 関数で、受信した各テレメトリ イベントを処理します。 イベントから `NodeId` を抽出し、*opcua-mapping.json* ファイル内の項目と照合します。 このファイルでは、各 `NodeId` が、ノードの値が保存されている Azure Digital Twins の特定の `twinId` とプロパティにマップされています。
1. この Azure 関数では、対応するデジタル ツインを更新するための適切なパッチ ドキュメントを生成し、ツイン プロパティの更新コマンドを実行します。

### <a name="create-opcua-mappingjson-file"></a>opcua-mapping.json ファイルの作成

まず、*opcua-mapping.json* ファイルを作成します。 空の JSON ファイルから開始し、以下の例とスキーマに従って `NodeId` 値を Azure Digital Twins の `twinId` 値とプロパティにマップするエントリを入力します。 すべての `NodeId` に対してマッピング エントリを作成する必要があります。

```JSON
[
    {
        "NodeId": "1001",
        "TwinId": "simulation",
        "Property": "Counter",
        "ModelId": "dtmi:com:microsoft:iot:opcua:simulation;1"
    },
    ...
]
```

エントリのスキーマを次に示します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| NodeId | OPC UA ノードからの値。 例: ns=3;i={value} | ✔ |
| TwinId | テレメトリ値を保存するツインの TwinId ($dtId) | ✔ |
| プロパティ | テレメトリ値を保存するツインのプロパティの名前 | ✔ |
| ModelId  | TwinId が存在しない場合にツインを作成するための modelId |  |

> [!TIP]
> *opcua-mapping.json* ファイルの完全な例については、[OPC UA to Azure Digital Twins GitHub リポジトリ](https://github.com/Azure-Samples/opcua-to-azure-digital-twins)を参照してください。

マッピングの追加が完了したら、ファイルを保存します。

マッピング ファイルが作成されたので、Azure 関数からアクセスできる場所にこれを格納する必要があります。 そのような場所の 1 つが、[Azure Blob Storage](../storage/blobs/storage-blobs-overview.md) です。

[ストレージ コンテナーを作成する](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)手順に従って、*opcua-mapping.json* ファイルをコンテナーにインポートします。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用してストレージ管理イベントを実行することもできます。 

次に、[コンテナーの Shared Access Signature](../storage/common/storage-sas-overview.md) を作成し、その URL を保存します。 後で Azure 関数にこの URL を指定すれば、格納されているファイルにアクセスできます。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-storage-explorer.png" alt-text="SAS トークンを作成するためのダイアログが表示された Azure Storage Explorer のスクリーンショット。":::

### <a name="publish-azure-function"></a>Azure 関数の発行

このセクションでは、「[前提条件](#prerequisites)」でダウンロードした Azure 関数を発行します。この関数では、OPC UA データを処理し、Azure Digital Twins を更新します。

1. ローカル コンピューターにダウンロードされた [OPC UA to Azure Digital Twins](https://github.com/Azure-Samples/opcua-to-azure-digital-twins) プロジェクトに移動し、*Azure Functions/OPCUAFunctions* フォルダーに移動します。 Visual Studio で **OPCUAFunctions.sln** ソリューションを開きます。
2. プロジェクトを Azure 内の関数アプリに発行します。 これを行う方法については、「[Visual Studio を使用する Azure Functions の開発](../azure-functions/functions-develop-vs.md#publish-to-azure)」を参照してください。

#### <a name="configure-the-function-app"></a>Function App を構成する

次に、Azure Digital Twins インスタンスにアクセスできるように、関数に **アクセス ロールを割り当て** て、**アプリケーション設定を構成** します。

[!INCLUDE [digital-twins-configure-function-app.md](../../includes/digital-twins-configure-function-app.md)]

#### <a name="add-application-settings"></a>アプリケーションの設定を追加する

環境と Azure 関数を完全に設定するには、さらにいくつかのアプリケーション設定を追加する必要があります。 [Azure portal](https://portal.azure.com) に移動し、ポータルの検索バーで名前を検索して、新しく作成した Azure 関数に移動します。

関数の左側のナビゲーション メニューから [構成] を選択します。 **[+ 新しいアプリケーション設定]** ボタンを使用して、新しい設定の作成を開始します。

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-1.png" alt-text="Azure portal で Azure 関数にアプリケーション設定を追加する画面のスクリーンショット。":::

次の 3 つのアプリケーション設定を作成する必要があります。

| 設定 | 説明 | 必須 |
| --- | --- | --- |
| ADT_SERVICE_URL | Azure Digital Twins インスタンスの URL。 例: `https://example.api.eus.digitaltwins.azure.net` | ✔ |
| JSON_MAPPINGFILE_URL | opcua-mapping.json の Shared Access Signature の URL | ✔ |
| LOG_LEVEL | ログ レベルの詳細度。 既定値は 100 です。 詳細は 300 です | |

:::image type="content" source="media/how-to-ingest-opcua-data/azure-function-settings-2.png" alt-text="Azure portal の Azure 関数のアプリケーション設定を示すスクリーンショット。上記の設定が追加されました。":::

> [!TIP]
> 詳細なログ記録を有効にするには、関数の `LOG_LEVEL` アプリケーション設定を 300 に設定します。 

### <a name="create-event-subscription"></a>イベント サブスクリプションの作成

最後に、関数アプリと *ProcessOPCPublisherEventsToADT* 関数を IoT Hub に接続するイベント サブスクリプションを作成します。 このイベント サブスクリプションは、関数を介してゲートウェイ デバイスから IoT Hub にデータが送られた後、Azure Digital Twins が更新されるために必要です。

手順については、Azure Digital Twins の "*チュートリアル: エンドツーエンドのソリューションを接続する* の「[IoT ハブを Azure 関数に接続する](tutorial-end-to-end.md#connect-the-iot-hub-to-the-azure-function)」で使用されているのと同じ手順に従ってください。

イベント サブスクリプションの [エンドポイントの種類] に **[Azure 関数]** を設定し、[エンドポイント] に **[ProcessOPCPublisherEventsToADT]** を設定します。

:::image type="content" source="media/how-to-ingest-opcua-data/event-subscription.png" alt-text="新しいイベント サブスクリプションの作成を示す Azure portal のスクリーンショット。":::

この手順の後、必要なすべてのコンポーネントをインストールして実行してください。 データが OPC UA シミュレーション サーバーから Azure IoT Hub を介して Azure Digital Twins インスタンスに送られます。 

### <a name="verify-completion"></a>完了の確認

このセクションでは、OPC UA データを Azure Digital Twins に接続する Azure 関数を設定しました。 次のチェックリストを完了したことを確認してください。
> [!div class="checklist"]
> * *opcua-mapping.json* ファイルを作成し、Blob Storage コンテナーにインポートした。 
> * サンプル関数 *ProcessOPCPublisherEventsToADT* を Azure の関数アプリに発行した。
> * Azure Functions アプリに 3 つの新しいアプリケーション設定を追加した。
> * IoT Hub イベントを関数アプリに送信するイベント サブスクリプションを作成した。

次のセクションでは、イベントを監視してすべてが正常に動作していることを確認するために実行できるいくつかの Azure CLI コマンドを示します。

## <a name="verify-and-monitor"></a>確認と監視

このセクションのコマンドは、[Azure Cloud Shell](https://shell.azure.com) または[ローカルの Azure CLI](/cli/azure/install-azure-cli) で実行できます。

次のコマンドを実行して、IoT Hub イベントを監視します。
```azurecli-interactive
az iot hub monitor-events -n <IoT-hub-name> -t 0
```

次のコマンドを実行して、Azure 関数のイベント処理を監視します。
```azurecli-interactive
az webapp log tail –name <function-name> --resource-group <resource-group-name>
```

最後に、Azure Digital Twins Explorer を使用してツイン プロパティの更新を手動で監視できます。 

:::image type="content" source="media/how-to-ingest-opcua-data/adt-explorer-2.png" alt-text="Azure Digital Twins Explorer を使用してツイン プロパティの更新を監視するスクリーンショット":::

## <a name="next-steps"></a>次のステップ

この記事では、シミュレートされた OPC UA サーバーのデータを Azure Digital Twins に取り込み、デジタル ツインのプロパティを更新するための、完全なデータ フローを設定しました。

次に、以下のリソースを使用して、この記事で使用されたサポート ツールおよびプロセスの詳細を確認してください。

* [Azure IoT Edge に OPC Publisher をインストールするためのステップバイステップ ガイド](https://www.linkedin.com/pulse/step-by-step-guide-installing-opc-publisher-azure-iot-kevin-hilscher) 
* [Linux に IoT Edge をインストールする](../iot-edge/how-to-provision-single-device-linux-symmetric.md) 
* [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher)
* [OPC Publisher の構成](/previous-versions/azure/iot-accelerators/howto-opc-publisher-configure)
* [UANodeSetWebViewer](https://github.com/barnstee/UANodesetWebViewer) 
* [OPCUA2DTDL](https://github.com/khilscher/OPCUA2DTDL)