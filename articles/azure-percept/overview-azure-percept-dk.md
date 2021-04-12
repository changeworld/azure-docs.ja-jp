---
title: Azure Percept DK の概要
description: Azure Percept DK の詳細情報
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: b20448a88cee6c4ea4af0bcbfaa6ec465f2e28bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105560966"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK の概要

Azure Percept DK は、[Azure Percept Studio](./overview-azure-percept-studio.md) を使用してビジョンおよびオーディオ AI のために設計されたエッジ AI 開発キットです。 Azure Percept DK は、[Microsoft オンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で購入できます。

> [!div class="nextstepaction"]
> [今すぐ購入](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>主要な機能

- エッジで AI を実行します。 組み込みのハードウェア アクセラレータを使用すると、開発キットは、クラウドに接続しなくても、AI モデルを実行できます。

- 信頼のハードウェア ルート セキュリティを組み込み。 詳細については、[Azure Percept のセキュリティ](./overview-percept-security.md)に関するページを参照してください。

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) やその他の Azure サービス (Azure IoT Hub、Azure Cognitive Services、[Live Video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview) など) とのシームレスな統合。

- [Azure Percept Audio](./overview-azure-percept-audio.md) と互換性があります。AI オーディオ ソリューションを構築するためのオプションのアクセサリです。

- ONNX や TensorFlow などのサードパーティ製 AI ツールのサポート。

- 80/20 レール システムとの統合。これにより、デバイスのマウントを無制限に構成できます。 [80/20 統合](./overview-8020-integration.md)について詳しくは、こちらをご覧ください。

## <a name="hardware-components"></a>ハードウェア コンポーネント

- Azure Percept DK キャリア ボード:
    - NXP iMX8m プロセッサ
    - トラステッド プラットフォーム モジュール (TPM) バージョン 2.0
    - Wi-Fi および Bluetooth 接続
    - 詳細については、「[Azure Percept DK のデータシート](./azure-percept-dk-datasheet.md)」を参照してください。

- Azure Percept Vision システム オン モジュール (SoM):
    - Intel Movidius Myriad X (MA2085) ビジョン処理ユニット (VPU)
    - RGB カメラ センサー
    - 詳細については、「[Azure Percept Vision のデータシート](./azure-percept-vision-datasheet.md)」を参照してください。

## <a name="getting-started-with-azure-percept-dk"></a>Azure Percept DK の使用の開始

- 開発キットを次のように設定します。
    - [Azure Percept DK を箱から取り出して組み立てる](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept DK セットアップ エクスペリエンスを完了する](./quickstart-percept-dk-set-up.md)

- ビジョンとオーディオ ソリューションの構築を開始します。
    - [Azure Percept Studio でコードなしのビジョン ソリューションを作成する](./tutorial-nocode-vision.md)
    - [Azure Percept Studio でコードなしの音声ソリューションを作成する](./tutorial-no-code-speech.md) (Azure Percept Audio アクセサリが必要)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Microsoft のオンライン ストアで Azure Percept DK を購入する](https://go.microsoft.com/fwlink/p/?LinkId=2155270)