---
title: Azure IoT Edge 用 AI ツールキットを使用する | Microsoft Docs
description: この記事では、Azure IoT Edge 用 AI ツールキットの使用方法について説明します。
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 2/1/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 023674f05dda6c2da8edddb843277dd07a513162
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244629"
---
# <a name="use-the-ai-toolkit-for-azure-iot-edge"></a>Azure IoT Edge 用 AI ツールキットを使用する

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

人工知能、機械学習、および高度な分析の能力をエッジで活用しましょう。 Azure IoT Edge 用 AI ツールキットを使用すれば、データがある場所に AI や機械学習をデプロイできます。 REST API を使用してモデルを Docker コンテナーとして運用化した後、Azure IoT Hub を使用したエッジ デバイスにモデルをプッシュします。 Azure IoT Edge 用 AI ツールキットは、接続が制限されている場所や、断続的に切断される場所、あるいは接続されていない場所に、インテリジェンス、柔軟なコンピューティング、およびクラウドのパワーを提供します。

Azure IoT Edge 用 AI ツールキットは、スクリプト、コード、およびデプロイ可能なコンテナーのコレクションです。 サンプルには、予測メンテナンス、イメージ分類、音声処理のほか、Azure Machine Learning および Azure IoT Hub を通じたカスタム モデル デプロイが含まれています。 ツールキットに含まれているモデルはそのまま使用することもできますが、すべてのソース コードとデータは、開発者のニーズに応じてカスタマイズすることができます。

Azure IoT Edge 用 AI ツールキットのパブリック GitHub リポジトリは、[aka.ms/AI-toolkit](https://aka.ms/AI-toolkit) にあります。