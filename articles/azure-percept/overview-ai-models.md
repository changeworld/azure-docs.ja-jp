---
title: Azure Percept AI モデル
description: プロトタイプとデプロイに使用できる AI モデルについての詳細情報
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: f10a330de52d40f728cd628a1d7cb83b54ad1ff6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557362"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI モデル

Azure Percept を使用すると、[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) で AI モデルを開発し、[Azure Percept DK](./overview-azure-percept-dk.md) に直接デプロイすることができます。 モデル デプロイでは、 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) と [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview) が利用されます。

## <a name="sample-ai-models"></a>AI モデルのサンプル

Azure Percept Studio には、次のアプリケーションのサンプル モデルが含まれています。

- 人物検出
- 車両検出
- 一般的な物体検出
- 棚の製品検出

事前トレーニング済みのモデルでは、コーディングやトレーニング データの収集は必要ありません。 ポータルから Azure Percept DK に[目的のモデルをデプロイし](./how-to-deploy-model.md)、devkit の[ビデオ ストリーム](./how-to-view-video-stream.md)を開いて、モデル推論の動作を確認するだけです。 [モデル推論テレメトリには](./how-to-view-telemetry.md)、[Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases) ツールからアクセスすることもできます。

## <a name="reference-solutions"></a>リファレンス ソリューション

[人を数えるリファレンス ソリューション](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app)も利用できます。 このリファレンス ソリューションは、ユーザー定義のゾーン エントリ/エグジット イベントを使用してエッジベースの人物カウントを行うオープンソースの AI アプリケーションです。 オンプレミスのエッジデバイスからのビデオと AI の出力は、Azure Web サイトとして実行されているユーザー インターフェイスを使用して [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) にエグレスされます。 AI 推論は、人物検出のためにオープンソースの AI モデルによって提供されます。

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="事前に構築された空間分析ソリューションの gif。":::

## <a name="custom-no-code-solutions"></a>コードなしのカスタム ソリューション

Azure Percept Studio を使用すると、カスタムの[ビジョン](./tutorial-nocode-vision.md)および[スピーチ](./tutorial-no-code-speech.md) ソリューションを開発できます。コーディングは必要ありません。

カスタム ビジョン ソリューションでは、物体検出モデルと分類 AI モデルの両方を使用できます。 トレーニング イメージをアップロードしてタグ付けするだけで、必要に応じて、Azure Percept DK の Azure Percept Vision SoM で直接取得できます。 モデルのトレーニングと評価は、 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview) の一部である [Custom Vision](https://www.customvision.ai/) で簡単に実行できます。

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

カスタム スピーチ ソリューションについては、現在次の用途の音声アシスタント テンプレートを利用できます。

- 接客業: 音声制御スマート デバイスを備えたホテルの部屋。
- 医療機関: 音声制御スマート デバイスを備えた治療施設。
- インベントリ: 音声制御スマート デバイスを備えたインベントリ ハブ。
- 自動車: 音声制御スマート デバイスを備えた自動車ハブ。

事前に構築された音声アシスタントのキーワードとコマンドは、ポータルから直接入手できます。 カスタム キーワードとコマンドは、Azure Cognitive Services の一部でもある [Speech Studio](https://speech.microsoft.com/) で作成およびトレーニングできます。

## <a name="advanced-development"></a>高度な開発

次のような作業の最新のガイダンス、チュートリアル、例については、「[Azure Percept DK の高度な開発 GitHub](https://github.com/microsoft/azure-percept-advanced-development)」を参照してください。

- Azure Percept DK にカスタム AI モデルをデプロイする
- 転送学習でサポートされているモデルを更新する
- その他
