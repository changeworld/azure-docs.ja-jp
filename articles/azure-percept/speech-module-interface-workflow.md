---
title: Azure Percept 音声モジュールのインターフェイス ワークフロー
description: Azure Percept 音声モジュールのワークフローと使用可能なメソッドについて説明します
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 7/19/2021
ms.custom: template-concept
ms.openlocfilehash: 8dec6a6c4162225b4f5e9ddf3ce74b739fd39562
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222817"
---
# <a name="azure-percept-speech-module-interface-workflow"></a>Azure Percept 音声モジュールのインターフェイス ワークフロー

この記事では、Azure Percept 音声モジュールが IoT Hub と対話する方法について説明します。 これは、モジュール ツインとモジュール メソッドを使用して行われます。 さらに、音声モジュールの呼び出しに使用されるダイレクト メソッド呼び出しの一覧を示します。

## <a name="speech-module-interaction-with-iot-hub-via-module-twin-and-module-method"></a>モジュール ツインとモジュール メソッドを使用した音声モジュールと IoT Hub の対話
- IoT Hub によりモジュール ツインを使用して音声モジュールの設定がデプロイされ、設定はプロパティに保存されます。 音声モジュールでは、モジュール ツインによって報告されるプロパティにより、デバイス情報とテレメトリを IoT Hub に更新できます。
- IoT Hub は、モジュール メソッドを使用して、音声モジュールに制御要求を送信できます。
- IoT Hub では、モジュール メソッドを使用して音声モジュールの状態を取得できます。

詳細については、「[IoT Hub のモジュール ツインの理解と使用](../iot-hub/iot-hub-devguide-module-twins.md)」を参照してください。


## <a name="speech-module-states"></a>音声モジュールの状態
- **IoTInitialized**: IoT モジュールは初期化され、音声モジュールとエッジ ハブ モジュール間のネットワークが接続されていることを示します。
- **Authenticating**: Azure オーディオ デバイスの認証が処理されています。
- **Authenticated**: Azure オーディオ デバイスの認証は終了しました。 失敗した場合、IoT Hub はエラー メッセージを受け取ります。
- **MicDiscovering**: ALSA インターフェイスを使用してマイク配列の列挙を開始します。
- **MicDiscovered**: マイク配列の列挙が完了しました。 失敗した場合、IoT Hub はエラー メッセージを受け取ります。
- **SpeechConfigured**: CC の構成が完了しました。 失敗した場合、IoT Hub はエラー メッセージを受け取ります。
- **SpeechStarted**: ボットが構成され、実行されていることを示します。
- **SpeechStopped**: ボットが停止されていることを示します。
- **DeviceRemoved**: Azure オーディオ デバイスが削除されたことを示します。


## <a name="speech-bot-states"></a>音声ボットの状態
音声ボットの状態の照会は、**SpeechStarted** の音声モジュール状態でのみサポートされます。
- **Ready**: KWS は準備が完了し、音声によるアクティブ化を待機しています。
- **Listening**: ボットは音声入力をリッスンしています。
- **Thinking**: ボットは応答を待機しています。
- **Speaking**: ボットは応答を取得し、応答を読み上げています。

## <a name="interaction-between-iot-hub-and-the-speech-module"></a>IoT Hub と音声モジュールの間の対話 
このセクションでは、IoT Hub と音声モジュールの対話方法について説明します。 図に示すように、3 種類のメッセージがあります。
- 必要なプロパティでのデプロイと、報告されたプロパティでの更新
- モジュール メソッドの呼び出し
- テレメトリを更新する

:::image type="content" source="media/speech-module-interface-workflow/speech-module-diagram.png" alt-text="IoT Hub と音声モジュールの間の対話を示す図":::

IoT Hub により、2 つのパラメーターを使用してモジュール メソッドが呼び出されます。
- モジュール メソッドの名前 (大文字と小文字が区別されます)
- メソッドのペイロード

音声モジュールは以下で応答します。
- 状態コード
    - **0** = アイドル
    - **102** = 処理中
    - **200** = 成功
    - **202** = 保留中
    - **500** = 失敗
    - **501** = 存在しない
- 状態ペイロード

モジュール メソッド GetModuleState の使用例を次に示します。
1. 次のパラメーターを使用してメソッドを呼び出します。
    - 文字列: "GetModuleState"
    - 指定されていません。
1. 応答:
    - 状態コード:200
    - ペイロード: "DeviceRemoved"

## <a name="next-steps"></a>次のステップ
[Azure IoT Hub を使用して音声アシスタント アプリケーションを構成する](./how-to-configure-voice-assistant.md)ときに、これらの概念を適用してみてください。