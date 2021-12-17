---
title: Azure RTOS 組み込みデバイスのクイックスタートのトラブルシューティング
description: Azure RTOS 組み込みデバイスのクイックスタートの使用時に一般的に発生する問題のトラブルシューティングに役立つ手順
author: JimacoMS4
ms.author: v-jbrannian
ms.service: iot-develop
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.openlocfilehash: 26353064f929c382559216c28651757c7b4ee498
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112124864"
---
# <a name="troubleshooting-the-azure-rtos-embedded-device-quickstarts"></a>Azure RTOS 組み込みデバイスのクイックスタートのトラブルシューティング

[組み込みデバイス開発のクイックスタート](quickstart-devkit-mxchip-az3166.md)に従う過程で、いくつかの一般的な問題に遭遇することがあります。 一般に、問題の発生源は次のいずれかです。

* **環境**。 マシン、ソフトウェア、ネットワークのセットアップと接続。
* **Azure IoT リソース**。 Azure IoT に接続するために作成した IoT ハブとデバイス。
* **デバイス**。 物理ボードとその構成。

この記事では、クイックスタートに取り組む過程で最も一般的に発生する問題について、推奨される解決策を紹介します。

## <a name="prerequisites"></a>前提条件

どのトラブルシューティング手順についても、取り組んでいるクイックスタートに関して次の前提条件を満たしている必要があります。

* クイックスタートの前提となるものとソフトウェア ツールをすべてインストール済みまたは取得済みであること。
* クイックスタートの手順に従って Azure IoT ハブまたは Azure IoT Central アプリケーションを作成し、デバイスを登録してあること。
* クイックスタートの手順に従ってデバイスのイメージを作成してあること。

## <a name="issue-the-source-directory-doesnt-contain-cmakeliststxt-file"></a>問題: ソース ディレクトリに CMakeLists.txt ファイルが存在しない
### <a name="description"></a>説明
この問題は、プロジェクトをビルドしようとしたときに発生する可能性があります。 プロジェクトが GitHub から正しくクローンされていないことが原因です。 プロジェクトには、 **--recursive** フラグを使用しない限り既定ではクローンされないサブモジュールが複数存在します。

### <a name="resolution"></a>解決方法
* Git を使用してリポジトリをクローンする際は、 **--recursive** オプションが存在することを確認してください。

## <a name="issue-the-build-fails"></a>問題: ビルドが失敗する

### <a name="description"></a>説明

この問題は、オブジェクト ファイルのパスが Windows における既定の最大パス長を超えていることが原因で発生する可能性があります。 ビルド出力で次のようなメッセージがないか調べてください。

```output
-- Configuring done
CMake Warning in C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/core/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeLists.txt:
  The object file directory

    C:/embedded quickstarts/areallyreallyreallylongpath/getting-started/NXP/MIMXRT1060-EVK/build/lib/netxduo/addons/azure_iot/azure_iot_security_module/iot-security-module-core/CMakeFiles/asc_security_core.dir/./

  has 208 characters.  The maximum full path to an object file is 250
  characters (see CMAKE_OBJECT_PATH_MAX).  Object file

    src/serializer/extensions/custom_builder_allocator.c.obj

  cannot be safely placed under this directory.  The build may not work
  correctly.


-- Generating done
```

### <a name="resolution"></a>解決方法

このエラーを解決するには、次のいずれかの方法を試すことができます。
* もっと短いパスのディレクトリにリポジトリをクローンしてやり直します。
* Windows 10 バージョン 1607 以降では、「[パスの最大長の制限](/windows/win32/fileio/maximum-file-path-limitation)」の手順に従って、長いパスを有効にします。

## <a name="issue-device-cant-connect-to-iot-hub"></a>問題: デバイスが IoT ハブに接続できない

### <a name="description"></a>説明

Azure リソースを作成してデバイスをフラッシュした後、この問題が発生することがあります。 新しくフラッシュしたデバイスを Azure IoT に接続しようとすると、次のようなコンソール メッセージが表示されます。

```output
Unable to resolve DNS for MQTT Server
```

### <a name="resolution"></a>解決方法

* *azure_config.h* ファイルで IoT の構成に対して入力した構成値のスペルと、大文字と小文字の区別を確認します。 一部の IoT リソース属性の値 (`deviceID`、`primaryKey` など) は、大文字と小文字が区別されます。

## <a name="issue--wi-fi-is-unable-to-connect"></a>問題: Wi-Fi に接続できない

### <a name="description"></a>説明

Wi-Fi 接続を使用するデバイスをフラッシュし、Wi-Fi ネットワークに接続しようとすると、Wi-Fi に接続できないというエラーメッセージが表示されます。

### <a name="resolution"></a>解決方法

* Wi-Fi のネットワークの周波数帯と設定を確認します。 組み込みデバイスのクイックスタートで使用されているデバイスはいずれも 2.4 GHz を使用します。 Wi-Fi ルーターが 2.4 GHz のネットワークをサポートするように構成されていることを確認してください。
* Wi-Fi モードを確認します。 *azure_config.h* ファイルで、WIFI_MODE 定数に使用されている設定を確認します。 Wi-Fi ネットワークのセキュリティ設定または認証設定をチェックして、Wi-Fi セキュリティ モードが構成ファイル内の設定と一致していることを確認してください。

## <a name="issue-flashing-the-board-fails"></a>問題: ボードのフラッシュに失敗する

### <a name="description"></a>説明

デバイスをフラッシュするプロセスを完了できません。 これは次のいずれかの症状に遭遇することで判明します。

* ビルドした * *.bin* イメージ ファイルがデバイスにコピーされない。
* デバイスのフラッシュに使用しているユーティリティで警告またはエラーが発生する。
* デバイスのフラッシュに使用しているユーティリティからプログラミングの正常完了が通知されない。

### <a name="resolution"></a>解決方法

* デバイス上の正しい USB ポートに接続していることを確認します。 一部、複数のポートを備えるデバイスもあります。
* 別のマイクロ USB ケーブルを使用してみます。 一部のデバイスとケーブルには互換性がありません。
* コンピューター上の別の USB ポートに接続してみます。 USB ポートが内部で切断されているか、ソフトウェアで無効になっているか、一時的に使用できない状態になっている可能性があります。
* コンピューターを再起動する

## <a name="issue-device-fails-to-connect-to-port"></a>問題: デバイスがポートに接続できない

### <a name="description"></a>説明

デバイスをフラッシュしてコンピューターに接続した後、ターミナル ソフトウェアに次のようなメッセージが表示されます。

```output
Failed to initialize the port.
Please verify the COM port settings.
```

### <a name="resolution"></a>解決方法

* ターミナル ソフトウェアの設定で **ポート** 設定をチェックし、正しいポートが選択されていることを確認します。 複数のポートが表示されている場合は、Windows デバイス マネージャーを開いて **[ポート]** ノードを選択し、接続されているデバイス用の正しいポートを見つけることができます。

## <a name="issue-terminal-output-shows-garbled-text"></a>問題: ターミナル出力のテキストが文字化けする

### <a name="description"></a>説明

デバイスを正常にフラッシュしてコンピューターに接続した後、ターミナル ソフトウェアに文字化けしたテキスト出力が表示されます。

### <a name="resolution"></a>解決方法

* ターミナル ソフトウェアの設定で、**ボー レート** の設定が *115,200* になっていることを確認します。

## <a name="issue-terminal-output-shows-no-text"></a>問題: ターミナル出力にテキストが表示されない

### <a name="description"></a>説明

デバイスを正常にフラッシュしてコンピューターに接続した後、ターミナル ソフトウェアに出力が表示されません。

### <a name="resolution"></a>解決方法

* ターミナル ソフトウェアの設定がクイックスタートの設定と一致していることを確認します。
* ターミナル ソフトウェアを再起動します。
* デバイス上の **Reset** ボタンを押します。
* USB ケーブルが適切に接続されていることを確認します。

## <a name="issue-communication-between-device-and-iot-hub-fails"></a>問題: デバイスと IoT Hub の間の通信に失敗する

### <a name="description"></a>説明

デバイスをフラッシュしてコンピューターに接続した後、ターミナル ウィンドウに次のようなメッセージが繰り返し表示されます。

```output
Failed to publish temperature
```

### <a name="resolution"></a>解決方法

* *[価格とスケール レベル]* が *Free* と *Standard* のどちらかであることを確認します。 **Basic はサポートされません**。cloud-to-device とデバイス ツインの通信が、Basic ではサポートされないためです。

## <a name="next-steps"></a>次のステップ

この記事で問題を確認した後も、ターミナルでデバイスを監視できなかったり Azure IoT に接続できなかったりする場合、デバイスのハードウェアか物理構成に問題がある可能性があります。 お使いのデバイスの製造元のページを参照して、ドキュメントおよびサポート オプションを見つけてください。

* [STMicroelectronics B-L475E-IOT01](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-discovery-kits/b-l475e-iot01a.html)
* [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK)
* [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro)