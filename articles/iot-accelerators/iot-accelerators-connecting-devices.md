---
title: C を使用してリモート監視するために Windows デバイスをプロビジョニングする - Azure | Microsoft Docs
description: Windows で実行されている C で記述されたアプリケーションを使用して、デバイスをリモート監視ソリューション アクセラレータに接続する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: dobett
ms.openlocfilehash: 55c8ff799ba3ff7fe9691d46dc90a00d5182d390
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2018
ms.locfileid: "48829412"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>デバイスをリモート監視ソリューション アクセラレータに接続する (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

このチュートリアルでは、リモート監視ソリューション アクセラレータに物理デバイスを接続する方法について説明します。

制限のあるデバイス上で実行されるほとんどの組み込みアプリケーションと同様、デバイス アプリケーションのためのクライアント コードは C で記述されています。このチュートリアルでは、Windows を実行しているコンピューターでデバイス クライアント アプリケーションを構築します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、[Windows 開発環境の設定](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)に関するページに示されている手順に従って、必要な開発ツールとライブラリを Windows コンピューターに追加します。

## <a name="view-the-code"></a>コードを表示する

このガイドで使用される[サンプル コード](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)は、Azure IoT C SDK GitHub リポジトリで入手できます。

### <a name="download-the-source-code-and-prepare-the-project"></a>ソース コードをダウンロードしてプロジェクトを準備する

プロジェクトを準備するには、GitHub から [Azure IoT C SDK リポジトリ](https://github.com/Azure/azure-iot-sdk-c)を複製またはダウンロードします。

サンプルは、**samples/solutions/remote_monitoring_client** フォルダーにあります。

テキスト エディターで、**samples/solutions/remote_monitoring_client** フォルダー内の **remote_monitoring.c** ファイルを開きます。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

1. **remote_monitoring.c** ファイルを編集し、`<connectionstring>` を、ソリューション アクセラレータにデバイスを追加したときにこのハウツー ガイドの冒頭でメモしたデバイス接続文字列に置き換えます。

1. 「[Build the C SDK in Windows](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows)」 (Windows で C SDK を構築する) の手順に従って、SDK とリモート監視クライアント アプリケーションを構築します。

1. ソリューションを構築するために使用したコマンド プロンプトで、次のコマンドを実行します。

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    コンソールには、次のメッセージが表示されます。

    - アプリケーションは、ソリューション アクセラレータにサンプル テレメトリを送信します。
    - ソリューションのダッシュ ボードから呼び出されたメソッドに応答します。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
