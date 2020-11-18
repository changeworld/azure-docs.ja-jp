---
title: Azure Data Factory との違い
description: Azure Synapse Analytics と Azure Data Factory のデータ統合機能の違いについて説明します
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357651"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Azure Synapse Analytics と Azure Data Factory のデータ統合

Azure Synapse Analytics の Synapse パイプラインやデータ フローなどのデータ統合機能は、Azure Data Factory の機能に基づいています。 詳細については、「[Azure Data Factory とは](../../data-factory/introduction.md)」を参照してください。 ほぼすべての機能は同じか類似しており、この 2 つのサービス間でドキュメントは共有されます。 この記事では、Azure Data Factory と Azure Synapse の現時点での違いについて取り上げ、確認します。

Azure Data Factory の機能または記事が Azure Synapse に適用されるかどうかを確認するには、記事の上部にあるモニカーを確認してください。

![適用対象のモニカー](../media/concepts-data-factory-differences/applies-to-moniker.png "適用対象のモニカー")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Azure Synapse では計画されていない Azure Data Factory の機能

次の機能は、Azure Data Factory では使用できますが、Azure Synapse では計画されていません。

* **SSIS パッケージのリフト アンド シフト:** Azure Data Factory には、SSIS 統合ランタイムを使用して SSIS パッケージをリフト アンド シフトする機能があります。 SSIS 統合ランタイムと SSIS パッケージの実行アクティビティは、Synapse ワークスペースでは使用できません。 
* **Time to Live:** Time to Live は、マッピング データ フローでデータ フローの完了後に Spark クラスターの *ウォーム* 状態を一定期間維持できるようにする Azure 統合ランタイムの設定です。 この機能は、Synapse ワークスペースでは使用できません。

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Data Factory でサポートされていない Azure Synapse の機能

次の機能は、Azure Synapse では使用できますが、Azure Data Factory では計画されていません。

* **Spark ジョブによるマッピング データ フローの監視:** Synapse では、Spark エンジンはユーザーのサブスクリプションに含まれているため、ユーザーは詳細な Spark ログを確認できます。 Azure Data Factory では、ジョブの実行は Azure Data Factory で管理されている Spark クラスターで行われるため、この情報は使用できません。 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Synapse とは動作が異なる Azure Data Factory の機能

次の機能は、動作が異なるか、現在 Azure Synapse に存在しません。 

* **ラングリング データ フロー:** ラングリング データ フロー アクティビティは、現時点では Azure Data Factory でのみ使用可能です。
* **ソリューション テンプレート ギャラリー:** Azure Data Factory では、ユーザーはソリューション テンプレート ギャラリーでパイプライン テンプレートを見つけることができます。 Synapse ワークスペースでは、ナレッジ センターに、追加のデータセットや SQL スクリプトとともにさまざまなテンプレートのセットが含まれています。 
* **Git 統合とネイティブ CI/CD ソリューション:** 現時点では、Synapse ワークスペースは Git リポジトリに接続できず、Azure Data Factory と同じ継続的インテグレーションと配信プロセスにも従いません。
* **Azure Monitor との統合:** Synapse ワークスペースは、Azure Data Factory のように Azure Monitor と統合されません。
* **ハイブリッド統合ランタイム構成:** Synapse ワークスペースでは、ユーザーがマネージド VNet IR と Azure IR の両方を持つことはできません。 この機能は、Azure Data Factory でサポートされています。
* **統合ランタイムの共有:** セルフホステッド統合ランタイムは、Synapse ワークスペース間で共有できません。 この機能は、Azure Data Factory でサポートされています。
* **データ フロー用のリージョン間統合ランタイム:** Synapse ワークスペースとは異なるリージョンの統合ランタイムでは、データ フローを実行できません。 この機能は、Azure Data Factory でサポートされています。

## <a name="next-steps"></a>次の手順

[Azure Data Lake Storage gen2 アカウントにデータを取り込む](data-integration-data-lake.md)方法を確認して、Synapse ワークスペースでのデータ統合を開始します。
