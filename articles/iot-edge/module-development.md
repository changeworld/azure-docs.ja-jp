---
title: Azure IoT Edge のモジュールを開発する | Microsoft Docs
description: Azure IoT Edge のカスタム モジュールの作成方法について説明します
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dbbd07e93602855afb0c9755e8872e0b46557611
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030021"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>IoT Edge モジュールを開発するための要件とツールについて理解する

この記事では、IoT Edge モジュールとして実行されるアプリケーションを記述する際に使用できる機能と、それらの活用方法について説明します。

## <a name="iot-edge-runtime-environment"></a>IoT Edge ランタイム環境
IoT Edge ランタイムでは、複数のIoT Edge モジュールの機能を統合し、IoT Edge デバイス上に展開するためのインフラストラクチャが提供されます。 簡単に言えば、ユーザーは任意のプログラムを IoT Edge モジュールとしてパッケージ化することができます。 ただし、IoT Edge の通信機能や管理機能を活用するには、モジュールで実行されるプログラムが、IoT Edge ランタイムに統合されたローカル IoT Edge ハブに接続できる必要があります。

## <a name="using-the-iot-edge-hub"></a>IoT Edge ハブの使用
IoT Edge ハブでは、2 つの主要機能が提供されます。 IoT Hub へのプロキシと、ローカル通信です。

### <a name="iot-hub-primitives"></a>IoT Hub プリミティブ
IoT Hub では、次のような意味で、モジュール インスタンスがデバイスのようなものとして見なされます。

* [デバイス ツイン][lnk-devicetwin]、およびそのデバイスの他のモジュール ツインとは区別されて分離された、モジュール ツインがある。
* [デバイスからクラウドへのメッセージ][lnk-iothub-messaging]を送信できる。
* その ID で明確に対象とされた[ダイレクト メソッド][lnk-methods]を受け取ることができる。

現在のところ、モジュールでは、クラウドからデバイスへのメッセージを受信したり、ファイルのアップロード機能を使用することはできません。

モジュールを作成する際には、[Azure IoT Device SDK][lnk-devicesdk] を使用して IoT Edge ハブに接続し、デバイス アプリケーションで IoT Hub を使用する場合と同様に、上記の機能を使用することができます。違うのは、アプリケーション バックエンドから、デバイス ID ではなくモジュール ID を参照する必要があるという点だけです。

デバイスからクラウドへのメッセージを送信し、モジュール ツインを使用するモジュール アプリケーションの例については、「[Develop and deploy an IoT Edge module to a simulated device (IoT Edge モジュールを開発し、シミュレートされたデバイスにデプロイする)][lnk-tutorial2]」を参照してください。

### <a name="device-to-cloud-messages"></a>デバイスからクラウドへのメッセージ
デバイスからクラウドへのメッセージの複雑な処理を可能にするため、IoT Edge ハブでは、モジュール間、およびモジュールと IoT Hub 間での、メッセージの宣言型ルーティングが提供されます。
これにより、モジュールは他のモジュールによって送信されたメッセージをインターセプトして処理し、それらを複雑なパイプラインに伝達できるようになります。
[モジュール構成][lnk-module-comp]に関する記事では、ルートを使用してモジュールを複雑なパイプラインへと構成する方法について説明しています。

通常の IoT Hub デバイス アプリケーションとは違い、IoT Edge モジュールでは、ローカルの IoT Edge ハブによってプロキシされている、デバイスからクラウドへのメッセージを受信し、それらを処理することができます。

IoT Edge ハブは、[モジュール構成][lnk-module-comp]に関する記事で説明されている宣言型のルートに基づいて、メッセージをモジュールに伝達します。 IoT Edge モジュールを開発する際には、メッセージ ハンドラーを設定することで、これらのメッセージを受信することができます (「[Develop and deploy an IoT Edge module to a simulated device (IoT Edge モジュールを開発し、シミュレートされたデバイスにデプロイする)][lnk-tutorial2]」チュートリアルで説明しています)。

ルートの作成を簡略化するため、IoT Edge では、モジュールの*入力*エンドポイントと*出力*エンドポイントの概念が追加されています。 モジュールは、自身にルーティングされたすべての D2C メッセージ (デバイスからクラウドへのメッセージ) を、入力を指定することなく受信したり、出力を指定することなく、D2C メッセージ送信することができます。
ただし、明示的な入力と出力を使用したほうが、ルーティング ルールがわかりやすくなります。 ルーティング ルールと、モジュールの入力および出力エンドポイントの詳細については、[モジュール構成][lnk-module-comp]に関する記事を参照してください。

最後に、Edge ハブによって処理された D2C メッセージには、次のシステム プロパティを使用してスタンプが付けられます。

| プロパティ | 説明 |
| -------- | ----------- |
| $connectionDeviceId | メッセージを送信したクライアントのデバイス ID |
| $connectionModuleId | メッセージを送信したモジュールのモジュール ID |
| $inputName | このメッセージを受信した入力。 空の場合もあります。 |
| $outputName | メッセージを送信するために使用された出力。 空の場合もあります。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>モジュールから IoT Edge ハブへの接続
モジュールからローカル IoT Edge ハブに接続するには、2 つの手順が必要です。1 つは、 モジュールの起動時に IoT Edge ランタイムによって提供された接続文字列を使用することで、もう 1 つは、そのデバイスの IoT Edge ハブによって提示された証明書がアプリケーションによって受け入れられることを確認することです。

使用する接続文字列は、IoT Edge ランタイムによって環境変数 `EdgeHubConnectionString` に挿入されます。 これにより、その接続文字列を使用する必要があるすべてのプログラムで、その接続文字列が使用可能になります。

同様に、IoT Edge ハブ接続の検証に使用する証明書は、IoT Edge ランタイムによって、環境変数 `EdgeModuleCACertificateFile` にパスがあるファイル内に挿入されます。

「[Develop and deploy an IoT Edge module to a simulated device (IoT Edge モジュールを開発し、シミュレートされたデバイスにデプロイする)][lnk-tutorial2]」チュートリアルでは、証明書がモジュール アプリケーション内のマシン ストアにあるかどうかを確認する方法が説明されています。 もちろん、その証明書を使用して接続を信頼する、その他の方法を使用することもできます。

## <a name="packaging-as-an-image"></a>イメージとしてのパッケージ化
IoT Edge モジュールは、Docker イメージとしてパッケージ化されます。
ユーザーは、Docker ツールチェーンを直接使用することもできますし、「[Develop and deploy an IoT Edge module to a simulated device (IoT Edge モジュールを開発し、シミュレートされたデバイスにデプロイする)][lnk-tutorial2]」チュートリアルに示すように、Visual Studio Code を使用することもできます。

## <a name="next-steps"></a>次の手順

モジュールを開発したら、[IoT Edge モジュールを大規模にデプロイして監視する][lnk-howto-deploy]方法について学習してください。

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
