---
title: Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録する方法 | Microsoft Docs
description: Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録する方法。
author: liydu
ms.author: liydu
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 300bde27f956b449d1e0e73f7efb54a13df27b0c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145667"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Azure IoT Hub Device Provisioning Service の自動プロビジョニングを使用して MXChip IoT DevKit を IoT Hub に登録します

この記事では、Azure IoT Hub Device Provisioning Service の[自動プロビジョニング](concepts-auto-provisioning.md)を使用して MXChip IoT DevKit を Azure IoT Hub に登録する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

* デバイスにデバイス プロビジョニング サービスのグローバル エンドポイントを構成する。
* 一意デバイス シークレット (UDS) を使って X.509 証明書を生成する。
* 個々のデバイスを登録する。
* デバイスが登録されていることを確認する。

[MXChip IoT DevKit](https://aka.ms/iot-devkit) は、豊富な周辺機器とセンサーを備えたオールインワンの Arduino 互換ボードです。 [Arduino 向け Visual Studio Code 拡張機能](https://aka.ms/arduino)を使って開発できます。 また、DevKit には、Azure サービスを活用したモノのインターネット (IoT) ソリューションのプロトタイプを作成する手順を示す[プロジェクト カタログ](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)が付属しています。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルを完了するには、最初に次のタスクを行います。

* 「[IoT DevKit AZ3166 をクラウドの Azure IoT Hub に接続する](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)」の手順に従って、DevKit を準備します。
* 「[Update DevKit firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)」(DevKit のファームウェアを更新する) チュートリアルで、最新のファームウェア (1.3.0 以降) にアップグレードします。
* 「[Azure Portal で IoT Hub Device Provisioning Service を設定する](/azure/iot-dps/quick-setup-auto-provision)」の手順に従って、IoT Hub を作成し、デバイス プロビジョニング サービス インスタンスとリンクします。

## <a name="build-and-deploy-auto-provisioning-registration-software-to-the-device"></a>自動プロビジョニング登録ソフトウェアをビルドし、デバイスにデプロイする

DevKit を作成したデバイス プロビジョニング サービス インスタンスに接続するには、次の手順を実行します。

1. Azure Portal で、デバイス プロビジョニング サービスの **[概要]** ウィンドウを選び、**[グローバル デバイス エンドポイント]** と **[ID スコープ]** の値を書き留めます。
  ![DPS のグローバル エンドポイントと ID スコープ](./media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

2. マシンに `git` がインストールされていること、およびコマンド ウィンドウからアクセスできる環境変数に追加されていることを確認します。 最新バージョンをインストールするには、[Software Freedom Conservancy の Git クライアント ツール](https://git-scm.com/download/)に関するページをご覧ください。

3. コマンド プロンプトを開きます。 デバイス プロビジョニング サービスのサンプル コードの GitHub リポジトリを複製します。
  ```bash
  git clone https://github.com/DevKitExamples/DevKitDPS.git
  ```

4. Visual Studio Code を開き、DevKit をコンピューターに接続して、複製したコードが含まれているフォルダーを開きます。

5. **DevKitDPS.ino** を開きます。 `[Global Device Endpoint]` と `[ID Scope]` を探して、書き留めた値に置き換えます。
  ![DPS エンドポイント](./media/how-to-connect-mxchip-iot-devkit/endpoint.png)**registrationId** は空白のままでかまいません。 MAC アドレスとファームウェア バージョンに基づいて、アプリケーションによって自動的に生成されます。 登録 ID をカスタマイズする場合は、最大 128 文字で、英数字、小文字、ハイフンの組み合わせを使う必要があります。 詳細については、[Azure Portal でのデバイス登録の管理](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments)に関する記事をご覧ください。

6. VS Code で Quick Open (Windows: `Ctrl+P`、macOS: `Cmd+P`) を使い、「*task device-upload*」と入力して、コードのビルドと DevKit へのアップロードを実行します。

7. 出力ウィンドウに、タスクが正常に完了したかどうかが示されます。

## <a name="save-a-unique-device-secret-on-an-stsafe-security-chip"></a>STSAFE セキュリティ チップに一意のデバイス シークレットを保存する

自動プロビジョニングは、デバイスの[構成証明メカニズム](concepts-security.md#attestation-mechanism)に基づいてデバイスで構成できます。 MXChip IoT DevKit では、[Trusted Computing Group](https://trustedcomputinggroup.org) の [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) を使います。 DevKit の STSAFE セキュリティ チップに保存された "*一意のデバイス シークレット*" (UDS) は、デバイス固有の [X.509](concepts-security.md#x509-certificates) 証明書の生成に使われます。 この証明書は後でデバイス プロビジョニング サービスでの登録プロセスで使用され、また、実行時の登録中に使用されます。

一般的な一意デバイス シークレットは、次の例のような 64 文字の文字列です。

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

文字列は文字のペアに分割され、セキュリティの計算に使用されます。 したがって、前記の UDS のサンプルは、"`0x19`, `0xe2`, `0x5a`, `0x25`, `0x9d`, `0x0c`, `0x2b`, `0xe0`, `0x3a`, `0x02`, `0xd4`, `0x16`, `0xc0`, `0x5c`, `0x48`, `0xcc`, `0xd0`, `0xcc`, `0x7d`, `0x17`, `0x43`, `0x45`, `0x8a`, `0xae`, `0x1c`, `0xb4`, `0x88`, `0xb0`, `0x74`, `0x99`, `0x3e`, `0xae`" に化一決されます。

一意デバイス シークレットを DevKit に保存するには、次の手順を実行します。

1. Putty などのツールを使ってシリアル モニターを開きます。 詳しくは、「[Use configuration mode](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)」(構成モードを使用する) をご覧ください。

2. お使いのコンピューターに接続された DevKit で、**A** ボタンを押しながら、**リセット** ボタンを押して離すと、構成モードになります。 画面に、DevKit の ID と構成が表示されます。

3. UDS 文字列のサンプルを取得し、1 つまたは複数の文字を独自の UDS の `0` ～ `f` の他の値に変更します。

4. シリアル モニター ウィンドウで、「*set_dps_uds <独自の UDS 値>*」と入力し、Enter キーをます。
  > [!NOTE]
  > たとえば、最後の 2 文字を `f` に変更して独自の UDS を設定する場合、`set_dps_uds 19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eff` のようなコマンドを入力する必要があります。

5. シリアル モニター ウィンドウを閉じずに、DevKit の**リセット** ボタンを押します。

6. **DevKit MAC Address** と **DevKit Firmware Version** の値を書き留めます。
  ![ファームウェア バージョン](./media/how-to-connect-mxchip-iot-devkit/firmware-version.png)

## <a name="generate-an-x509-certificate"></a>X.509 証明書を生成する

### <a name="windows"></a>Windows

1. エクスプローラーを開き、前に複製したデバイス プロビジョニング サービスのサンプル コードを含むフォルダーに移動します。 **.build** フォルダーで、**DPS.ino.bin** と **DPS.ino.map** を探して、コピーします。
  ![生成されたファイル](./media/how-to-connect-mxchip-iot-devkit/generated-files.png)
  > [!NOTE]
  > Arduino の `built.path` 構成を別のフォルダーに変更した場合は、構成したフォルダーでこれらのファイルを探す必要があります。

2. この 2 つのファイルを、**.build** フォルダーと同じレベルの **tools** フォルダーに貼り付けます。

3. **dps_cert_gen.exe** を実行します。 メッセージに従って **UDS**、DevKit の **MAC アドレス**、**ファームウェアのバージョン**を入力して、X.509 証明書を生成します。
  ![dps-cert-gen.exe の実行](./media/how-to-connect-mxchip-iot-devkit/dps-cert-gen.png)

4. X.509 証明書が生成された後、**.pem** 証明書は同じフォルダーに保存されます。

## <a name="create-a-device-enrollment-entry-in-the-device-provisioning-service"></a>デバイス プロビジョニング サービスでのデバイス登録エントリの作成

1. Azure Portal でデバイス プロビジョニング サービス インスタンスに移動します。 **[登録を管理します]** を選び、**[個々の登録]** タブを選びます。![個々の登録](./media/how-to-connect-mxchip-iot-devkit/individual-enrollments.png)

2. **[追加]** を選択します。

3. [Add enrollment]\(登録の追加\) パネルで:
   - **[メカニズム]** で **[X.509]** を選択します
   - **[Primary certificate .pem or .cer file]\(プライマリ証明書 .pem または .cer ファイル\)** の [ファイルの選択] をクリックします
   - [ファイルを開く] ダイアログで、生成した **.pem** 証明書に移動し、アップロードします
   - 残りを既定値のままにし、**[保存]** をクリックします

   ![証明書のアップロード](./media/how-to-connect-mxchip-iot-devkit/upload-cert.png)

  > [!NOTE]
  > このメッセージでエラーが発生する場合:
  >
  > `{"message":"BadRequest:{\r\n \"errorCode\": 400004,\r\n \"trackingId\": \"1b82d826-ccb4-4e54-91d3-0b25daee8974\",\r\n \"message\": \"The certificate is not a valid base64 string value\",\r\n \"timestampUtc\": \"2018-05-09T13:52:42.7122256Z\"\r\n}"}`
  >
  > 証明書ファイル **.pem** をテキストとして開き (メモ帳または任意のテキスト エディターで開く)、次の行を削除します。
  >
  > `"-----BEGIN CERTIFICATE-----"` および `"-----END CERTIFICATE-----"`。
  >


## <a name="start-the-devkit"></a>DevKit を起動する

1. VS Code を開き、シリアル モニターを開きます。

2. DevKit の**リセット** ボタンを押します。

DevKit がデバイス プロビジョニング サービスへの登録を開始したことがわかります。

![VS Code の出力](./media/how-to-connect-mxchip-iot-devkit/vscode-output.png)

## <a name="verify-that-the-devkit-is-registered-with-azure-iot-hub"></a>DevKit が Azure IoT Hub に登録されたことを確認する

デバイスが起動すると、次のアクションが実行されます。

1. デバイスがデバイス プロビジョニング サービスに登録要求を送信します。
2. デバイス プロビジョニング サービスが登録チャレンジを送信し、デバイスがこれに応答します。
3. 登録が成功すると、デバイス プロビジョニング サービスは、IoT Hub の URI、デバイス ID、暗号化されたキーをデバイスに送信します。
4. デバイス上の IoT Hub クライアント アプリケーションがハブに接続します。
5. ハブに正常に接続されると、IoT Hub の Device Explorer にデバイスが表示されます。
  ![登録されたデバイス](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="change-the-device-id"></a>デバイスの ID を変更する

Azure IoT Hub に登録されている既定のデバイス ID は *AZ3166* です。 ID を変更する場合は、「[Customize device ID](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/)」(デバイス ID をカスタマイズする) の説明に従ってください。

## <a name="problems-and-feedback"></a>問題とフィードバック

問題が発生した場合は、IoT Hub の [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) を参照するか、以下のチャネルからサポートにお問い合わせください。

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、デバイスが Azure IoT Hub に自動的に登録できるように、Device Identity Composition Engine を使ってデバイス プロビジョニング サービスに安全にデバイスを登録する方法を学習しました。 

まとめると、次の方法を学習しました。

> [!div class="checklist"]
> * デバイスにデバイス プロビジョニング サービスのグローバル エンドポイントを構成する。
> * 一意デバイス シークレットを使って X.509 証明書を生成する。
> * 個々のデバイスを登録する。
> * デバイスが登録されていることを確認する。

[シミュレートされたデバイスを作成してプロビジョニングする](./quick-create-simulated-device.md)方法を学習してください。

