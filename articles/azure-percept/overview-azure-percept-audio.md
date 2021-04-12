---
title: Azure Percept Audio デバイスの概要
description: Azure Percept Audio について説明します。
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 23fae249cfceec75af0b7c49a3875ab447ecbafd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564264"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept Audio の概要

Azure Percept Audio は、音声 AI 機能を [Azure Percept DK](./overview-azure-percept-dk.md) に追加するアクセサリ デバイスです。 これには、事前に構成された音声プロセッサと 4 つのマイクの線形配列が含まれています。これにより、Azure Cognitive Services の支援を受けて、音声コマンド、キーワード認識、および遠距離音声を使用できます。 Azure Percept DK、[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)、およびその他の Azure エッジ管理サービスに追加設定なしで統合されています。 Azure Percept Audio は、[Microsoft オンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で購入できます。

> [!div class="nextstepaction"]
> [今すぐ購入](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="azure-percept-audio-components"></a>Azure Percept Audio コンポーネント

Azure Percept Audio には、次の主要なコンポーネントが含まれています。

- 4 つのマイクの線形配列と、XMOS コーデックを介して実行されるオーディオ処理を行う実稼働可能な Azure Percept Audio デバイス (SoM)
- 開発者 (インターポーザー) ボード: 2 個のボタン、3 個の LED、マイクロ USB、および 3.5 mm オーディオ ジャック
- 必要なケーブル: FPC ケーブル、USB Micro Type-B to USB-A
- ウェルカム カード
- 統合 80/20 1010 シリーズ マウントによる機械式マウント プレート

## <a name="compute-capabilities"></a>コンピューティング機能 

Azure Percept Audio は、ハイブリッド エッジ クラウド方式で、Azure Percept DK キャリア ボードの CPU で実行される音声スタックを介してオーディオ入力を渡します。 したがって、Azure Percept Audio が動作するには、音声スタックをサポートする OS を備えたキャリア ボードが必要です。 

このオーディオ処理は次のように実行されます。 

- Azure Percept Audio: 音声をキャプチャして変換し、DK およびオーディオ ジャックに送信します。

- Azure Percept DK: 音声スタックがビーム フォーミングとエコー キャンセルを実行します。また、受信オーディオを処理して音声用に最適化します。 処理が完了すると、キーワー認識を実行します。

- クラウド: 自然言語のコマンドと語句、キーワードの検証、再トレーニングを処理します。 

- Offline: デバイスがオフラインの場合は、キーワードを検出し、インターネット接続ステータスのテレメトリをキャプチャします。 クラウドでのキーワードの検証を実行できない場合は、キーワード スポッティングで誤許可率が高くなる可能性があります。 

## <a name="getting-started"></a>作業の開始

- [Azure Percept DK を組み立てる](./quickstart-percept-dk-unboxing.md)
- [Azure Percept Audio デバイスを devkit に接続する](./quickstart-percept-audio-setup.md)
- [Azure Percept DK セットアップ エクスペリエンスを完了する](./quickstart-percept-dk-set-up.md)

## <a name="build-a-no-code-prototype"></a>コードなしのプロトタイプをビルドする

Azure Percept の音声アシスタント テンプレートを使用して、接客、医療、在庫、自動車のシナリオに対して、[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) で[コードなしの音声ソリューション](./tutorial-no-code-speech.md)を構築します。

### <a name="manage-your-no-code-speech-solution"></a>コードなしの音声ソリューションを管理する

- [Azure Percept Studio 内で音声アシスタントを構成する](./how-to-manage-voice-assistant.md)
- [IoT Hub で音声アシスタントを構成する](./how-to-configure-voice-assistant.md)
- [Azure Percept Audio のトラブルシューティング](./troubleshoot-audio-accessory-speech-module.md)

## <a name="additional-technical-information"></a>その他の技術情報

- [Azure Percept Audio のデータシート](./azure-percept-audio-datasheet.md)
- [ボタンと LED の動作](./audio-button-led-behavior.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Microsoft のオンライン ストアで Azure Percept Audio デバイスを購入する](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
