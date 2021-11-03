---
title: Azure Percept Audio と音声モジュールのトラブルシューティング
description: Azure Percept Audio と azureearspeechclientmodule のトラブルシューティングのヒントを得る
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 3cd92c3158aec8dd63d58455641d4d42d02ff6e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131059815"
---
# <a name="troubleshoot-azure-percept-audio-and-speech-module"></a>Azure Percept Audio と音声モジュールのトラブルシューティング

音声アシスタント アプリケーションの問題をトラブルシューティングするには、以下のガイドラインを使用してください。

## <a name="checking-runtime-status-of-the-speech-module"></a>音声モジュールのランタイムの状態を確認する

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** になっているかどうかを確認します。 デバイス モジュールのランタイムの状態を特定するには、[Azure portal](https://portal.azure.com/) を開いて **[すべてのリソース]**  ->  **[お使いの IoT ハブ]**  ->  **[IoT Edge]**  ->  **[お使いのデバイス ID]** に移動します。 **[モジュール]** タブを選択すると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure portal のエッジ デバイス ページ。":::

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** と表示されていない場合は、 **[モジュールの設定]**  ->  **[azureearspeechclientmodule]** を選択します。 **[モジュールの設定]** ページの **[必要な状態]** を **[実行中]** に設定し、 **[更新]** を選択します。

## <a name="voice-assistant-application-doesnt-load"></a>音声アシスタント アプリケーションが読み込まれない
[いずれかの音声アシスタント テンプレートをデプロイ](./tutorial-no-code-speech.md)してみてください。 テンプレートをデプロイすると、音声アシスタント アプリケーションに必要なすべてのサポート リソースが確実に作成されます。

## <a name="voice-assistant-template-doesnt-get-created"></a>音声アシスタント テンプレートが作成されない
音声アシスタント テンプレートの作成時にエラーが発生する場合は、通常、サポート リソースの 1 つに問題があります。
1. [以前に作成したすべての音声アシスタント リソースを削除します](./delete-voice-assistant-application.md)。
1. 新しい[音声アシスタント テンプレート](./tutorial-no-code-speech.md)をデプロイします。

## <a name="voice-assistant-was-created-but-doesnt-respond-to-commands"></a>音声アシスタントは作成されたが、コマンドに応答しない
この問題をトラブルシューティングするには、[LED の動作とトラブルシューティング ガイド](audio-button-led-behavior.md)に関するページの指示に従ってください。

## <a name="voice-assistant-doesnt-respond-to-custom-keywords-created-in-speech-studio"></a>Speech Studio で作成したカスタム キーワードに音声アシスタントが応答しない
音声モジュールが古いと、この症状が発生することがあります。 次の手順に従って、音声モジュールを最新バージョンに更新してください。

1. Azure Percept Studio ホームページの左側のメニュー パネルにある **[Devices]\(デバイス\)** を選択します。
1. デバイスを探して選択します。

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio のデバイス一覧のスクリーンショット。":::
1. デバイス ウィンドウで **[Speech]\(音声\)** タブを選択します。
1. 音声モジュールのバージョンを確認します。 更新プログラムが利用可能な場合は、バージョン番号の横に **[Update]\(更新\)** ボタンが表示されます。
1. **[Update]\(更新\)** を選択して、音声モジュールの更新プログラムをデプロイします。 通常、更新プロセスは、完了までに 2 分から 3 分かかります。

## <a name="collecting-speech-module-logs"></a>音声モジュールのログを収集する
これらのコマンドを実行するには、 [SSH で開発キットに接続](./how-to-ssh-into-percept-dk.md)し、コマンドを SSH クライアント プロンプトに入力します。

音声モジュールのログを収集します。

```console
sudo iotedge logs azureearspeechclientmodule
```

詳しく分析するために、出力を .txt ファイルにリダイレクトするには、次の構文を使用します。

```console
sudo [command] > [file name].txt
```

.txt ファイルのアクセス許可を変更して、コピーできるようにします。

```console
sudo chmod 666 [file name].txt
```

出力を .txt ファイルにリダイレクトしたら、SCP を使用してファイルをホスト PC にコピーします。

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

[local host file path] は、.txt ファイルのコピー先となるホスト PC 上の場所です。 <remote username> は、[セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に選択した SSH ユーザー名です。

## <a name="known-issues"></a>既知の問題
- 無料試用版を使用している場合は、音声モデルが無料試用版の価格プランを超える可能性があります。 この場合、モデルはエラー メッセージなしで動作を停止します。
- 5 台を超える IoT Edge デバイスが接続されている場合、レポート (テレメトリ経由で IoT Hub および Speech Studio に送信されるテキスト) がブロックされる可能性があります。
- デバイスがリソースとは異なるリージョンにある場合、レポート メッセージが遅延することがあります。 

## <a name="useful-links"></a>便利なリンク
- [Azure Percept Audio の設定](./quickstart-percept-audio-setup.md)
- [Azure Percept Audio のボタンと LED の動作](./audio-button-led-behavior.md)
- [Azure Percept DK と Azure Percept Audio を使用して音声アシスタントを作成する](./tutorial-no-code-speech.md)
- [Azure Percept DK の一般的なトラブルシューティング ガイド](./troubleshoot-dev-kit.md)
- [以前に作成した音声アシスタント アプリケーションに戻る方法](return-to-voice-assistant-application-window.md)
