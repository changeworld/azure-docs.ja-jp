---
title: .NET を使用してデバイスから Azure IoT Hub にファイルをアップロードする | Microsoft Docs
description: .NET 用 Azure IoT device SDK を使用して、デバイスからクラウドにファイルをアップロードする方法。 アップロードしたファイルは Azure Storage Blob コンテナーに格納されます。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: df460ba4163b414ad954dce73125a6376f4cad6f
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112004585"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub を使用してデバイスからクラウドにファイルをアップロードする (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

このチュートリアルでは、.NET ファイル アップロード サンプルを使用して、IoT Hub のファイル アップロード機能を使用する方法について説明します。 

[デバイスから IoT ハブにテレメトリを送信する方法](quickstart-send-telemetry-dotnet.md)のクイックスタートと [IoT Hub で cloud-to-device メッセージを送信する方法](iot-hub-csharp-csharp-c2d.md)のチュートリアルには、IoT Hub のデバイスからクラウドへのメッセージングと cloud-to-device メッセージの基本的な機能が示されています。 [IoT Hub を使用したメッセージ ルーティングの構成](tutorial-routing.md)に関するチュートリアルでは、デバイスからクラウドへのメッセージを Microsoft Azure Blob ストレージに確実に格納する方法について説明しています。 ただし、一部のシナリオでは、デバイスが送信するデータを、IoT Hub が受け付けるデバイスからクラウドへの比較的小さなメッセージには簡単にマップできません。 次に例を示します。

* イメージを含む大きなファイル

* ビデオ

* 高頻度でサンプリングされる振動データ

* 何らかの形式の前処理済みデータ

これらのファイルは通常、[Azure Data Factory](../data-factory/introduction.md) や [Hadoop](../hdinsight/index.yml) スタックなどのツールを使用してクラウドでバッチ処理されます。 ただし、デバイスからファイルをアップロードする必要がある場合は、IoT Hub のセキュリティと信頼性を利用できます。 このチュートリアルでは、その方法を説明します。

> [!NOTE]
> IoT Hub は、Azure IoT device SDK を通して、多数のデバイス プラットフォームと言語 (C、Java、Python、JavaScript を含む) をサポートしています。 Azure IoT Hub にデバイスを接続するための詳しい手順については、[Azure IoT デベロッパー センター](https://azure.microsoft.com/develop/iot)を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>前提条件

* Visual Studio Code

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 アカウントがない場合は、 [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成することができます。

* [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) から Azure IoT C# サンプルをダウンロードし、ZIP アーカイブを展開します。

* Visual Studio Code で *FileUploadSample* フォルダーを開き、*FileUploadSample.cs* ファイルを開きます。

* ポート 8883 がファイアウォールで開放されていることを確認してください。 この記事のサンプルでは、ポート 8883 を介して通信する MQTT プロトコルを使用しています。 このポートは、企業や教育用のネットワーク環境によってはブロックされている場合があります。 この問題の詳細と対処方法については、「[IoT Hub への接続 (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)」を参照してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT ハブ接続文字列を取得する

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="examine-the-application"></a>アプリケーションを調べる

.NET サンプル ダウンロードの *FileUploadSample* フォルダーに移動します。 Visual Studio Code でフォルダーを開きます。 フォルダーには *parameters.cs* という名前のファイルが含まれています。 そのファイルを開くと、パラメーター *p* は必須であり、接続文字列が含まれていることがわかります。 パラメーター *t* は、トランスポート プロトコルを変更したい場合に指定できます。 既定のプロトコルは mqtt です。 *program.cs* ファイルには *main* 関数が含まれています。 *FileUploadSample.cs* ファイルにはプライマリ サンプル ロジックが含まれています。 *TestPayload.txt* は、BLOB コンテナーにアップロードするファイルです。

## <a name="run-the-application"></a>アプリケーションの実行

これで、アプリケーションを実行する準備が整いました。

1. Visual Studio Code でターミナル ウィンドウを開きます。
1. 次のコマンドを入力します。
    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your connection string}"
    ```

出力は次のようになります。

```cmd/sh
  Uploading file TestPayload.txt
  Getting SAS URI from IoT Hub to use when uploading the file...
  Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
  Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
  Successfully uploaded the file to Azure Storage
  Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
  Time to upload file: 00:00:01.5077954.
  Done.
```

## <a name="verify-the-file-upload"></a>ファイルのアップロードを確認する

次の手順を実行して *TestPayload.txt* がコンテナーにアップロードされたことを確認します。

1. ストレージ アカウントの左ペインで、 **[データ ストレージ]** の下の **[コンテナー]** を選択します。
1. *TestPayload.txt* をアップロードしたコンテナーを選択します。
1. デバイスにちなんだ名前のフォルダーを選択します。
1. *TestPayload.txt* を選択します。
1. ファイルをダウンロードして、その内容をローカルで表示します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、IoT Hub のファイル アップロード機能を使用して、デバイスからのファイルのアップロードを簡素化する方法を学習しました。 IoT Hub の機能やシナリオを次の記事で引き続き調べることができます。

* [プログラムによる IoT Hub の作成](iot-hub-rm-template-powershell.md)

* [C SDK の概要](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/quickstart-linux.md)