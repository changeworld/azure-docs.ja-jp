---
title: Azure Percept Audio と音声モジュールに関する問題のトラブルシューティング
description: Azure Percept Audio と azureearspeechclientmodule のトラブルシューティングのヒントを得る
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c02c93acd23b71f73d5e27d8914d08fb1a1f99f9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110071400"
---
# <a name="azure-percept-audio-and-speech-module-troubleshooting"></a>Azure Percept Audio と音声モジュールのトラブルシューティング

音声アシスタント アプリケーションの問題をトラブルシューティングするには、以下のガイドラインを使用してください。

## <a name="understanding-ear-som-led-indicators"></a>Ear SoM LED インジケーターについて

LED インジケーターを使用して、デバイスの状態を把握できます。 通常、デバイスの電源がオンになり、モジュールの初期化が完了するまでに約 4～5 分かかります。 初期化の手順を終えると、次のことが確認できます。

1. 中央の白色 LED が点灯 (静止): デバイスの電源がオンです。
1. 中央の白色 LED が点灯 (点滅): 認証が進行中です。
1. 中央の白い LED が点灯 (静止): デバイスは認証されていますが、キーワードが構成されていません。
1. デモがデプロイされ、デバイスが使用できる状態になると、3 つの LED がすべて青色に変化します。

詳細については、[Azure Percept Audio のボタンと LED の動作](./audio-button-led-behavior.md)に関する記事を参照してください。

### <a name="troubleshooting-led-issues"></a>LED に関する問題のトラブルシューティング
- **中央の LED が白で点灯している場合** は、[テンプレートを使用して音声アシスタントを作成](./tutorial-no-code-speech.md)してみてください。
- **中央の LED が常に点滅している場合** は、認証の問題を示しています。 こちらのトラブルシューティング手順を試してください。
    - USB-A とマイクロ USB の接続がセキュリティで保護されていることを確認します。 
    - [音声モジュールが実行されている](./troubleshoot-audio-accessory-speech-module.md#checking-runtime-status-of-the-speech-module)かどうかを確認します。
    - デバイスを再起動します
    - [ログを収集](./troubleshoot-audio-accessory-speech-module.md#collecting-speech-module-logs)してサポート リクエストに添付します
    - 開発キットで最新のソフトウェアが実行されているかどうかを確認し、利用可能な場合は更新プログラムを適用します。

## <a name="checking-runtime-status-of-the-speech-module"></a>音声モジュールのランタイムの状態を確認する

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** になっているかどうかを確認します。 デバイス モジュールのランタイムの状態を特定するには、[Azure portal](https://portal.azure.com/) を開いて **[すべてのリソース]**  ->  **[お使いの IoT ハブ]**  ->  **[IoT Edge]**  ->  **[お使いのデバイス ID]** に移動します。 **[モジュール]** タブをクリックすると、インストールされているすべてのモジュールのランタイムの状態が表示されます。

:::image type="content" source="./media/troubleshoot-audio-accessory-speech-module/over-the-air-iot-edge-device-page.png" alt-text="Azure portal のエッジ デバイス ページ。":::

**azureearspeechclientmodule** のランタイムの状態が **[running]\(実行中\)** として表示されていない場合は、 **[モジュールの設定]**  ->  **[azureearspeechclientmodule]** をクリックします。 **[モジュールの設定]** ページの **[必要な状態]** を **[running]\(実行中\)** に設定し、 **[更新]** をクリックします。

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
