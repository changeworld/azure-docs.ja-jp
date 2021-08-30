---
title: スマートフォンを Azure IoT デバイスとして使用する
description: Azure IoT プラグ アンド プレイ アプリを使用してスマートフォンを IoT デバイスに変える方法を紹介した攻略ガイド。
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: how-to
ms.date: 05/27/2021
ms.author: dobett
ms.openlocfilehash: 0daffdab23d5da069598512f1934cfdc04bf01b3
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112306136"
---
# <a name="how-to-turn-your-smartphone-into-an-iot-device"></a>スマートフォンを IoT デバイスに変える方法

Azure IoT ソリューションを使用すると、IoT デバイスをクラウドベースの IoT サービスに接続することができます。 デバイスは、温度や湿度といったテレメトリを送信したり、再起動や配信間隔の変更といったコマンドに応答したりします。 また、デバイスの内部状態をサービスと同期させて、デバイス モデルやオペレーティング システムなどのプロパティを共有することもできます。

IoT プラグ アンド プレイ スマートフォン アプリを使用すれば、専用の IoT デバイスを構成しなくても、Azure IoT の機能をすぐに体験できます。

## <a name="azure-iot-plug-and-play-app"></a>Azure IoT プラグ アンド プレイ アプリ

この記事では、すぐに体験できるよう、スマートフォン アプリを IoT デバイスとして使用します。 このアプリで、スマートフォンのセンサーから収集されたテレメトリを送信したり、サービスから呼び出されたコマンドに応答したり、プロパティ値をレポートしたりします。

このスマートフォン アプリを使用すると、次のことができます。

- 基本的な IoT シナリオを試す。
- スマートフォンをリモートから管理したり対話的に操作したりする。
- 構成をテストする。
- カスタム デバイス開発の出発点とする。

## <a name="install-the-app"></a>アプリをインストールする

[!INCLUDE [iot-phoneapp-install](../../includes/iot-phoneapp-install.md)]

## <a name="app-features"></a>アプリの機能

### <a name="connect"></a>接続

IoT Central アプリケーションには、IoT Central で QR コードをスキャンして接続できます。

詳細については、このガイドで後述する「[アプリを接続する](#connect-the-app)」を参照してください。

### <a name="telemetry"></a>テレメトリ

このアプリは、ご利用の IoT サービスにテレメトリとして送信するデータをスマートフォン上のセンサーから収集します。 既定では、センサー データが 5 秒おきに集計されますが、この時間はアプリの設定ページで変更できます。

:::image type="content" source="media/iot-phone-app-how-to/telemetry.png" alt-text="スマートフォン アプリのテレメトリ ページのスクリーンショット。":::

次のスクリーンショットに示したのは、IoT Central のデバイス ビューです。いくつかのデバイス テレメトリが表示されています。

:::image type="content" source="media/iot-phone-app-how-to/central-telemetry.png" alt-text="IoT Central におけるデバイス テレメトリのスクリーンショット。":::

### <a name="properties"></a>Properties

このアプリは、デバイスのモデルや製造元など、デバイスの状態をレポートします。 編集可能なプロパティもあります。それらのプロパティに変更を加えて、Azure IoT ソリューションで見ると、その変更が同期されていることがわかります。

:::image type="content" source="media/iot-phone-app-how-to/properties.png" alt-text="モバイル デバイス アプリのプロパティ ページを示すスクリーンショット。":::

次のスクリーンショットは、書き込み可能なプロパティがデバイスに送信された後、そのプロパティを IoT Central で見たところです。

:::image type="content" source="media/iot-phone-app-how-to/central-writable-property.png" alt-text="IoT Central における書き込み可能なプロパティを示すスクリーンショット。":::

### <a name="image-upload"></a>イメージのアップロード

IoT Central と IoT Hub ではどちらも、デバイスから Azure Storage にファイルをアップロードすることができます。 デバイスからのイメージのアップロードは、スマートフォン アプリを使用して行うことができます。

デバイスからのファイルのアップロードをサポートするようにサービスを構成する方法について詳しくは、次の記事を参照してください。

- [IoT Hub を使用してデバイスからクラウドにファイルをアップロードする](../iot-hub/iot-hub-csharp-csharp-file-upload.md)。
- [IoT Central を使用してデバイスからクラウドにファイルをアップロードする](../iot-central/core/howto-configure-file-uploads.md)。

:::image type="content" source="media/iot-phone-app-how-to/image-upload.png" alt-text="イメージをアップロードするためのスマートフォン アプリ ページを示すスクリーンショット。":::

### <a name="logs"></a>ログ

スマートフォン アプリは、ローカル ログ ファイルにイベントを書き込みます。これらのログ ファイルはアプリ内から閲覧できます。 ログ ファイルは、トラブルシューティングを行ったり、アプリの動作を詳しく把握したりする目的で使用します。

:::image type="content" source="media/iot-phone-app-how-to/logs.png" alt-text="スマートフォン アプリのログ ページを示すスクリーンショット。":::

### <a name="settings"></a>設定

アプリの設定ページでは、次のことができます。

- アプリを Azure IoT ソリューションに接続する。
- 現在のデバイス登録情報を確認する。
- 保存されたデータをクリアしてアプリをリセットする。
- アプリの外観をカスタマイズする。
- アプリから IoT サービスにテレメトリが送信される頻度を設定する。

:::image type="content" source="media/iot-phone-app-how-to/settings.png" alt-text="スマートフォン アプリの設定ページのスクリーンショット。":::

## <a name="connect-the-app"></a>アプリを接続する

### <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

<!-- To do: does this need an app template? -->
IoT Central アプリケーションを作成します。 詳細については、「[IoT Central アプリケーションを作成する](../iot-central/core/howto-create-iot-central-application.md)」を参照してください。

### <a name="register-a-device"></a>デバイスを登録する

スマートフォン アプリを接続する前に、IoT Central アプリケーションにデバイスを登録する必要があります。 デバイス登録を作成すると、IoT Central によってデバイスの接続情報が生成されます。

IoT Central にデバイスを登録するには:

1. IoT Central アプリケーションにサインインし、 **[デバイス]** ページに移動します。

1. **[デバイスの作成]** を選択します。

1. **[新しいデバイスの作成]** ページで **[作成]** を選択します。

    :::image type="content" source="media/iot-phone-app-how-to/iot-central-create-device.png" alt-text="IoT Central でのデバイスの作成方法を示すスクリーンショット。":::

1. デバイスの一覧でデバイス名をクリックし、 **[接続]** を選択します。 **[デバイス接続]** ページで、スマートフォン アプリでスキャンする QR コードを確認できます。

    :::image type="content" source="media/iot-phone-app-how-to/device-connection-qr-code.png" alt-text="QR コードが表示される [デバイス接続] ページのスクリーンショット。":::

### <a name="connect-the-device"></a>デバイスを接続する

IoT Central にデバイスを登録した後、QR コードをスキャンすることでスマートフォン アプリを接続できます。 アプリを接続するには:

1. スマートフォンで **IoT PnP** アプリを開きます。

1. ようこそのページで、 **[Scan QR code]\(QR コードのスキャン\)** を選択します。 スマートフォンのカメラで QR コードをポイントします。 その後、接続が確立されるまで数秒間待ちます。

1. アプリのテレメトリのページで、アプリから IoT Central に送信されているデータを確認できます。 ログのページで、デバイスが接続中であることと、いくつかの初期化メッセージを確認できます。

1. **[設定] > [登録]** ページで、アプリで IoT Central への接続に使用したデバイス ID と ID スコープを確認できます。

デバイスから IoT Central に接続する方法について詳しくは、「[Azure IoT Central に接続する](../iot-central/core/concepts-get-connected.md)」を参照してください。

### <a name="verify-the-connection"></a>接続を確認する

デバイスから送信されているデータを IoT Central アプリケーションで表示するには:

1. IoT Central アプリケーションにサインインし、 **[デバイス]** ページに移動します。 デバイスは自動的に **Smartphone** デバイス テンプレートに割り当てられています。

    > [!TIP]
    > **Smartphone** デバイス テンプレートにいつデバイスが割り当てられたかを確認するには、Web ブラウザーでページを最新の情報に更新しなければならない場合があります。

1. デバイスの一覧でデバイス名をクリックし、 **[概要]** を選択します。 スマートフォンのセンサーからのテレメトリが **[概要]** ページに表示されます。

    :::image type="content" source="media/iot-phone-app-how-to/smartphone-overview.png" alt-text="IoT Central のデバイスの概要ページのスクリーンショット。スマートフォンのセンサーから送信されたテレメトリが表示されている。":::

1. デバイスから送信されたプロパティを **[About]\(情報\)** ページで確認します。

1. **[コマンド]** ページで **LightOn** コマンドを実行すると、スマートフォンの懐中電灯がオンになります。

> [!TIP]
> **[生データ]** ページには、デバイスから送信されているすべてのデータが表示されます。

## <a name="next-steps"></a>次のステップ

スマートフォン アプリを IoT Central に接続したら、次のステップとして、[IoT Central](../iot-central/core/overview-iot-central.md) について詳しい知識を身に付けることをお勧めします。
