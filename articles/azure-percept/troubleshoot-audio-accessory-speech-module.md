---
title: Azure Percept Audio と音声モジュールに関する問題をトラブルシューティングする
description: オンボーディング エクスペリエンス中に多く見られるいくつかの問題について、トラブルシューティングのヒントを取り上げます
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e2ad920afb55066f07430568f976154f6d7cae1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678660"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio と音声モジュールのトラブルシューティング

音声アシスタント アプリケーションの問題をトラブルシューティングするには、以下のガイドラインを使用してください。

## <a name="collecting-speech-module-logs"></a>音声モジュールのログを収集する

これらのコマンドを実行するには、[Azure Percept DK Wi-Fi アクセス ポイントに接続して SSH で開発キットに接続](./how-to-ssh-into-percept-dk.md)し、SSH ターミナルでコマンドを入力します。

```console
 iotedge logs azureearspeechclientmodule
```

詳しく分析するために、出力を .txt ファイルにリダイレクトするには、次の構文を使用します。

```console
[command] > [file name].txt
```

出力を .txt ファイルにリダイレクトしたら、SCP を使用してファイルをホスト PC にコピーします。

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

<local host file path> は、.txt ファイルのコピー先となるホスト PC 上の場所です。 <remote username> は、[セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に選択した SSH ユーザー名です。 OOBE 中に SSH ログインを設定しなかった場合、リモートユーザー名は root になります。

## <a name="checking-runtime-status-of-the-speech-module"></a>音声モジュールのランタイムの状態を確認する

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** になっているかどうかを確認します。 デバイス モジュールのランタイムの状態を特定するには、[Azure portal](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_Azure_Iothub=aduprod&microsoft_azure_marketplace_ItemHideKey=Microsoft_Azure_ADUHidden#home) を開いて **[すべてのリソース]**  ->  **\<your IoT hub>**  ->  **[IoT Edge]**  ->  **\<your device ID>** に移動します。 **[モジュール]** タブをクリックすると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure portal のエッジ デバイス ページ。":::

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** として表示されていない場合は、 **[モジュールの設定]**  ->  **[azureearspeechclientmodule]** をクリックします。 **[モジュールの設定]** ページの **[必要な状態]** を **[running]\(実行中\)** に設定し、 **[更新]** をクリックします。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/firmware-desired-status-stopped.png" alt-text="Azure portal の [モジュールの設定] 画面。":::

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED インジケーターについて

LED インジケーターを使用して、デバイスの状態を把握できます。 通常、"*電源投入*" 後、モジュールの初期化が完了するまでに約 2 分かかります。 初期化の手順を終えると、次のことが確認できます。

1. 左側の LED 1 つが緑色で点灯 - デバイスの電源がオンです。 
2. 左側の LED 1 つが緑色で点灯し、中央の LED が緑色で点滅 - 認証中です。 
3. デバイスが認証されて使用できる状態になると、3 つの LED がすべて青色に変化します。

|LED の状態                  |Ear SoM 状態            |
|----------------------------|---------------------------|
|1 つ (左側の LED) が緑色         |電源オン |
|1 つ (左側の LED) が緑色 <br> 1 つ (中央の LED) が緑色で点滅 |認証が進行中 |
|3 つともオフ                      |初期化が完了 |
|3 つが青色                     |使用準備完了 |
|3 つが青色で点滅            |キーワード認識 |
|3 つが濃い青色              |処理 |
|3 つが赤色                      |ミュート |

## <a name="next-steps"></a>次のステップ

Azure Percept DK のトラブルシューティングの詳細については、[一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)を参照してください。