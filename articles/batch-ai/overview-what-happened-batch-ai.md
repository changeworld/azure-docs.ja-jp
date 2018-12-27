---
title: Azure Batch AI の現状 | Microsoft Docs
description: Azure Batch AI と Azure Machine Learning service のコンピューティング オプションの現状について説明します。
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436877"
---
# <a name="whats-happening-to-azure-batch-ai"></a>Azure Batch AI の現状

**Azure Batch AI サービスは 3 月に提供が停止されます。** Batch AI の大規模トレーニングとスコアリング機能は、[Azure Machine Learning service](../machine-learning/service/overview-what-is-azure-ml.md) (2018 年 12 月 4 日に一般公開) で利用できるようになりました。

他の多くの機械学習機能と同様に、Azure Machine Learning service には、機械学習モデルのトレーニング、デプロイ、スコアリングのためのクラウドベースのマネージド コンピューティング ターゲットが含まれています。 このコンピューティング ターゲットは、[Azure Machine Learning コンピューティング](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)と呼ばれています。 [今すぐ移行して使ってみましょう](#migrate)。 Azure Machine Learning service との対話には、[Python SDK](../machine-learning/service/quickstart-create-workspace-with-python.md)、コマンドライン インターフェイス、および [Azure portal](../machine-learning/service/quickstart-get-started.md) を使用できます。

## <a name="support-timeline"></a>サポートのタイムライン

| 日付 | Batch AI サービスのサポートの詳細 |
| ---- |-----------------|
| 2018&nbsp;年 12 月&nbsp;14 日| 以前と同じように、既存の Azure Batch AI サブスクリプションを引き続き使用できます。 ただし、**新規サブスクリプション**は登録できなくなります。また、このサービスに新しい投資は行われなくなります。|
| 2019&nbsp;年 3 月&nbsp;31 日 | この日付を過ぎると、既存の Batch AI サブスクリプションは機能しなくなります。 |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>移行する方法

アプリケーションの中断を回避し、最新の機能を利用するには、2019 年 3 月 31 日より前に次の手順を実行します。

1. Azure Machine Learning service のワークスペースを作成し、次を開始します。
    + [Python ベースのクイック スタート](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Azure portal ベースのクイック スタート](../machine-learning/service/quickstart-get-started.md)

1. モデルのトレーニング用に [Azure Machine Learning コンピューティング](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute)を設定します。

1. Azure Machine Learning コンピューティングを使用するようにスクリプトを更新します。

## <a name="support"></a>サポート

より包括的な [Azure Machine Learning service](https://aka.ms/aml-docs) に移行する既存のお客様向けにサポートが提供されています。

サポートされている機能が Batch AI サービスに存在し、Azure Machine Learning service がお客様のニーズを満たしていない場合は、サポート チームに対して Batch AI サポート リクエストを開き、お客様のサブスクリプションをホワイトリストに登録して、サービスの終了まで Batch AI を使用できるように依頼してください。

## <a name="next-steps"></a>次の手順

+ 「[Azure Machine Learning サービスの概要](../machine-learning/service/overview-what-is-azure-ml.md)」を読みます。

+ Azure Machine Learning service を使用して[モデルのトレーニング用のコンピューティング ターゲットを構成](../machine-learning/service/how-to-set-up-training-targets.md)します。

+ [Azure のロードマップ](https://azure.microsoft.com/updates/)で、他の Azure サービスの更新情報を確認します。
