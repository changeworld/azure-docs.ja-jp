---
title: Azure IoT Central のデバイス開発ベスト プラクティス | Microsoft Docs
description: この記事では、Azure IoT Central におけるデバイス接続のベスト プラクティスについて説明します。
author: dominicbetts
ms.author: dobett
ms.date: 03/03/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- device-developer
ms.openlocfilehash: a3cbfa17d3b063ddcef90820dc31a080a768cbcd
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493765"
---
# <a name="best-practices-for-device-development"></a>デバイス開発のベスト プラクティス

これらの推奨事項は、IoT Central の組み込みのディザスター リカバリーと自動スケーリングを活用するようにデバイスを実装する方法を示しています。

次の一覧に、デバイスが IoT Central に接続するときのフローの概要を示します。

1. DPS を使用してデバイスをプロビジョニングし、デバイス接続文字列を取得します。

1. 接続文字列を使用して IoT Central の内部 IoT Hub エンドポイントに接続します。 IoT Central アプリケーションとの間でデータを送受信します。

1. デバイスで接続エラーが発生した場合は、エラーの種類に応じて、接続を再試行するか、デバイスを再プロビジョニングします。

## <a name="use-dps-to-provision-the-device"></a>DPS を使用してデバイスをプロビジョニングする

DPS を使用してデバイスをプロビジョニングするには、IoT Central アプリケーションのスコープ ID、資格情報、およびデバイス ID を使用します。 資格情報の種類の詳細については、「[X.509 グループ登録](concepts-get-connected.md#x509-group-enrollment)」と「[SAS グループの登録](concepts-get-connected.md#sas-group-enrollment)」を参照してください。 デバイス ID の詳細については、「[デバイス登録](concepts-get-connected.md#device-registration)」を参照してください。

成功すると、DPS によって、デバイスが IoT Central アプリケーションに接続するために使用できる接続文字列が返されます。 プロビジョニング エラーをトラブルシューティングするには、「[デバイスのプロビジョニングの状態を確認する](troubleshoot-connection.md#check-the-provisioning-status-of-your-device)」を参照してください。

後で接続するために使用する接続文字列をデバイスでキャッシュできます。 ただし、デバイスは、[接続エラーを処理する](#handle-connection-failures)準備ができている必要があります。

## <a name="connect-to-iot-central"></a>IoT Central に接続する

接続文字列を使用して IoT Central の内部 IoT Hub エンドポイントに接続します。 接続によって、テレメトリの IoT Central アプリケーションへの送信、プロパティ値の IoT Central アプリケーションとの同期、および IoT Central アプリケーションから送信されたコマンドへの応答を実行できます。

## <a name="handle-connection-failures"></a>接続エラーを処理する

スケーリングまたはディザスター リカバリーのために、IoT Central によって基になる IoT ハブが更新されることがあります。 接続を維持するために、新しい IoT Hub エンドポイントへの接続を確立することによって、デバイス コードで特定の接続エラーを処理する必要があります。

デバイスの接続時に次のエラーのいずれかが取得された場合、デバイスでは DPS によるプロビジョニング手順を再実行して新しい接続文字列を取得する必要があります。 これらのエラーは、デバイスが使用している接続文字列が有効ではなくなったことを意味します。

- IoT Hub エンドポイントに到達できません。
- セキュリティ トークンの有効期限が切れました。
- IoT Hub でデバイスが無効です。

デバイスの接続時に次のエラーのいずれかが取得された場合は、デバイスではバックオフ戦略を使用して接続を再試行する必要があります。 これらのエラーは、デバイスで使用されている接続文字列は引き続き有効ですが、一時的な状態によってデバイスの接続が停止されていることを意味します。

- オペレーターによってデバイスがブロックされました。
- サービスからの内部エラー 500。

デバイス エラー コードの詳細については、[デバイス接続のトラブルシューティング](troubleshoot-connection.md)に関するページを参照してください。

## <a name="test-failover-capabilities"></a>フェールオーバー機能のテスト

Azure CLI を使用すると、デバイスのクライアント コードのフェールオーバー機能をテストできます。 CLI コマンドは、デバイスの登録を別の内部 IoT ハブに一時的に切り替えることによって機能します。 デバイスのフェールオーバーが正常に機能していることを確認するには、デバイスが引き続きテレメトリを送信し、IoT Central アプリケーションのコマンドに応答していることを確認します。

デバイスのフェールオーバー テストを実行するには、次のコマンドを実行します。

```azurecli
az iot central device manual-failover \
    --app-id {Application ID of your IoT Central application} \
    --device-id {Device ID of the device you're testing} \
    --ttl-minutes {How to wait before moving the device back to it's original IoT hub}
```

> [!TIP]
> **アプリケーション ID** を見つけるには、IoT Central アプリケーションで、 **[管理] > [自分のアプリケーション]** に移動します。

コマンドが成功すると、次のような出力が表示されます。

```output
Command group 'iot central device' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
{
  "hubIdentifier": "6bd4...bafa",
  "message": "Success! This device is now being failed over. You can check your device'’'s status using 'iot central device registration-info' command. The device will revert to its original hub at Tue, 18 May 2021 11:03:45 GMT. You can choose to failback earlier using device-manual-failback command. Learn more: https://aka.ms/iotc-device-test"
}
```

CLI コマンドの詳細については、「[az iot central device manual-failover](/cli/azure/iot/central/device#az_iot_central_device_manual_failover)」を参照してください。

デバイスからのテレメトリが IoT Central アプリケーションに到達している最中かどうかを確認できるようになりました。

さまざまなプログラミング言語でのフェールオーバーを処理するサンプルのデバイスコードについては、「[IoT 高可用性クライアント](https://github.com/iot-for-all/iot-central-high-availability-clients)」を参照してください。

## <a name="next-steps"></a>次のステップ

推奨される次のステップは以下のとおりです。

- 「[チュートリアル:クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](tutorial-connect-device.md)」で、SAS トークンの使い方を示すサンプル コードを確認する
- [IoT Central アプリケーション用の Node.js デバイス SDK を使用して、x.509 証明書を使用するデバイスを接続する](how-to-connect-devices-x509.md)方法を確認する
- [Azure CLI を使用してデバイスの接続性を監視する](./howto-monitor-devices-azure-cli.md)方法を確認する
- [Azure IoT Central アプリケーションで新しい IoT デバイスの種類を定義する](./howto-set-up-template.md)方法を確認する
- [Azure IoT Edge デバイスと Azure IoT Central](./concepts-iot-edge.md) について確認する
