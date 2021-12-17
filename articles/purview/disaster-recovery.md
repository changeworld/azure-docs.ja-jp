---
title: Azure Purview のディザスター リカバリー
description: Azure Purview のディザスター リカバリー環境を構成する方法を説明します。
author: sudheerreddykoppula
ms.author: sukoppul
ms.service: purview
ms.topic: how-to
ms.date: 04/23/2021
ms.openlocfilehash: a5b06b204cf0124a73639a9c2c59a2bc05712cb7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214296"
---
# <a name="disaster-recovery-for-purview"></a>Purview のディザスター リカバリー

この記事では、Azure Purview のディザスター リカバリー環境を構成する方法を説明します。 Azure データ センターの停止はほとんど発生しませんが、数分から数時間かかる可能性があります。 データ センターで停電が起こると、データ ガバナンスに使用している環境で障害が発生するおそれがあります。 この記事で詳述する手順により、Purview アカウントのプライマリ リージョンのデータ センターで停電が起こった場合にも、データ ガバナンスを継続できます。

## <a name="achieve-business-continuity-for-azure-purview"></a>Azure Purview で事業継続性を確保する

Azure Purview インスタンスにおいて、事業継続とディザスター リカバリー (BCDR) とは、障害が生じた場合、特にスキャン、カタログ、インサイト機能に対する障害が生じた場合に、事業のデータ喪失を防止して運営を継続するための仕組み、方針、手順を指します。 このページでは、Azure Purview のディザスター リカバリー環境を構成する方法を説明します。

現在、Azure Purview は自動 BCDR をサポートしていません。 このサポートが追加されるまで、バックアップと復元の作業はユーザーの手で行います。 他のリージョンでのウォーム スタンバイ インスタンスとして、手動でセカンダリ Purview アカウントを作成できます。

手動のディザスター リカバリーは、次の手順で行います。

1. 特定のリージョンでプライマリ Purview アカウントを作成したら、Azure portal で、1 つ以上のセカンダリ Purview アカウントを別のリージョンに用意する必要があります。 

2. プライマリ Purview アカウントに対して実行するすべての操作は、セカンダリ Purview アカウントでも実行する必要があります。 これには次のものが含まれます 

    - アカウント情報の管理
    - カスタム スキャン ルール セット、分類、分類ルールの作成と管理
    - ソースの登録とスキャン
    - コレクション、およびソースとコレクションの関連付けの作成と管理
    - スキャン時に使用する認証情報の作成と管理。
    - データ資産のキュレーション
    - 用語集の用語の作成と管理


手動 BCDR の計画を立てる際は、次のことを考慮します。 

- 課金は、プライマリおよびセカンダリ Purview アカウントに対して行われます。 

- Azure Data Factory、Azure Data Share、Synapse Analytics の同一アカウントに対して、プライマリおよびセカンダリ Purview アカウントの両方を構成することはできません。  そのため、Azure Data Factory と Azure Data Share の系列は、セカンダリ Purview アカウントで見ることができません。 また、プライマリ Purview アカウントに関連付けられた Synapse Analytics のワークスペースは、セカンダリ Purview アカウントに関連付けることができません。 これは現時点での制約であり、自動 BCDR のサポートの実装時に解消する予定です。 

- 各 Purview アカウントでは、それぞれ専用の統合ランタイムを使用します。 そのため、プライマリおよびセカンダリ Purview アカウントで並行してスキャンを行いたい場合は、複数のセルフホステッド統合ランタイムを管理する必要があります。 この制約も、自動 BCDR のサポートを実装するときに解消する予定です。 

- 同一のソースに対して、プライマリおよびセカンダリ Purview アカウントの両方から同時にスキャンを実行すると、ソースのパフォーマンスに影響が出るおそれがあります。 これによるスキャン時間への影響の大きさは Purview アカウントにより異なります。   

## <a name="related-information"></a>関連情報

- [事業継続とディザスター リカバリー](../best-practices-availability-paired-regions.md)
- [BCDR 戦略に高可用性を組み込む](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)
- [Azure の状態](https://status.azure.com/status)

## <a name="next-steps"></a>次のステップ

Azure Purview の使用を開始するには、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するページを参照してください。
