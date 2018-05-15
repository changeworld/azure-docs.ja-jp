---
title: Azure Machine Learning と FPGA
description: FPGA を使用してモデルとディープ ニューラル ネットワークを高速化する方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>Intel Field-programmable Gate Arrays (FPGA) とは

Intel Field-programmable Gate Arrays (FPGA) は、必要に応じて再構成できる集積回路です。 必要に応じて FPGA を変更して、新しいロジックを実装できます。 Azure Machine Learning のハードウェア 高速化モデルでは、トレーニング済みのモデルを Azure クラウド内の FPGA にデプロイできます。

この機能は Project Brainwave を活用したものであり、ディープ ニューラル ネットワーク (DNN) の FPGA プログラムへの変換を処理します。 

## <a name="why-use-an-fpga"></a>FPGA を使用する理由

FPGA の遅延は極端に短く、それらはバッチ サイズでのデータのスコア付けで特に有効です (大きなバッチ サイズ要件はありません)。  それらはコスト効率が高く、Azure で利用できます。  Project Brainwave は、事前トレーニング済みの DNN を これらの FPGA 間で並行化して、サービスをスケール アウトできます。

## <a name="next-steps"></a>次の手順

[モデルを Web サービスとして FPGA にデプロイする](how-to-deploy-fpga-web-service.md)

[FPGA SDK のインストール方法を確認する](reference-fpga-package-overview.md)