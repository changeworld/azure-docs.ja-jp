---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491138"
---
<!-- All needs updating -->
Azure IoT Central アプリケーションのオペレーターとして、次のことを実行できます。

* 2 つのサーモスタット コンポーネントによって送信されたテレメトリを **[概要]** ページで確認します。

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="デバイス テレメトリの表示":::

* **[About]\(情報\)** ページでデバイスのプロパティを確認します。 このページには、デバイス情報コンポーネントと 2 つのサーモスタット コンポーネントからのプロパティが表示されます。

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="デバイスのプロパティを表示する":::

## <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

温度コントローラー デバイスの接続時に IoT Central によって自動的に作成されたデバイス テンプレートは、ソリューション開発者がカスタマイズできます。

デバイスに関連付けられている顧客名を格納するためのクラウド プロパティを追加するには、次の手順に従います。

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** ページの **Temperature Controller** デバイス テンプレートに移動します。

1. **Temperature Controller** デバイス テンプレートで **[クラウドのプロパティ]** を選択します。

1. **[クラウド プロパティの追加]** を選択します。 **[表示名]** として「*Customer name*」を入力し、 **[スキーマ]** として **[文字列]** を選択します。 次に、 **[保存]** を選択します。

**[Get Max-Min report]\(最大-最小レポートの取得\)** コマンドが IoT Central アプリケーションでどのように表示されるかをカスタマイズするには:

1. デバイス テンプレートで **[カスタマイズ]** を選択します。

1. **getMaxMinReport (thermostat1)** と表示する場合は、 *[Get Max-Min report]\(最大-最小レポートの取得\)* を *[Get thermostat1 status report]\(thermostat1 状態レポートの取得\)* で置き換えます。

1. **getMaxMinReport (thermostat2)** と表示する場合は、 *[Get Max-Min report]\(最大-最小レポートの取得\)* を *[Get thermostat2 status report]\(thermostat 状態レポートの取得\)* で置き換えます。

1. **[保存]** を選択します。

**[Target Temperature]\(目標温度\)** 書き込み可能プロパティが IoT Central アプリケーションにどのように表示されるかをカスタマイズするには:

1. デバイス テンプレートで **[カスタマイズ]** を選択します。

1. **targetTemperature (thermostat1)** と表示する場合は、 *[Target Temperature]\(目標温度\)* を *[Target Temperature (1)]\(目標温度 (1)\)* で置き換えます。

1. **targetTemperature (thermostat2)** と表示する場合は、 *[Target Temperature]\(目標温度\)* を *[Target Temperature (2)]\(目標温度 (2)\)* で置き換えます。

1. **[保存]** を選択します。

**Temperature Controller** モデルのサーモスタット コンポーネントには **[Target Temperature]\(目標温度\)** という書き込み可能プロパティが、デバイス テンプレートには **[Customer Name]\(顧客名\)** というクラウド プロパティがあります。 オペレーターがこれらのプロパティを編集する際に使用できるビューを作成します。

1. **[ビュー]** を選択し、 **[デバイスとクラウドのデータの編集]** タイルを選択します。

1. フォーム名として「_Properties_」を入力します。

1. **[Target Temperature (1)]\(目標温度 (1)\)** 、 **[Target Temperature (2)]\(目標温度 (2)\)** 、および **[Customer Name]\(顧客名\)** の各プロパティを選択します。 次に、 **[セクションの追加]** を選択します。

1. 変更を保存します。

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="プロパティ値を更新するためのビュー":::

## <a name="publish-the-device-template"></a>デバイス テンプレートを公開する

これまでに加えたカスタマイズをオペレーターが見て使用できるようにするためには、デバイス テンプレートを発行する必要があります。

**Thermostat** デバイス テンプレートから **[発行]** を選択します。 **[このデバイス テンプレートのアプリケーションへの発行]** パネルで、 **[発行]** を選択します。

これでオペレーターは、 **[プロパティ]** ビューを使用してプロパティ値を更新したり、デバイスのコマンド ページで **[Get thermostat1 status report]\(thermostat1 状態レポートの取得\)** や **[Get thermostat2 status report]\(thermostat2 状態レポートの取得\)** というコマンドを呼び出したりできるようになりました。

* 書き込み可能なプロパティの値を **[プロパティ]** ページで更新します。

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="デバイス プロパティを更新する":::

* **[コマンド]** ページからコマンドを呼び出します。

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="コマンドを呼び出す":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="コマンドの応答を確認する":::
