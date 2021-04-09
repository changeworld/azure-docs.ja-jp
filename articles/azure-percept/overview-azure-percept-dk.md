---
title: Azure Percept DK の概要
description: Azure Percept DK の詳細情報
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: c93ecdc138e757f84e995a69a6c3d7f1b5460abe
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179376"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK の概要

Azure Percept DK は、ビジョンおよびオーディオ AI 概念実証のために設計されたエッジ AI および IoT 開発キットです。 [Azure Percept Studio](./overview-azure-percept-studio.md) および [Azure Percept Audio](./overview-azure-percept-audio.md) と組み合わせると、さまざまなビジョンまたはオーディオ AI アプリケーション用のエッジ AI ソリューションを構築するための、非常に使いやすく強力なプラットフォームになります。 [Microsoft オンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で購入できます。

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="Azure Percept DK デバイス。":::

## <a name="key-features"></a>主な機能

- **AI をエッジで実行する機能**。 組み込みのハードウェア アクセラレータを使用すると、クラウドに接続しなくても、ビジョン AI モデルを実行できます。
- **信頼のハードウェア ルート セキュリティを組み込み**。 詳細については、[Azure Percept のセキュリティ](./overview-percept-security.md)の概要を参照してください。
- **[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)** およびその他の Azure サービスとシームレスに統合。 Azure IoT Hub、Azure Cognitive Services、[Live Video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview) など
- **オプションの [Azure Percept Audio](./overview-azure-percept-audio.md) とのシームレスな統合**
- **主要な AI プラットフォームをサポート**。 ONNX や TensorFlow など。
- **80/20 レーリング システムとの統合**。 運用環境でプロトタイプを簡単に構築できます。 [80/20 統合](./overview-8020-integration.md)について詳しくは、こちらをご覧ください。

## <a name="hardware-components"></a>ハードウェア コンポーネント

- Azure Percept DK 通信事業者ボード
    - NXP iMX8m プロセッサ
    - トラステッド プラットフォーム モジュール (TPM) バージョン 2.0
    - WiFi および Bluetooth 接続
    - [データシート](./azure-percept-dk-datasheet.md)全体をご覧ください
- Azure Percept Vision システム オン モジュール (SoM)
    - Intel Movidius Myriad X (MA2085) ビジョン処理ユニット (VPU)
    - 2 つ目を追加する機能を備えた RGB カメラ センサー
    - [データシート](./azure-percept-vision-datasheet.md)全体をご覧ください

## <a name="get-started-with-the-azure-percept-dk"></a>Azure Percept DK の使用を開始する

- クイック スタートを完了する
    - [Azure Percept DK を箱から取り出して組み立てる](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept DK を設定し、最初のビジョン AI モデルを実行する](./quickstart-percept-dk-set-up.md)
- これらのチュートリアルを使用して、概念実証の構築を開始する
    - [Azure Percept Studio でコードなしのビジョン ソリューションを作成する](./tutorial-nocode-vision.md)
    - [Azure Percept Studio で音声アシスタントを作成する](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>次のステップ

[Microsoft のオンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で Azure Percept DK を注文します。
