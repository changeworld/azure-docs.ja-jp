---
title: Azure IoT Central エクスプローラーを使用してデバイスの接続を監視する
description: IoT Central エクスプローラー CLI を使用して、デバイスのメッセージを監視し、デバイス ツインの変更を観察します。
author: viv-liu
ms.author: viviali
ms.date: 12/18/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 90cf83f86acb647b8194619bc1b572e5147cc0cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434949"
---
# <a name="monitor-device-connectivity-using-azure-cli-preview-features"></a>Azure CLI を使用してデバイスの接続性を監視する (プレビュー機能)

*このトピックはビルダーおよび管理者向けです。*

Azure CLI IoT 拡張機能を使用して、デバイスから IoT Central に送信されるメッセージを確認し、デバイス ツインでの変更を観察します。 このツールを使用し、デバイスの接続状態をデバッグし、観察し、クラウドに達しないデバイス メッセージの問題や、ツイン変更に応答しないデバイスの問題を診断できます。

[詳細については、Azure CLI 拡張機能のリファレンスを参照してください。](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/central)

## <a name="prerequisites"></a>前提条件

+ Azure CLI がインストールされており、バージョンが 2.0.7 以降であること。 `az --version` を実行し、Azure CLI のバージョンを確認してください。 インストールと更新の方法は [Azure CLI ドキュメント](https://docs.microsoft.com/cli/azure/install-azure-cli)でご確認いただけます。
+ Azure の職場または学校アカウント。IoT Central アプリケーションにユーザーとして追加されます。

## <a name="install-the-iot-central-extension"></a>IoT Central 拡張機能をインストールする

インストールするには、コマンド ラインから次のコマンドを実行します。

```cmd/sh
az extension add --name azure-cli-iot-ext
```

次を実行して拡張機能のバージョンを確認します。 
```cmd/sh
az --version
```
azure-cli-iot-ext 拡張機能が 0.8.1 以上になっているはずです。 そうでない場合は次を実行します。
```cmd/sh
az extension update --name azure-cli-iot-ext
```

## <a name="using-the-extension"></a>拡張機能を使用する

次のセクションでは、`az iot central` を実行するときに使用できる一般的なコマンドとオプションについて説明します。 コマンドおよびオプションの完全なセットを表示するには、`--help` を `az iot central` またはそのいずれかのサブコマンドに渡します。

### <a name="login"></a>ログイン

まず、Azure CLI にサインインします。 

```cmd/sh
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>IoT Central アプリのアプリケーション ID を取得する
**[Administration/Application Settings]\(管理/アプリケーション設定\)** で**アプリケーション ID** をコピーします。 これは後の手順で使用します。

### <a name="monitor-messages"></a>メッセージの監視
デバイスから IoT Central アプリに送信されているメッセージを監視します。 これにはすべてのヘッダーと注釈が含まれます。

```cmd/sh
az iot central app monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>デバイスのプロパティを表示する
特定のデバイスを対象に、現在の読み取りおよび読み取り/書き込みのデバイス プロパティを表示します。

```cmd/sh
az iot central device-twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>次のステップ

これで IoT Central エクスプローラーの使用方法についての学習が終わりました。推奨される次の手順は、[IoT Central でのデバイスの管理](howto-manage-devices.md)について確認することです。
