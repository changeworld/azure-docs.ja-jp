---
title: Azure Data Factory との違い
description: Azure Synapse Analytics と Azure Data Factory のデータ統合機能の違いについて説明します
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91339722"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics と Azure Data Factory のデータ統合

Azure Synapse Analytics の Synapse パイプラインやデータ フローなどのデータ統合機能は、Azure Data Factory の機能に基づいています。 詳細については、「[Azure Data Factory とは](../../data-factory/introduction.md)」を参照してください。 ほぼすべての機能は同じか類似しており、この 2 つのサービス間でドキュメントは共有されます。 この記事では、Azure Data Factory と Azure Synapse の現時点での違いについて取り上げ、確認します。

Azure Data Factory の機能または記事が Azure Synapse に適用されるかどうかを確認するには、記事の上部にあるモニカーを確認してください。

![適用対象のモニカー](../media/concepts-data-factory-differences/applies-to-moniker.png "適用対象のモニカー")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Azure Synapse では計画されていない Azure Data Factory の機能

次の機能は、Azure Data Factory では使用できますが、Azure Synapse では計画されていません。

* SSIS パッケージのリフト アンド シフト機能。
* コピー アクティビティとマッピング データ フローのシンクとしての Snowflake。
* Azure 統合ランタイムのライブ設定へのマッピング データ フロー時間。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Data Factory でサポートされていない Azure Synapse の機能

次の機能は、Azure Synapse では使用できますが、Azure Data Factory では計画されていません。

* Spark ジョブによるマッピング データ フローの監視は、Synapse でのみ使用できます。 Synapse では、Spark エンジンはユーザーのサブスクリプションに含まれているため、ユーザーは詳細な Spark ログを確認できます。 Azure Data Factory では、ジョブの実行は Azure Data Factory で管理されている Spark クラスターで行われます。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Synapse とは動作が異なる Azure Data Factory の機能

次の機能は、動作が異なるか、現在 Azure Synapse に存在しません。 

* ラングリング データ フロー
* ソリューション テンプレート ギャラリー
* Git 統合とネイティブ CI/CD ソリューション
* Azure Monitor との統合
* 発行後のリソースの名前変更
* Synapse ワークスペース内のハイブリッド統合ランタイム構成。 ユーザーは、マネージド VNet IR と Azure IR の両方を持つことはできません。
* Synapse ワークスペース間での統合ランタイムの共有

## <a name="next-steps"></a>次の手順

[Azure Data Lake Storage gen2 アカウントにデータを取り込む](data-integration-data-lake.md)方法を確認して、Synapse ワークスペースでのデータ統合を開始します。
