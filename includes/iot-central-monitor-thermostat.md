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
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017505"
---
Azure IoT Central アプリケーションのオペレーターとして、次のことを実行できます。

* デバイスから送信されたテレメトリを **[概要]** ページで確認します。

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="デバイス テレメトリの表示":::

* **[About]\(情報\)** ページでデバイスのプロパティを確認します。

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="デバイスのプロパティを表示する":::

## <a name="customize-the-device-template"></a>デバイス テンプレートをカスタマイズする

サーモスタット デバイスの接続時に IoT Central によって自動的に作成されたデバイス テンプレートは、ソリューション開発者がカスタマイズできます。

デバイスに関連付けられている顧客名を格納するためのクラウド プロパティを追加するには、次の手順に従います。

1. IoT Central アプリケーションで、 **[デバイス テンプレート]** ページの **Thermostat** デバイス テンプレートに移動します。

1. **Thermostat** デバイス テンプレートで **[クラウドのプロパティ]** を選択します。

1. **[クラウド プロパティの追加]** を選択します。 **[表示名]** として「*Customer name*」を入力し、 **[スキーマ]** として **[文字列]** を選択します。 次に、 **[保存]** を選択します。

IoT Central アプリケーションにおける **[Get Max-Min report]\(最大-最小レポートの取得\)** コマンドの表示方法をカスタマイズするには、デバイス テンプレートの **[カスタマイズ]** を選択します。 **[Get Max-Min report]\(最大-最小レポートの取得\)** を「*Get status report*」に置き換えます。 次に、 **[保存]** を選択します。

**[Thermostat]\(サーモスタット\)** モデルには **[Target Temperature]\(目標温度\)** という書き込み可能プロパティが、デバイス テンプレートには **[Customer Name]\(顧客名\)** というクラウド プロパティがあります。 オペレーターがこれらのプロパティを編集する際に使用できるビューを作成します。

1. **[ビュー]** を選択し、 **[デバイスとクラウドのデータの編集]** タイルを選択します。

1. フォーム名として「_Properties_」を入力します。

1. **[Target Temperature]\(目標温度\)** プロパティと **[Customer Name]\(顧客名\)** プロパティを選択します。 次に、 **[セクションの追加]** を選択します。

1. 変更を保存します。

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="プロパティ値を更新するためのビュー":::

## <a name="publish-the-device-template"></a>デバイス テンプレートを公開する

これまでに加えたカスタマイズをオペレーターが見て使用できるようにするためには、デバイス テンプレートを発行する必要があります。

**Thermostat** デバイス テンプレートから **[発行]** を選択します。 **[このデバイス テンプレートのアプリケーションへの発行]** パネルで、 **[発行]** を選択します。

これで、オペレーターが **[プロパティ]** ビューを使用してプロパティ値を更新したり、デバイスのコマンド ページで "**Get status report** " というコマンドを呼び出したりできるようになりました。

* 書き込み可能なプロパティの値を **[プロパティ]** ページで更新します。

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="デバイス プロパティを更新する":::

* **[コマンド]** ページからコマンドを呼び出します。

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="コマンドを呼び出す":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="コマンドの応答を確認する":::
