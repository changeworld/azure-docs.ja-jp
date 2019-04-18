---
title: Azure IoT Central に対するデバイスの接続文字列を生成する | Microsoft Docs
description: デバイスの開発者は、IoT Central アプリケーションに接続する必要のあるデバイスのための接続文字列を生成する方法を知る必要があります。
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3a5e8d15d9a705892fe54c50e9b79e6d42af78d9
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426663"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>Azure IoT Central アプリケーションに接続するためのデバイスの接続文字列を生成する

この記事では、デバイスの開発者が、IoT Central アプリケーションに接続する必要のあるデバイスのための接続文字列を生成する方法を説明します。 この記事で説明する手順では、Shared Access Signature (SAS) を使用して 1 台のデバイスを簡単に接続する方法を示します。 このアプローチは、IoT Central を使用した実験やデバイスのテストを行う場合に便利です。 運用環境で使用する代わりの方法については、「[Azure IoT Central のデバイス接続機能](concepts-connectivity.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

1. Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
1. [Node.js](https://nodejs.org/) バージョン 8.0.0 以降がインストールされた開発用コンピューター。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。

## <a name="get-connection-information"></a>接続情報の取得

次の手順では、デバイスの SAS 接続文字列を生成するために必要な情報を入手する方法について説明します。

1. **エクスプローラー**で、アプリケーションに接続する実際のデバイスを探します。

    ![実際のデバイスを選択する](media/howto-generate-connection-string/real-devices.png)

1. **[デバイス]** ページで **[接続]** を選択します。

    ![[接続] を選択する](media/howto-generate-connection-string/connect.png)

1. 以下の手順で使用する接続の詳細 (**スコープ ID**、**デバイス ID**、**デバイスの プライマリ キー**) を記録しておきます。

    ![接続の詳細](media/howto-generate-connection-string/device-connect.png)

    このページから値をコピーして保存できます。

## <a name="generate-the-connection-string"></a>接続文字列を生成する

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>次の手順

ここでは、実デバイスを Azure IoT Central アプリケーションに接続するための接続文字列を生成しました。推奨される次の手順は以下のとおりです。

* [DevKit デバイスを準備して接続する (C)](howto-connect-devkit.md)
* [Raspberry Pi (Python) を準備して接続する](howto-connect-raspberry-pi-python.md)
* [Raspberry Pi (C#) を準備して接続する](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT Core デバイスを準備して接続する (C#)](howto-connect-windowsiotcore.md)
* [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)