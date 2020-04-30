---
title: DevKit デバイスを Azure IoT Central アプリケーションに接続する | Microsoft Docs
description: デバイス開発者として、IoT プラグ アンド プレイ (プレビュー) を使用して MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する方法について学習します。
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756799"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>MXChip IoT DevKit デバイスを Azure IoT Central アプリケーションに接続する

"*この記事は、デバイス開発者を対象としています。* "

この記事では、MXChip IoT DevKit (DevKit) デバイスを Azure IoT Central アプリケーションに接続する方法を説明します。 このデバイスでは、DevKit デバイスの認定された IoT プラグ アンド プレイ (プレビュー) モデルを使用して、IoT Central への接続を構成します。

このハウツー記事では、次のことを行います。

- IoT Central アプリケーションから接続の詳細を取得します。
- デバイスを準備し、IoT Central アプリケーションに接続します。
- IoT Central で、デバイスのテレメトリとプロパティを表示します。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のリソースが必要です。

- [DevKit デバイス](https://aka.ms/iot-devkit-purchase)。
- IoT Central アプリケーション 「[IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」の手順に従うことができます。

## <a name="get-device-connection-details"></a>デバイス接続の詳細を取得する

1. Azure IoT Central アプリケーションで、 **[デバイス テンプレート]** タブを選択し、 **[+ 新規]** を選択します。 **[Use a preconfigured device template]\(構成済みのデバイス テンプレートを使用する\)** のセクションで、 **[MXChip IoT DevKit]** を選択します。

    ![MXChip IoT DevKit のデバイス テンプレート](media/howto-connect-devkit/device-template.png)

1. **[Customize]\(次へ: カスタマイズ\)** 、 **[作成]** の順に選択します。

1. **[デバイス]** タブを選択します。デバイスの一覧で **[MXChip IoT DevKit]** を選択し、 **[+ 新規]** を選択してテンプレートから新しいデバイスを作成します。

    ![新しいデバイス](media/howto-connect-devkit/new-device.png)

1. ポップアップ ウィンドウで、 **[デバイス ID]** を `SampleDevKit`、 **[デバイス名]** を `MXChip IoT DevKit - Sample` のように入力します。 **[シミュレート済み]** オプションがオフになっていることを確認します。 **[作成]** を選択します。

    ![デバイス ID と名前](media/howto-connect-devkit/device-id-name.png)

1. 作成したデバイスを選択し、 **[接続]** を選択します。 **[ID スコープ]** 、 **[デバイス ID]** 、 **[主キー]** をメモします。 この使用方法の記事で、後ほどこれらの値が必要になります。

    ![デバイスの接続情報](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>デバイスの準備

1. GitHub から DevKit デバイス用の最新の[事前構築済み Azure IoT Central プラグ アンド プレイ (プレビュー) ファームウェア](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin)をダウンロードします。

1. USB ケーブルを使用して、DevKit デバイスを開発用コンピューターに接続します。 Windows では、DevKit デバイス上のストレージにマッピングされたドライブでファイル エクスプローラー ウィンドウが開きます。 たとえば、このドライブは **AZ3166 (D:)** と呼ばれることがあります。

1. **iotc_devkit.bin** ファイルをドライブ ウィンドウにドラッグします。 コピーが完了すると、デバイスが新しいファームウェアで再起動します。

    > [!NOTE]
    > **Wi-Fi なし**などのエラーが画面に表示される場合は、DevKit がまだ WiFi に接続していないことが原因です。

1. DevKit で **B ボタン**を押しながら、**リセット** ボタンを押して離した後、**B ボタン** を離します。これで、デバイスがアクセス ポイント モードになりました。 確認するために、画面に "IoT DevKit-AP" と構成ポータルの IP アドレスが表示されます。

1. コンピューターまたはタブレット上で、デバイスの画面に表示されている WiFi ネットワーク名に接続します。 WiFi ネットワークは、**AZ-** で始まり、その後に MAC アドレスが続きます。 このネットワークに接続するとき、インターネット アクセスは存在しません。 この状態は予期されたものであり、このネットワークにはデバイスを構成する間の短時間しか接続されません。

1. Web ブラウザーを開き、[http://192.168.0.1/](http://192.168.0.1/) に移動します。 次の Web ページが表示されます。

    ![構成の UI](media/howto-connect-devkit/config-ui.png)

    Web ページで、以下を入力します。

    - WiFi ネットワークの名前 (SSID)。
    - WiFi ネットワークのパスワード。
    - 接続の詳細: 前にメモした **[デバイス ID]** 、 **[ID スコープ]** 、 **[SAS Primary Key]\(SAS 主キー\)** を入力します。

    > [!NOTE]
    > 現時点では、IoT DevKit は 2.4 GHz の Wi-Fi にのみ接続できます。ハードウェアの制限のため、5 GHz はサポートされていません。

1. **[デバイスの構成]** を選択します。DevKit デバイスが再起動してアプリケーションを実行します。

    ![再起動の UI](media/howto-connect-devkit/reboot-ui.png)

    DevKit の画面に、アプリケーションが実行されていることを確認するメッセージが表示されます。

    ![DevKit が実行中](media/howto-connect-devkit/devkit-running.png)

DevKit では、最初に IoT Central アプリケーションに新しいデバイスが登録されてから、データの送信が開始されます。

## <a name="view-the-telemetry"></a>テレメトリを表示する

この手順では、Azure IoT Central アプリケーションでテレメトリを表示します。

IoT Central アプリケーションで、 **[Devices]\(デバイス\)** タブを選択し、追加したデバイスを選択します。 **[Overview]\(概要\)** タブで、DevKit からのテレメトリを確認できます。

![IoT Central デバイスの概要](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>コードの確認

コードを確認したり、コードを変更してコンパイルしたりするには、[コード サンプル](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)にアクセスします。

## <a name="next-steps"></a>次のステップ

デバイス開発者にお勧めする次の手順は以下です。

- [Azure IoT Central のデバイス接続](./concepts-get-connected.md)について確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
