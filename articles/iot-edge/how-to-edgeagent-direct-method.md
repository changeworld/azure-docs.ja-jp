---
title: 組み込みの edgeAgent ダイレクト メソッド - Azure IoT Edge
description: IoT Edge エージェント ランタイム モジュールの組み込みのダイレクト メソッドを使用して IoT Edge デプロイを監視および管理します
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240343"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>組み込みのダイレクト メソッドを使用して edgeAgent と通信する

IoT Edge エージェント モジュールに含まれているダイレクト メソッドを使用して IoT Edge デプロイを監視および管理します。 ダイレクト メソッドはデバイスに実装され、その後クラウドから呼び出すことができます。 IoT Edge エージェントには、IoT Edge デバイスをリモートで監視および管理できるようにするダイレクト メソッドが含まれています。

ダイレクト メソッドの詳細、使用方法、独自のモジュールに実装する方法については、「[IoT Hub からのダイレクト メソッドの呼び出しについて](../iot-hub/iot-hub-devguide-direct-methods.md)」を参照してください。

これらのダイレクト メソッドの名前は、大文字と小文字を区別して処理されます。

## <a name="ping"></a>ping

**ping** メソッドは、IoT Edge がデバイスで実行されているかどうか、またはデバイスが IoT Hub への接続を開いているかどうかの確認に役立ちます。 このダイレクト メソッドを使用して、IoT Edge エージェントに ping を実行し、その状態を取得します。 ping が成功すると、空のペイロードと **"status":200** が返されます。

次に例を示します。

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Azure portal で、メソッド名 `ping` と空の JSON ペイロード `{}` を指定してメソッドを呼び出します。

![Azure portal でダイレクト メソッド 'ping' を呼び出す](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>モジュールの再起動

**RestartModule** メソッドを使用すると、IoT Edge デバイスで実行されているモジュールをリモート管理できます。 モジュールでエラー状態またはその他の異常な動作が報告されている場合は、IoT Edge エージェントをトリガーしてそのモジュールを再起動することができます。 再起動コマンドが成功すると、空のペイロードと **"status":200** が返されます。

RestartModule メソッドは IoT Edge バージョン 1.0.9 以降で使用できます。 

RestartModule ダイレクト メソッドは、IoT Edge デバイスで実行されているモジュール (edgeAgent モジュール自体を含む) で使用できます。 ただし、このダイレクト メソッドを使用して edgeAgent をシャットダウンすると、モジュールの再起動中に接続が中断されるため、成功の結果は得られません。

次に例を示します。

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

Azure portal で、メソッド名 `RestartModule` と次の JSON ペイロードを指定してメソッドを呼び出します。

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Azure portal でダイレクト メソッド 'RestartModule' を呼び出す](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>実験的なメソッド

テスト用の実験的な機能として、新しいダイレクト メソッドのオプションを利用できます。これには以下が含まれます。

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md):モジュール ログを取得し、Azure Blob Storage にアップロードします。
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus):ログのアップロード要求の状態を確認します。
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs):ダイレクト メソッドの応答でモジュール ログをインラインで取得します。

## <a name="next-steps"></a>次のステップ

[IoT Edge エージェントと IoT Edge ハブのモジュール ツインのプロパティ](module-edgeagent-edgehub.md)
