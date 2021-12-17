---
title: Azure Percept DK にビジョン AI モデルをデプロイする
description: Azure Percept Studio から Azure Percept DK にビジョン AI モデルをデプロイする方法について説明します
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 55948f2beede0597a30dd557a82ee297c6a540ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006375"
---
# <a name="deploy-a-vision-ai-model-to-azure-percept-dk"></a>Azure Percept DK にビジョン AI モデルをデプロイする

Azure Percept Studio 内から Azure Percept DK にビジョン AI モデルをデプロイするには、このガイドに従ってください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- [Azure Percept DK セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md): 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること

## <a name="model-deployment"></a>モデル デプロイ

1. 開発キットの電源をオンにします。

1. [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) に移動します。

1. 概要ページの左側にある **[デバイス]** をクリックします。

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Azure Percept Studio の概要画面。" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. 一覧から開発キットを選択します。

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Percept デバイスの一覧。":::

1. 事前にトレーニングされたいずれかのサンプル ビジョン モデルをデプロイする場合は、次のページで **[サンプル モデルのデプロイ]** をクリックします。 既にある [コーディングなしのカスタム ビジョン ソリューション](./tutorial-nocode-vision.md)をデプロイする場合は、 **[Custom Vision プロジェクトのデプロイ]** をクリックします。

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="デプロイのモデルの選択肢。":::

1. コーディングなしでビジョン ソリューションをデプロイしたい場合は、プロジェクトおよび望ましいモデルのイテレーションを選択し、 **[デプロイ]** をクリックします。

1. サンプル モデルをデプロイしたい場合は、モデルを選択し、 **[デバイスへのデプロイ]** をクリックします。

1. モデル デプロイが成功すると、画面の右上隅にステータス メッセージが表示されます。 モデル推論の動作を確認するには、ステータス メッセージの **[ストリームを表示します]** リンクをクリックして、開発キットの Vision SoM からの RTSP ビデオ ストリームを確認します。

## <a name="next-steps"></a>次のステップ

[Azure Percept DK のテレメトリ](how-to-view-telemetry.md)を表示する方法を学習します。
