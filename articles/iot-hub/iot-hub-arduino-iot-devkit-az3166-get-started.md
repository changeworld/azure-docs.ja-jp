---
title: 'IoT DevKit をクラウドへ: IoT DevKit AZ3166 を Azure IoT Hub に接続する | Microsoft Docs'
description: このチュートリアルでは、IoT DevKit AZ3166 を設定して Azure IoT Hub に接続し、Azure クラウド プラットフォームにデータを送信する方法について説明します。
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 6d5b0036bb44f301ea0b11e5d984fcd5b4bfac71
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599831"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>IoT DevKit AZ3166 を Azure IoT Hub に接続する

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を使用すると、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションを開発し、プロトタイプを作成できます。 これには、豊富な周辺機器やセンサーを備えた Arduino 互換ボード、オープン ソース ボード パッケージ、および増え続ける[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が含まれます。

## <a name="what-you-do"></a>作業内容

作成する Azure IoT ハブに [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) を接続し、センサーから温度と湿度のデータを収集して、そのデータを IoT ハブに送信します。

DevKit をお持ちでない場合は、 [DevKit シミュレーター](https://azure-samples.github.io/iot-devkit-web-simulator/)を使用するか、[DevKit を購入](https://aka.ms/iot-devkit-purchase)してください。

## <a name="what-you-learn"></a>学習内容

* IoT DevKit をワイヤレス アクセス ポイントに接続し、開発環境を準備する方法。
* IoT ハブを作成し、MXChip IoT DevKit のデバイスを登録する方法。
* MXChip IoT DevKit のサンプル アプリケーションを実行して、センサー データを収集する方法。
* センサー データを IoT ハブに送信する方法。

## <a name="what-you-need"></a>必要なもの

* MXChip IoT DevKit ボードとマイクロ USB ケーブル。 [こちら](https://aka.ms/iot-devkit-purchase)から今すぐ入手できます。
* Windows 10 または macOS 10.10 以降を実行しているコンピューター。
* 有効な Azure サブスクリプション [30 日間の無料試用版 Microsoft Azure アカウント](https://azureinfo.microsoft.com/us-freetrial.html)をアクティブにします。
  
## <a name="prepare-your-hardware"></a>ハードウェアの準備

次のハードウェアをコンピューターに接続します。

* DevKit ボード
* マイクロ USB ケーブル

![必要なハードウェア](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

DevKit をコンピューターに接続するには、次の手順を実行します。

1. USB ケーブルの端部をコンピューターに接続します。

2. マイクロ USB の端部を DevKit に接続します。

3. 電源の緑色の LED で、接続されていることを確認できます。

   ![ハードウェアの接続](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Wi-Fi を構成する

IoT プロジェクトではインターネット接続を使用します。 次の手順を使用して、DevKit が Wi-Fi に接続できるように構成します。

### <a name="enter-ap-mode"></a>AP モードの開始

B ボタンを押しながら、リセット ボタンを押して離し、その後、B ボタンを離します。DevKit が AP モードに切り替わり、Wi-Fi を構成できるようになります。 DevKit のサービス セット識別子 (SSID) と、構成ポータルの IP アドレスが画面に表示されます。

![リセット ボタン、B ボタン、および SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>DevKit AP への接続

ここで、別の Wi-Fi 対応デバイス (コンピューターまたは携帯電話) を使用して、DevKit SSID (前の図で強調表示されています) に接続します。 パスワードは空のままにしてください。

![ネットワーク情報と [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>DevKit 用に Wi-Fi を構成する

コンピューターまたは携帯電話のブラウザーで、DevKit の画面に表示されている IP アドレスを開いて、DevKit を接続する Wi-Fi ネットワークを選択し、パスワードを入力します。 **[接続]** を選択します。

![[パスワード] ボックスと [接続] ボタン](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

接続に成功すると、DevKit は数秒後に再起動されます。 Wi-Fi 名と IP アドレスが画面に表示されます。

![Wi-Fi 名と IP アドレス](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 写真に表示されている IP アドレスは、DevKit 画面に表示されている、実際に割り当てられた IP アドレスと一致しない場合があります。 Wi-Fi は DHCP を使用して IP を動的に割り当てるため、これは正常な動作です。

Wi-Fi の構成後は、デバイスを取り外しても、その接続の資格情報がデバイス上で保持されます。 たとえば、自宅で Wi-Fi を構成した DevKit をオフィスに持っていった場合は、(「AP モードの開始」セクション以降の手順を実行して) AP モードを再構成し、オフィスの Wi-Fi に DevKit を接続する必要があります。 

## <a name="start-using-the-devkit"></a>DevKit の使用を開始する

DevKit で実行されている既定のアプリによって、ファームウェアが最新バージョンであるかどうかが確認され、センサー診断データの一部が表示されます。

### <a name="upgrade-to-the-latest-firmware"></a>最新ファームウェアへのアップグレード

> [!NOTE] 
> バージョン 1.1 以降、DevKit ではブートローダーで ST-SAFE が有効になっています。 v1.1 より前のバージョンを実行している場合は、ファームウェアをアップグレードする必要があります。

ファームウェアのアップグレードが必要な場合は、現在のファームウェア バージョンと最新のファームウェア バージョンが画面に表示されます。 アップグレードするには、[ファームウェアのアップグレード](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) ガイドに従ってください。

![現在および最新のファームウェア バージョンの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> この操作を行う必要があるのは 1 回です。 DevKit での開発を開始して、アプリをアップロードすると、そのアプリに対して最新のファームウェアが適用されます。

### <a name="test-various-sensors"></a>さまざまなセンサーのテスト

B ボタンを押してセンサーをテストします。 B ボタンを押して離す操作を繰り返して、センサーごとのテストを実行します。

![B ボタンとセンサーの表示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>開発環境の準備

次は、開発環境、つまり魅力的な IoT アプリケーションを構築するためのツールおよびパッケージを設定します。 ご利用のオペレーティング システムに応じて、Windows バージョンまたは macOS バージョンを選択できます。

### <a name="windows"></a>Windows

インストール パッケージを使用して、開発環境を準備することをお勧めします。 問題が発生した場合は、[IoT DevKit の手動インストールの手順](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)に従って作業を完了してください。

#### <a name="download-the-latest-package"></a>最新パッケージのダウンロード

ダウンロードする.zip ファイルには、DevKit 開発に必要なツールとパッケージがすべて含まれています。

> [!div class="button"]
[ダウンロード](https://aka.ms/devkit/prod/installpackage/latest)

.zip ファイルに含まれるツールとパッケージは次のとおりです。 既にインストール済みのコンポーネントがある場合、スクリプトによってそのコンポーネントが検出され、スキップされます。

* Node.js と Yarn: セットアップ スクリプトおよび自動化されたタスクのランタイム。

* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows): Azure リソースを管理するためのクロスプラットフォーム コマンドライン エクスペリエンス。 MSI には、依存する Python と pip が含まれています。

* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): DevKit 開発のための軽量なコード エディター。

* [Arduino の Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): Visual Studio Code での Arduino 開発を可能にする拡張機能。

* [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino の拡張機能が依存するツール。

* DevKit ボード パッケージ: DevKit 用のツール チェーン、ライブラリ、およびプロジェクト。

* ST-Link ユーティリティ: 必須のツールとドライバー。

#### <a name="run-the-installation-script"></a>インストール スクリプトの実行

エクスプローラーで .zip ファイルを見つけて展開します。 `install.cmd` を見つけて右クリックし、**[管理者として実行]** を選択します。

![エクスプローラー](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

インストール中、各ツールまたはパッケージのインストールの進行状況が表示されます。

![Installation progress](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

> [!NOTE] 
> 環境によっては、Arduino IDE のインストール時にエラーが表示されることがあります。 このような場合は、[個別に Arduino IDE をインストール](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows)してから、install.cmd をもう一度実行してください。 それ以外の場合は、[IoT DevKit の手動インストールの手順](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/#windows)に従って、すべての必要なツールとパッケージをインストールしてください。

#### <a name="install-drivers"></a>ドライバーのインストール

Arduino の VS Code 拡張機能では、Arduino IDE が使用されます。 Arduino IDE を初めてインストールする場合は、関連するドライバーをインストールするように求められます。

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

インターネットの速度にもよりますが、インストールは約 10 分かかります。 インストールが完了したら、デスクトップに Visual Studio Code と Arduino IDE のショートカットが表示されます。

> [!NOTE] 
> VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 これを解決するには、VS Code を閉じて、Arduino IDE を再起動します。 VS Code によって、Arduino IDE のパスが正しく検索されます。

### <a name="macos"></a>macOS

ワンクリック インストール機能を使用して、開発環境を準備することをお勧めします。 問題が発生した場合は、[IoT DevKit の手動インストールの手順](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)に従って作業を完了してください。

#### <a name="install-homebrew"></a>Homebrew をインストールする

> [!NOTE] 
> Homebrew をインストール済みの場合は、この手順をスキップできます。

[Homebrew のインストール手順](https://docs.brew.sh/Installation.html)に従ってインストールします。

#### <a name="download-the-latest-package"></a>最新パッケージのダウンロード

ダウンロードする.zip ファイルには、DevKit 開発に必要なツールとパッケージがすべて含まれています。

> [!div class="button"]
[ダウンロード](https://aka.ms/devkit/prod/installpackage/mac/latest)

.zip ファイルに含まれるツールとパッケージは次のとおりです。 既にインストール済みのコンポーネントがある場合、スクリプトによってそのコンポーネントが検出され、スキップされます。

* Node.js と Yarn: セットアップ スクリプトおよび自動化されたタスクのランタイム。

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest#a-namemacosinstall-on-macos): Azure リソースを管理するためのクロスプラットフォーム コマンドライン エクスペリエンス。

* [Visual Studio Code](https://code.visualstudio.com/) (VS Code): DevKit 開発のための軽量なコード エディター。

* [Arduino の Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): Visual Studio Code での Arduino 開発を可能にする拡張機能。

* [Arduino IDE](https://www.arduino.cc/en/Main/Software): Arduino の拡張機能が依存するツール。

* DevKit ボード パッケージ: DevKit 用のツール チェーン、ライブラリ、およびプロジェクト。

* ST-Link ユーティリティ: 必須のツールとドライバー。

#### <a name="run-the-installation-script"></a>インストール スクリプトの実行

Finder で .zip を見つけて展開します。

![macOS ファインダー](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-finder.png)

ターミナル アプリを起動し、.zip ファイルを展開したフォルダーに移動して以下の手順を実行します。

```bash
./install.sh
```

![macOS のインストール](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/mac-install-sh.png)

> [!NOTE] 
> Homebrew のアクセス許可エラーが表示された場合は、`brew doctor` を実行して修正します。 詳しくは、[IoT DevKit FAQ の homebrew エラーに関するセクション](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#homebrew-permission-error-on-macos)をご覧ください。

これで、macOS に必要なツールとパッケージがすべてインストールされました。

## <a name="open-the-project-folder"></a>プロジェクト フォルダーを開く

プロジェクト フォルダーを開くことから開始します。 

### <a name="start-vs-code"></a>VS Code の起動

DevKit が接続されていないことを確認します。 VS Code を起動し、DevKit をコンピューターに接続します。 VS Code にって DevKit が自動的に検出され、概要ページが開きます。

![[概要] ページ](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

> [!NOTE] 
> VS Code を起動するときに、Arduino IDE または関連するボード パッケージが見つからないことを示すエラーが表示される場合があります。 VS Code を閉じて、Arduino IDE を再起動します。 VS Code によって、Arduino IDE のパスが正しく検索されます。

### <a name="open-the-arduino-examples-folder"></a>[Arduino Examples]\(Arduino の例\) フォルダーを開く

**[Arduino Examples]\(Arduino の例\)** タブで、**[Examples for MXCHIP AZ3166]** > **[AzureIoT]** に移動し、**[GetStarted]** を選択します。

![[Arduino Examples]\(Arduino の例\) タブ](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/vscode_start.png)

ウィンドウを偶然閉じた場合は、再度開くことができます。 `Ctrl+Shift+P` キー (macOS: `Cmd+Shift+P` キー) を使用してコマンド パレットを開き、「**Arduino**」と入力します。次に、**[Arduino: Examples]\(Arduino: 例\)** を見つけて選択します。

## <a name="provision-azure-services"></a>Azure サービスのプロビジョニング

ソリューション ウィンドウで、「`task cloud-provision`」と入力し、`Ctrl+P` キー (macOS: `Cmd+P` キー) を使用してタスクを実行します。

VS Code ターミナルでは、対話型コマンド ラインを使用して、必要な Azure サービスをプロビジョニングできます。

![対話型コマンド ライン](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>Arduino スケッチのビルドとアップロード

次に、Arduino スケッチをビルドし、アップロードします。

### <a name="windows"></a>Windows

1. `Ctrl+P` キーを使用して `task device-upload` を実行します。

2. ターミナルによって、構成モードを開始するよう求められます。 これを行うには、A ボタンを押しながら、リセット ボタンを押して離します。 画面に、DevKit の ID と "構成" が表示されます。

   これは、`task cloud-provision` の手順から取得する接続文字列を設定するためです。

   その後、VS Code で、Arduino スケッチの確認とアップロードが開始されます。

   ![Arduino スケッチの確認とアップロード](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

   DevKit が再起動され、コードの実行が開始されます。

   > [!NOTE] 
   > "エラー: AZ3166: 不明なパッケージ" エラーが発生する場合があります。 これは、ボード パッケージのインデックスが更新されていないためです。 これを解決するには、こちらの[FAQ の手順](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)を確認してください。

### <a name="macos"></a>macOS

1. DevKit を構成モードにします。A ボタンを押しながら、リセット ボタンを押して離します。 画面に "構成" が表示されます。

2. `Cmd+P` キーを使用して `task device-upload` を実行します。

   これは、`task cloud-provision` の手順から取得する接続文字列を設定するためです。

   その後、VS Code で、Arduino スケッチの確認とアップロードが開始されます。

   ![device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

   DevKit が再起動され、コードの実行が開始されます。

   > [!NOTE] 
   > "エラー: AZ3166: 不明なパッケージ" エラーが発生する場合があります。 これは、ボード パッケージのインデックスが更新されていないためです。 この問題を解決するには、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) にある手順を確認してください。


## <a name="test-the-project"></a>プロジェクトのテスト

VS Code で、次の手順に従ってシリアル モニターを開いて設定します。

1. ステータス バーの `COM[X]` の単語をクリックして、適切な COM ポートを `STMicroelectronics` に設定します。

   ![COM ポート](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png)

2. ステータス バーの電源プラグ アイコンをクリックして、シリアル モニターを開きます。

   ![シリアル モニター](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution//connect-iothub/serial-monitor.png)

3. ステータス バーで、ボー レートを表す数字をクリックして `115200` に設定します。

   ![ボー レート](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

4. 次の結果が表示された場合、サンプル アプリケーションは正常に実行されてます。

   * シリアル モニターに、次のスクリーンショットの内容と同じ情報が表示されている。
   
   * MXChip IoT DevKit の RGB LED が点滅している。

   ![VS Code での最終出力](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[IoT DevKit の FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) で解決方法を確認してください。 このページにコメントを残してフィードバックをお寄せいただくこともできます。

## <a name="next-steps"></a>次の手順

MXChip IoT DevKit を IoT ハブに接続し、キャプチャしたセンサー データを IoT ハブに送信しました。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
