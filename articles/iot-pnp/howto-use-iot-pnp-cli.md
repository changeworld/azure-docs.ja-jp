---
title: Azure CLI 用 Azure IoT 拡張機能を使用して、IoT プラグ アンド プレイ プレビュー デバイスと対話する | Microsoft Docs
description: Azure CLI 用 Azure IoT 拡張機能をインストールし、IoT ハブに接続された IoT プラグ アンド プレイ デバイスとの対話に使用します。
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352023"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールして使用する

[Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) は、IoT ハブなどの Azure リソースを管理するためのオープンソースのクロス プラットフォーム コマンドライン ツールです。 Azure CLI は、Windows、Linux、および MacOS で使用できます。 Azure CLI は [Azure Cloud Shell](https://shell.azure.com) にもプレインストールされています。 Azure CLI を使用すると、拡張機能をインストールしなくても、Azure IoT Hub リソース、デバイス プロビジョニング サービス インスタンス、およびリンク済みのハブを管理できます。

Azure CLI の Azure IoT 拡張機能は、IoT プラグ アンド プレイ プレビュー デバイスとの対話とテストに使用されるコマンドライン ツールです。 拡張機能は、次のことに使用できます。

- デバイスに接続する。
- デバイスが送信するテレメトリを表示する。
- デバイスのプロパティを操作する。
- デバイスのコマンドを呼び出す。

この記事で取り上げるテクニック:

- Azure CLI 用 Azure IoT 拡張機能をインストールして構成する。
- 拡張機能を使用して、デバイスとの対話とテストを行う。

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能をインストールする

### <a name="step-1---install-the-azure-cli"></a>手順 1 - Azure CLI をインストールする

[インストール手順](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に従って、環境内の Azure CLI を設定します。 最適なエクスペリエンスになるようにするには、Azure CLI のバージョンがバージョン 2.9.1 以上である必要があります。 検証するには、`az -–version` を使用します。

### <a name="step-2---install-iot-extension"></a>手順 2 - IoT 拡張機能をインストールする

[IoT 拡張機能の readme](https://github.com/Azure/azure-iot-cli-extension) に、拡張機能をインストールするためのいくつかの方法が説明されています。 最も簡単な方法は、`az extension add --name azure-iot` を実行することです。 インストール後、`az extension list` を使用して、現在インストールされている拡張機能を確認することができます。また、`az extension show --name azure-iot` を使用して、IoT 拡張機能の詳細を表示することもできます。 この記事の執筆時点での拡張機能のバージョン番号は `0.9.7` です。

拡張機能を削除するには、`az extension remove --name azure-iot` を使用します。

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Azure CLI 用 Azure IoT 拡張機能を使用する

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションにサインインするには、次のコマンドを実行します。

```azurecli
az login
```

> [!NOTE]
> Azure クラウド シェルを使用している場合は、自動的にサインインされるので、前のコマンドを実行する必要はありません。

Azure CLI 用 Azure IoT 拡張機能を使用するには、以下が必要です。

- Azure IoT Hub。 お使いの Azure サブスクリプションに IoT ハブを追加する方法にはさまざまなものがあります。一例を挙げると、[Azure CLI を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-using-cli.md)という方法があります。 Azure IoT 拡張機能コマンドを実行するには、IoT ハブの接続文字列が必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

- お使いの IoT ハブに登録されているデバイス。 次の Azure CLI コマンドを使用してデバイスを登録することができます。`{YourIoTHubName}` と `{YourDeviceID}` のプレースホルダーは必ず実際の値に置き換えてください。

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>デバイスと対話する

この拡張機能を使用して、IoT ハブに接続されている IoT プラグ アンド プレイ デバイスを表示し、操作できます。 この拡張機能は、IoT プラグ アンド プレイ デバイスを表すデジタル ツインで動作します。

#### <a name="list-devices"></a>デバイスを一覧表示する

IoT ハブ上のすべてのデバイスの一覧を表示します。

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>デジタル ツインを表示する

デバイスのデジタル ツインを表示します。

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Properties

読み取りおよび書き込みプロパティの値を設定します。

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>コマンド

コマンドを呼び出します。

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>デジタル ツイン イベント

**$Default** イベント ハブ コンシューマー グループにアクセスする特定のデバイスとインターフェイスからのすべての IoT プラグ アンド プレイ デジタル ツイン イベントを監視します。

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>次のステップ

このハウツー記事では、Azure CLI 用 Azure IoT 拡張機能をインストールして使用し、プラグ アンド プレイ デバイスと対話する方法を学習しました。 次は、[デバイスで Azure IoT Explorer](./howto-use-iot-explorer.md) を使用する方法について学習することをお勧めします。
