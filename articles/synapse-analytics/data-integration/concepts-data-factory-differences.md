---
title: Azure Data Factory との違い
description: Azure Synapse Analytics と Azure Data Factory のデータ統合機能の違いについて説明します
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: d8320c6356f310584d24c51a384446dfdcb22afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128608168"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics と Azure Data Factory のデータ統合

Azure Synapse Analytics の Synapse パイプラインやデータ フローなどのデータ統合機能は、Azure Data Factory の機能に基づいています。 詳細については、「[Azure Data Factory とは](../../data-factory/introduction.md)」を参照してください。


## <a name="available-features-in-adf--azure-synapse-analytics"></a>ADF および Azure Synapse Analytics で利用可能な機能

次の表で、利用可能な機能を確認してください。

| カテゴリ                 | 特徴量    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | SSIS および SSIS Integration Runtime の使用 | ✓ | ✗ |
|                          | リージョン間 Integration Runtime (データ フロー) のサポート | ✓ | ✗ |
|                          | Integration Runtime の共有 | ✓<br>"<small>*異なるデータ ファクトリ間で共有できる*" | ✗ |
| **パイプライン アクティビティ** | SSIS パッケージ アクティビティ | ✓ | ✗ |
|                          | Power Query アクティビティのサポート | ✓ | ✗ |
| **テンプレート ギャラリーとナレッジ センター** | ソリューション テンプレート | ✓<br>"<small>*Azure Data Factory のテンプレート ギャラリー*" | ✓<br><small>*Synapse ワークスペースのナレッジ センター* |
| **Git リポジトリの統合** | Git 統合 | ✓ | ✓ |
| **Monitoring**           | データ フローについての Spark ジョブの監視 | ✗ | ✓<br>"<small>*Synapse Spark プールを活用する*" |

## <a name="next-steps"></a>次の手順

[Azure Data Lake Storage gen2 アカウントにデータを取り込む](data-integration-data-lake.md)方法を確認して、Synapse ワークスペースでのデータ統合を開始します。
