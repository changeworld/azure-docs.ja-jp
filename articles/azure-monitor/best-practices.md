---
title: Azure Monitor に関するベスト プラクティス
description: Azure Monitor をデプロイするためのガイダンスと推奨事項。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: 0150a5ad522d4002d5c6f6ebbceaa7e80a6cd403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026148"
---
# <a name="azure-monitor-best-practices"></a>Azure Monitor に関するベスト プラクティス
このシナリオでは、クラウドおよびハイブリッドのアプリケーションとリソースのパフォーマンスと可用性を監視するように Azure Monitor の機能を構成するための推奨ガイダンスを示します。 

Azure Monitor は、Azure サブスクリプションを作成した時点で利用できるようになります。 アクティビティ ログでは、サブスクリプション内のアクティビティに関するイベントの収集が直ちに開始され、作成したすべての Azure リソースのプラットフォーム メトリックが収集されます。 データを分析するためにメトリックス エクスプローラーなどの機能を使用できます。 他の機能は構成が必要です。 このシナリオでは、Azure Monitor のすべての機能を活用するために必要な構成手順を示します。 また、どの機能を利用する必要があるか、および特定の要件に基づいてどのように構成オプションを決定するかに関する推奨も行います。

Azure Monitor ですべての Azure リソースを監視できるようにするには、Azure Monitor コンポーネントの構成と、Azure Monitor で収集される監視データを生成するための Azure リソースの構成を、組み合わせて行います。 完全な実装の目標は、すべてのクラウド リソースとアプリケーションから有用なすべてのデータを収集し、そのデータに基づいて Azure Monitor の全機能セットを有効にすることです。


> [!IMPORTANT]
> Azure Monitor を初めて使用する場合、または単純に 1 つの Azure リソースを監視することのみを行う場合は、チュートリアル「[Azure Monitor を使用した Azure リソースの監視](essentials/monitor-azure-resource.md)」を参照してください。 このチュートリアルでは、Azure Monitor の一般的な概念と、1 つの Azure リソースを監視するためのガイダンスを紹介します。 このシナリオでは、Azure Monitor のすべての機能を活用して、お使いのすべてのアプリケーションとリソースをまとめて大規模に監視するよう環境を準備するための推奨事項を示します。

## <a name="scope-of-the-scenario"></a>シナリオの範囲
このシナリオの目的は、Azure Monitor の完全な実装の基本的手順について説明することで、その機能を最大限に活用し、クラウドおよびハイブリッドのアプリケーションとリソースの監視を最大限に行うことができるようにすることです。 ここでは、実際の構成の詳細ではなく、構成の要件とデプロイ オプションに焦点を当てています。 必要な構成を実際に行うための詳細情報を示す、他のコンテンツへのリンクが提供されます。

## <a name="scenario-articles"></a>シナリオの記事
この記事ではシナリオを紹介します。 特定の分野を参照する場合は、次の表で説明するこのシナリオの一部である他のいずれかの記事を参照してください。

| [アーティクル] | 説明 |
|:---|:---|
| [Planning](best-practices-plan.md)  | 実装を開始する前に考慮する必要がある計画。 設計上の決定事項と、収集する必要がある組織と要件に関する情報が含まれています。 |
| [データ コレクションを構成する](best-practices-data-collection.md) | Azure およびハイブリッドのアプリケーションとリソースから監視データを収集するために必要なタスク。 |
| [分析と視覚化](best-practices-analysis.md) | 標準機能と、収集された監視データを分析するために作成できる追加の視覚化。 |
| [アラートと自動応答](best-practices-alerts.md) | アラートが作成されると自動的にトリガーされる通知とプロセスを構成します。 |


## <a name="next-steps"></a>次のステップ

- [監視の戦略と構成を計画する](best-practices-plan.md)
