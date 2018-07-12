---
title: Azure CLI 2.0 向けの IoT 拡張機能を使用した Azure IoT デバイス管理 | Microsoft Docs
description: Azure IoT Hub デバイス管理用の Azure CLI 2.0 向けの IoT 拡張機能を使用します。このツールでは、ダイレクト メソッドとデバイス ツインの必要なプロパティを管理するためのオプションを使用できます。
author: chrissie926
manager: ''
keywords: Azure IoT デバイス管理, Azure IoT Hubデバイス管理, デバイス管理 IoT, IoT Hub デバイス管理
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: dc96e70a031d6080217e71b829ec5de3c64e4cf7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971756"
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Azure CLI 2.0 向けの IoT 拡張機能を使用した Azure IoT Hub デバイス管理

![エンド ツー エンド ダイアグラム](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure CLI 2.0 向けの IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)は新しいオープン ソースの IoT 拡張機能であり、[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) の機能に追加されます。 Azure CLI 2.0 には、Azure Resource Manager と管理エンドポイントと対話するためのコマンドが含まれています。 たとえば、Azure CLI 2.0 を使って Azure VM または IoT ハブを作成できます。 Azure サービスは、CLI 拡張機能を使用して Azure CLI を拡張することで、追加されるサービス固有の機能にアクセスできるようにします。 IoT 開発者は、IoT 拡張機能を使用して、すべての IoT Hub、IoT Edge、および IoT Hub Device Provisioning Service の機能にコマンド ラインからアクセスできます。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理オプション          | タスク                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| ダイレクト メソッド             | メッセージの送信開始や停止、デバイスの再起動などの機能をデバイスに実行させます。                                        |
| デバイス ツインの必要なプロパティ    | デバイスを特定の状態にします (LED を緑に設定したり、テレメトリの送信間隔を 30 分に設定したりします)。         |
| デバイス ツインの報告されるプロパティ   | デバイスの報告される状態を取得します。 たとえば、デバイスは、現在 LED が点滅していることを報告します。                                    |
| デバイス ツインのタグ                  | デバイス固有のメタデータをクラウドに格納します。 例: 自動販売機の設置場所。                         |
| デバイス ツイン クエリ        | すべてのデバイス ツインをクエリして、任意の条件と一致するデバイスを取得します (例: 使用可能なデバイスを識別する)。 |

これらのオプションの相違点の詳細な説明と使用するためのガイダンスについては、「[device-to-cloud 通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」と「[cloud-to-device 通信に関するガイダンス](iot-hub-devguide-c2d-guidance.md)」を参照してください。

デバイス ツインは、デバイスの状態に関する情報 (メタデータ、構成、状態) を格納する JSON ドキュメントです。 IoT Hub は、IoT Hub に接続する各デバイスにデバイス ツインを保持します。 デバイス ツインの詳細については、「[デバイス ツインの使用](iot-hub-node-node-twin-getstarted.md)」を参照してください。

## <a name="what-you-learn"></a>学習内容

開発マシン上で Azure CLI 2.0 向けの IoT 拡張機能をさまざまな管理オプションで使用します。

## <a name="what-you-do"></a>作業内容

Azure CLI 2.0 と Azure CLI 2.0 向けの IoT 拡張機能をさまざまな管理オプションを使用して実行します。

## <a name="what-you-need"></a>必要なもの

- 次の要件に対応するために、[デバイスのセットアップ](iot-hub-raspberry-pi-kit-node-get-started.md)に関するチュートリアルを完了しておきます。
  - 有効な Azure サブスクリプション
  - サブスクリプションの Azure IoT Hub。
  - Azure IoT Hub にメッセージを送信するクライアント アプリケーション。

- このチュートリアルの実行時にクライアント アプリケーションでデバイスが実行されていることを確認します。

- [Python 2.7x または Python 3.x](https://www.python.org/downloads/)

- Azure CLI 2.0 をインストールします。 Windows にインストールする簡単な方法の 1 つは、[MSI](https://aka.ms/InstallAzureCliWindows) をダウンロードしてインストールすることです。 [Microsoft Docs](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) のインストール手順に従って、環境に Azure CLI 2.0 をセットアップすることもできます。 Azure CLI 2.0 のバージョンは、少なくとも 2.0.24 以降である必要があります。 検証するには、`az –version` を使用します。 

- IoT 拡張機能をインストールします。 最も簡単な方法は、`az extension add --name azure-cli-iot-ext` を実行することです。 [IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) に、拡張機能をインストールするためのいくつかの方法が説明されています。


## <a name="log-in-to-your-azure-account"></a>Azure アカウントへのログイン

次のコマンドを実行して Azure アカウントにログインします。

```bash
az login
```

## <a name="direct-methods"></a>ダイレクト メソッド

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>デバイス ツインの目的のプロパティ

次のコマンドを実行して、目的のプロパティ interval = 3000 を設定します。

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

このプロパティをデバイスから読み取ることができます。

## <a name="device-twin-reported-properties"></a>デバイス ツインの報告されるプロパティ

次のコマンドを実行して、デバイスの報告されるプロパティを取得します。

```bash
az iot hub device-twin show -n <your hub name> -d <your device id>
```

ツインの reported プロパティの 1 つは $metadata.$lastUpdated です。これは、デバイス アプリがその reported プロパティのセットを最後に更新した時刻を示します。

## <a name="device-twin-tags"></a>デバイス ツインのタグ

次のコマンドを実行して、デバイスのタグとプロパティを取得します。

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

次のコマンドを実行して、フィールド role = temperature&humidity をデバイスに追加します。

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>デバイス ツイン クエリ

次のコマンドを実行して、タグ role = 'temperature&humidity' のクエリを実行します。

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

次のコマンドを実行して、タグ role = 'temperature&humidity' 以外のすべてのデバイスのクエリを実行します。

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>次の手順

使用している IoT デバイスと Azure IoT Hub の間で D2C メッセージを監視し、C2D メッセージを送信する方法については学習しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
