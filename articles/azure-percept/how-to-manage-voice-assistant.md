---
title: Azure Percept 音声アシスタント アプリケーションを管理する
description: Azure Percept Studio 内で音声アシスタント アプリケーションを構成する
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7e3ab60b3f89db21c9dbc6290733b2cab365dad9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006356"
---
# <a name="manage-your-azure-percept-voice-assistant-application"></a>Azure Percept 音声アシスタント アプリケーションを管理する

この記事では、音声アシスタント アプリケーションのキーワードとコマンドを [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 内で構成する方法について説明します。 ポータルではなく IoT Hub 内でキーワードを構成する方法については、こちらの[ハウツー記事](./how-to-configure-voice-assistant.md)を参照してください。

音声アシスタント アプリケーションをまだ作成していない場合は、[Azure Percept Studio と Azure Percept Audio を使用し、コーディングなしで音声アシスタントを作成する方法](./tutorial-no-code-speech.md)に関するページを参照してください。

## <a name="keyword-configuration"></a>キーワードの構成

キーワードは、音声アシスタントをアクティブ化するために使用される単語または短いフレーズです。 たとえば、"コルタナさん" は、Cortana アシスタントのキーワードです。 音声によるアクティブ化を使用すると、ユーザーがキーワードを話すことで、ハンドフリーで製品の操作を開始することができます。 製品ではキーワードが継続的にリッスンされているため、ユーザー データを可能な限りプライベートに保つために、すべての音声は検出が行われるまで、デバイスでローカルに処理されます。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>音声アシスタントのデモ ウィンドウ内の構成

1. デモ ページの **[カスタム キーワード]** の横にある **[変更]** をクリックします。

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Hospitality (接客) デモ ウィンドウのスクリーンショット。":::

    デモ ページを開いていない場合は、デバイス ページ (下記参照) に移動し、 **[アクション]** の **[音声アシスタントのテスト]** を選択してデモにアクセスします。

1. 使用可能ないずれかのキーワードを選択し、 **[保存]** を選択して変更を適用します。

1. 構成が完了して音声アシスタントが使用できる状態になると、Azure Percept Audio デバイスにある 3 つの LED ライトが明るい青色 (点滅なし) に変化します。

### <a name="configuration-within-the-device-page"></a>デバイス ページ内の構成

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) の概要ページで、左側のメニュー ペインにある **[デバイス]** を選択します。

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="デバイスが強調表示された Azure Percept Studio の概要ページのスクリーンショット。":::

1. 音声アシスタント アプリケーションのデプロイ先となったデバイスを選択します。

1. **[Speech]\(音声\)** タブを開きます。

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="[音声] タブが強調表示されたエッジ デバイス ページのスクリーンショット。":::

1. **[キーワード]** の横にある **[変更]** を選択します。

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="使用可能な音声ソリューションのアクションのスクリーンショット。":::

1. 使用可能ないずれかのキーワードを選択し、 **[保存]** を選択して変更を適用します。

1. 構成が完了して音声アシスタントが使用できる状態になると、Azure Percept Audio デバイスにある 3 つの LED ライトが明るい青色 (点滅なし) に変化します。

## <a name="create-a-custom-keyword"></a>カスタム キーワードを作成する

音声アシスタントのカスタム キーワードは、[Speech Studio](https://speech.microsoft.com/) で作成できます。 基本的なカスタム キーワード モデルのトレーニングには最大 30 分かかります。

カスタム キーワードの作成に関するガイダンスについては、[Speech Studio のドキュメント](../cognitive-services/speech-service/custom-keyword-basics.md)に従ってください。 構成が完了すると、音声アシスタント アプリケーションで使用するための新しいキーワードが Project Santa Cruz ポータルで利用できるようになります。

## <a name="commands-configuration"></a>コマンドの構成

カスタム コマンドを使用すると、音声優先の対話操作を行うために最適化された、リッチな音声コマンド処理アプリを簡単に構築できます。 カスタム コマンドは、タスクの完了やコマンドと制御のシナリオに最適です。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>音声アシスタントのデモ ウィンドウ内の構成

1. デモ ページの **[カスタム コマンド]** の横にある **[変更]** を選択します。 デモ ページを開いていない場合は、デバイス ページ (下記参照) に移動し、 **[アクション]** の **[音声アシスタントのテスト]** を選択してデモにアクセスします。

1. 使用可能ないずれかのカスタム コマンドを選択し、 **[保存]** を選択して変更を適用します。

### <a name="configuration-within-the-device-page"></a>デバイス ページ内の構成

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) の概要ページで、左側のメニュー ペインにある **[デバイス]** を選択します。

1. 音声アシスタント アプリケーションのデプロイ先となったデバイスを選択します。

1. **[Speech]\(音声\)** タブを開きます。

1. **[コマンド]** の横にある **[変更]** を選択します。

1. 使用可能ないずれかのカスタム コマンドを選択し、 **[保存]** を選択して変更を適用します。

## <a name="create-custom-commands"></a>カスタム コマンドを作成する

音声アシスタントを実行するためのカスタム コマンドは、[Speech Studio](https://speech.microsoft.com/) で作成できます。

カスタム コマンドの作成に関するガイダンスについては、[Speech Studio のドキュメント](../cognitive-services/speech-service/quickstart-custom-commands-application.md)に従ってください。 構成が完了すると、音声アシスタントアプリケーション用の新しいコマンドが Azure Percept Studio で使用できるようになります。

## <a name="next-steps"></a>次のステップ

音声アシスタント アプリケーションの作成後は、Azure Percept DK を使用し、[コーディングなしでビジョン ソリューション](./tutorial-nocode-vision.md)を開発してみましょう。
