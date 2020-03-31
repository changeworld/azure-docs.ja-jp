---
title: Azure Data Share のディザスター リカバリー
description: Azure Data Share のディザスター リカバリー
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: a736e3ddfcf785f9ce27140eed58374a0732c1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75475969"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Azure Data Share のディザスター リカバリー

この記事では、Azure Data Share のディザスター リカバリー環境を構成する方法について説明します。 Azure データ センターの停止はほとんど発生しませんが、数分から数時間かかる可能性があります。 データ センターの停止により、データ プロバイダーによって共有されているデータに依存する環境が中断する可能性があります。 データ プロバイダーは、この記事で詳しく説明する手順に従うことで、データ共有をホストしているプライマリ リージョンでデータ センターが停止した場合でも、引き続きデータ コンシューマーとデータを共有できます。 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Azure Data Share のビジネス継続性の実現

データ プロバイダーは、データ センターの停止に備えるために、セカンダリ リージョンにプロビジョニングされたデータ共有環境を持つことができます。 データ センターの停止が発生した場合に、円滑なフェールオーバーを行うために実行できる対策があります。 

データ プロバイダーは、追加のリージョンにセカンダリ Azure Data Share リソースをプロビジョニングできます。 これらの Data Share リソースは、プライマリ データ共有環境に存在するデータセットを含めるように構成できます。 データ コンシューマーは、DR 環境を構成するときにデータ共有に追加するか、後で (つまり、 手動フェールオーバー手順の一環として) 追加することができます。

データ コンシューマーがプロビジョニングされたセカンダリ環境にアクティブな DR のために共有サブスクリプションを持っている場合、フェールオーバーの一環としてスナップショット スケジュールを有効にすることができます。 データ コンシューマーが DR のためにセカンダリ リージョンにサブスクライブしたくない場合は、後でセカンダリ データ共有に招待することができます。 

データ コンシューマーは、アイドル状態のアクティブな DR 用共有サブスクリプションを持つことができます。または、データ プロバイダーが手動フェールオーバーの手順の一環として後でそれらを追加することができます。 

## <a name="related-information"></a>関連情報

- [事業継続とディザスター リカバリー](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
- [BCDR 戦略に高可用性を組み込む](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>次のステップ

データの共有を始める方法については、[データの共有](share-your-data.md)に関するチュートリアルをご覧ください。




