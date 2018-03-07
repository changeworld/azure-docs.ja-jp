---
title: "MXChip IoT DevKit を使用して Azure IoT Hub Device Provisioning Service に接続する方法 | Microsoft Docs"
description: "MXChip IoT DevKit を使用して Azure IoT Hub Device Provisioning Service に接続する方法"
services: iot-dps
keywords: 
author: liydu
ms.author: liydu
ms.date: 02/20/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 77c8a6a5e384d27dca2582cc0fedc2bd23c0592e
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service への MXChip IoT DevKit の接続

この記事では、Device Provisioning Service を使用して IoT Hub に自動的に登録されるように DevKit を構成する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

* デバイスで DPS のグローバル エンドポイントを構成する
* Unique Device Secret (UDS) を使用して X.509 証明書を生成する
* 個々のデバイスを登録する
* デバイスが登録されていることを確認する

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 [Arduino 向け Visual Studio Code 拡張機能](https://aka.ms/arduino)を使用して開発できます。 また、Microsoft Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、以下が必要になります。

* [ファースト ステップ ガイド](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)を参照して DevKit を準備します。
* [ファームウェアのアップグレード](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)のチュートリアルを参照して、最新のファームウェア (1.3.0 以上) にアップグレードします。
* [自動プロビジョニングの設定](https://docs.microsoft.com/en-us/azure/iot-dps/quick-setup-auto-provision)に関する記事を参照して、IoT Hub を作成し、Device Provisioning Service インスタンスとリンクさせます。

## <a name="set-up-the-device-provisioning-service-configuration-on-the-device"></a>デバイスで Device Provisioning Service の構成を設定する

DevKit を有効にして、作成した Device Provisioning Service インスタンスに接続するには、次の手順を実行します。

1. Azure Portal で、Device Provisioning Service の **[概要]** を選択し、**[グローバル デバイス エンドポイント]** と **[ID スコープ]** の値を書き留めます。
  ![DPS のグローバル エンドポイントと ID スコープ](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. マシンに `git` がインストールされ、コマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 最新バージョンをインストールするには、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページをご覧ください。

3. コマンド プロンプトを開きます。 DPS サンプル コードの GitHub リポジトリを複製します。
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. VS Code を起動し、DevKit をコンピューターに接続して、複製したコードが含まれているフォルダーを開きます。

5. **DevKitDPS.ino** を開き、`[Global Device Endpoint]` と `[ID Scope]` を見つけて、先ほど書き留めておいた値に置き換えます。
  ![DPS エンドポイント](./media/how-to-connect-mxchip-iot-devkit/endpoint.png)**registrationId** は空白のままでかまいません。MAC アドレスとファームウェア バージョンに基づいて、アプリケーションによって自動的に生成されます。 登録 ID をカスタマイズする場合は、最大 128 文字で、英数字、小文字、ハイフンの組み合わせを使用する必要があります。 詳細については、[Azure Portal でのデバイス登録の管理](https://docs.microsoft.com/en-us/azure/iot-dps/how-to-manage-enrollments)に関する記事をご覧ください。

6. VS Code で **Quick Open** (Windows: `Ctrl+P`、macOS: `Cmd+P`) を使用し、「**task device-upload**」と入力して、コードのビルドと DevKit へのアップロードを実行します。

7. 出力ウィンドウで、タスクが正常に完了したことを確認します。

## <a name="save-unique-device-secret-on-stsafe-security-chip"></a>STSAFE セキュリティ チップに Unique Device Secret を保存する

Device Provisioning Service は、[ハードウェア セキュリティ モジュール (HSM)](https://azure.microsoft.com/en-us/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) に基づいてデバイスで構成できます。 DevKit では、[Trusted Computing Group (TCG)](https://trustedcomputinggroup.org) の [Device Identity Composition Engine (DICE)](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) を使用します。 DevKit の STSAFE セキュリティ チップに保存された **Unique Device Secret (UDS)** は、デバイス固有の [X.509](https://docs.microsoft.com/en-us/azure/iot-dps/tutorial-set-up-device#select-a-hardware-security-module) 証明書の生成に使用されます。 この証明書は、後で Device Provisioning Service での登録プロセスに使用できます。

標準的な **Unique Device Secret (UDS)** は、64 文字の長さの文字列です。 UDS のサンプルは次のとおりです。

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

セキュリティの計算で、2 文字ずつ、16 進値として使用されます。 したがって、上記の UDS のサンプルは、"`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`" に解決されます。

Unique Device Secret を DevKit に保存するには、次の手順を実行します。

1. Putty などのツールを使用してシリアル モニターを開きます。詳細については、「[Use configuration mode (構成モードの使用)](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)」をご覧ください。

2. コンピューターに接続された DevKit で、A ボタンを押しながら、リセット ボタンを押して離すと、構成モードになります。 画面に DevKit の ID と **"構成"** が表示されます。

3. 上記の長い UDS 文字列のサンプルを取得し、1 つまたは複数の文字を `0` ～ `f` の他の値に変更します。 これは独自の UDS として使用されます。

4. シリアル モニター ウィンドウで、「**set_dps_uds [独自の UDS 値]**」と入力し、Enter キーを押して保存します。
  > [!NOTE]
  > たとえば、最後の 2 つの文字を `f` に変更して独自の UDS を設定した場合は、**set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff** のようなコマンドを入力する必要があります。

5. シリアル モニター ウィンドウを閉じずに、DevKit のリセット ボタンを押します。

6. **DevKit MAC Address** と **DevKit Firmware Version** の値を書き留めます。
  ![ファームウェア バージョン](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-x509-certificate"></a>X.509 証明書を生成する

### <a name="windows"></a>Windows

1. エクスプローラーを開き、複製した DSP サンプル コードが含まれているフォルダーに移動します。**.build** フォルダーで、**DPS.ino.bin** と **DPS.ino.map** を見つけてコピーします。
  ![生成されたファイル](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Arduino の `built.path` 構成を他のフォルダーに変更した場合は、 構成済みのフォルダーでこれらのファイルを見つける必要があります。

2. この 2 つのファイルを、**.build** フォルダーと同じレベルの **tools** フォルダーに貼り付けます。

3. **dps_cert_gen.exe** を実行し、メッセージに従って **UDS**、DevKit の **MAC アドレス**、**ファームウェア バージョン**を入力して、X.509 証明書を生成します。
  ![dps-cert-gen.exe の実行](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. 生成が正常に完了したことを確認します。**.pem** 証明書が同じフォルダーに保存されます。

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>Device Provisioning Service でのデバイス登録エントリの作成

1. Azure Portal でプロビジョニング サービスに移動します。 **[登録を管理します]** をクリックし、**[個々の登録]** タブを選択します。![個々の登録](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. **[追加]**をクリックします。

3. **[メカニズム]** で **[X.509]** を選択します。
  ![証明書のアップロード](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

4. **[.pem または .cer の証明書ファイルです]** で、先ほど生成された **.pem** 証明書をアップロードします。

5. 残りを既定値のままにし、**[保存]** をクリックします。

## <a name="start-the-devkit"></a>DevKit を起動する

1. VS Code を起動し、シリアル モニターを開きます。

2. DevKit の**リセット** ボタンを押します。

DevKit が Device Provisioning Service への登録を開始したことがわかります。

![VS Code の出力](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-the-devkit-is-registered-on-iot-hub"></a>DevKit が IoT Hub に登録されていることを確認する

デバイスが起動すると、次のアクションが実行されます。

1. デバイスが Device Provisioning Service に登録要求を送信します。
2. Device Provisioning Service が登録チャレンジを送信し、デバイスがこれに応答します。
3. 登録が成功すると、Device Provisioning Service は、IoT ハブの URI、デバイス ID、暗号化されたキーをデバイスに送信します。
4. デバイス上の IoT Hub クライアント アプリケーションがハブに接続します。
5. ハブに正常に接続されると、IoT ハブの Device Explorer にデバイスが表示されます。
  ![登録されたデバイス](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-device-id"></a>デバイス ID の変更

Azure IoT Hub に登録されている既定のデバイス ID は **AZ3166** です。 これを変更する場合は、[こちらの指示](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)に従ってください。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、[FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからお問い合わせください。

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

ここでは、DICE を使用してデバイスを DPS に安全に登録するために DevKit を準備する方法を説明しました。これで、IoT Hub にゼロタッチで自動的に登録できます。 このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * デバイスで DPS のグローバル エンドポイントを構成する
> * Unique Device Secret (UDS) を使用して X.509 証明書を生成する
> * 個々のデバイスを登録する
> * デバイスが登録されていることを確認する

他のチュートリアルに進んで、次の方法を学習してください。

> [!div class="nextstepaction"]
> [シミュレートされたデバイスの作成とプロビジョニング](./quick-create-simulated-device.md)

