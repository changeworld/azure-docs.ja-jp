---
title: クイックスタート - Azure IoT Central アプリケーションを作成して使用する | Microsoft Docs
description: クイックスタート - 新しい Azure IoT Central アプリケーションを作成し、最初のデバイスを接続します。 このクイックスタートでは、Google Play または Apple App Store のスマートフォン アプリを IoT デバイスとして使用します。
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 4e40c079c50ccb7f83a4e03d47cf3e9b419870ef
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2021
ms.locfileid: "112288531"
---
# <a name="quickstart---create-an-azure-iot-central-application-and-use-your-smartphone-to-send-telemetry"></a>クイックスタート - Azure IoT Central アプリケーションを作成し、スマートフォンを使用してテレメトリを送信する

このクイックスタートでは、Azure IoT Central アプリケーションを作成して最初のデバイスを接続する方法を説明します。 すぐに作業を開始するには、スマートフォンにアプリをインストールして、デバイスとして動作させることができます。 アプリでテレメトリの送信、プロパティのレポート、コマンドへの応答を行います。

:::image type="content" source="media/quick-deploy-iot-central/overview.png" alt-text="スマートフォン アプリを IoT Central に接続するクイックスタート シナリオの概要。" border="false":::

## <a name="prerequisites"></a>前提条件

有効な Azure サブスクリプション Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!TIP]
> Azure サブスクリプションに、少なくとも **共同作成者** のアクセス権を持っている必要があります。 ご自身でサブスクリプションを作成した場合は、ご自分が自動的に十分なアクセス権を持つ管理者になります。 詳細については、「[Azure ロールベースのアクセス制御とは](../../role-based-access-control/overview.md)」を参照してください。

公式アプリ ストアのどれかから無料のアプリをインストールできる Android または iOS 搭載のスマートフォン。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central ビルド](https://aka.ms/iotcentral) Web サイトに移動します。 次に、お使いの Azure サブスクリプションに関連付けられている Microsoft 個人、職場、または学校アカウントを使用してサインインします。

IoT Central には、さまざまな業界に特化したアプリケーション テンプレートが用意されていて、作業を開始する際に役立ちます。 このクイックスタートでは、**カスタム アプリケーション** テンプレートを使用して、アプリケーションを最初から作成します。

1. **[ビルド]** ページに移動し、 **[カスタム アプリ]** タイルの **[アプリの作成]** を選択します。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="「IoT アプリケーションのビルド」ページ":::

1. **[新しいアプリケーション]** ページで、 **[アプリケーション テンプレート]** の下で **[カスタム アプリケーション]** が選択されていることを確認します。

1. Azure IoT Central によって、選択したアプリケーション テンプレートに基づいて **アプリケーション名** が自動的に提案されます。 *Contoso quickstart app* など、独自のアプリケーション名を入力します。

1. Azure IoT Central によって、アプリケーション名に基づいて、一意の **URL** プレフィックスも生成されます。 この URL は、アプリケーションにアクセスするために使用します。 必要に応じて、この URL プレフィックスをもっと覚えやすいものに変更します。 この URL は一意である必要があります。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Azure IoT Central の [アプリケーションの作成] ページ":::

1. このクイックスタートでは、料金プランを **Standard 2** に設定されたままにしておきます。

1. **[Azure サブスクリプション]** ドロップダウンでサブスクリプションを選択します。

1. **[場所]** ドロップダウンで最も近い場所を選択します。

1. 使用条件を確認し、ページの下部にある **[作成]** を選択します。 数秒後に、IoT Central アプリケーションを使用できるようになります。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Azure IoT Central アプリケーション":::

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT Central アプリケーションに接続するには、いくつかの接続情報が必要です。 この接続情報を取得する簡単な方法は、デバイスを登録することです。

デバイスを登録するには:

1. IoT Central で **[デバイス]** ページに移動し、 **[デバイスの作成]** を選択します。

    :::image type="content" source="media/quick-deploy-iot-central/create-device.png" alt-text="IoT Central でのデバイスの作成を示すスクリーンショット。":::

1. **[新しいデバイスを作成します]** ページで、既定値をそのまま使用して **[作成]** を選択します。

1. デバイスの一覧にあるデバイス名をクリックします。

    :::image type="content" source="media/quick-deploy-iot-central/device-name.png" alt-text="選択できる強調表示されたデバイス名を示すスクリーンショット。":::

1. デバイスのページで、 **[接続]** を選択し、次に **[QR コード]** を選択します。

    :::image type="content" source="media/quick-deploy-iot-central/device-registration.png" alt-text="スマートフォン アプリの接続に使用できる QR コードを示すスクリーンショット。":::

このページは開いたままにしておきます。 次のセクションでは、スマートフォン アプリを使用してこの QR コードをスキャンし、IoT Central に接続します。

## <a name="connect-your-device"></a>デバイスを接続する

この記事では、すぐに始められるように、**IoT プラグ アンド プレイ** スマートフォン アプリを IoT デバイスとして使用します。 このアプリで、スマートフォンのセンサーから収集されたテレメトリを送信し、IoT Central から呼び出されたコマンドに応答して、プロパティ値を IoT Central に報告します。

[!INCLUDE [iot-phoneapp-install](../../../includes/iot-phoneapp-install.md)]

**IoT プラグ アンド プレイ** アプリを IoT Central アプリケーションに接続するには:

1. スマートフォンで **IoT PnP** アプリを開きます。

1. ようこそのページで、 **[Scan QR code]\(QR コードのスキャン\)** を選択します。 スマートフォンのカメラで QR コードをポイントします。 その後、接続が確立されるまで数秒間待ちます。

1. アプリのテレメトリのページで、アプリから IoT Central に送信されているデータを確認できます。 ログのページで、デバイスが接続中であることと、いくつかの初期化メッセージを確認できます。

IoT Central でスマートフォン アプリからのテレメトリを表示するには:

1. IoT Central で、 **[デバイス]** ページに移動します。

1. デバイスの一覧で、デバイス名をクリックし、 **[概要]** を選択します。

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-telemetry.png" alt-text="テレメトリのプロットが示されている概要ページのスクリーンショット。":::

> [!TIP]
> スマートフォン アプリからのデータは画面がオンのときにのみ送信されます。
## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、IoT Central アプリケーションを作成し、テレメトリを送信するデバイスを接続しました。 このクイックスタートでは、IoT Central に接続する IoT デバイスとしてスマートフォン アプリを使用しました。 IoT Central についての学習を続ける場合、推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [IoT Central アプリケーションにルールを追加する](./quick-configure-rules.md)
