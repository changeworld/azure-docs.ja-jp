---
title: Azure Monitor のブランド変更 | Microsoft Docs
description: Azure 管理サービスで最近行われたブランドと名称の変更について説明します。
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 68986b3d60566ee190a92c6ccf8cda767824350f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000146"
---
# <a name="azure-monitor-branding-changes"></a>Azure Monitor のブランドの変更
この記事では、Azure 管理サービスで最近行われたブランドと名称の変更について説明します。 

## <a name="consolidation-of-monitoring-services-into-azure-monitor"></a>監視サービスを Azure Monitor への統合
Log Analytics と Application Insights は、Azure リソースとハイブリッド環境を監視する単一の統合エクスペリエンスを提供するために Azure Monitor に統合されました。 これらのサービスから削除された機能はありません。機能を失ったり妥協したりすることなく、これまでと同じシナリオを実行できます。

これらの各サービスのドキュメントは、Azure Monitor の単一のコンテンツ セットに統合されています。 そのため、特定の監視シナリオに関するすべてのコンテンツを 1 つの場所で見つけることができます。複数のコンテンツ セットを参照する必要はありません。 統合サービスの進化に伴って、今後もコンテンツの統合を進める予定です。

## <a name="log-analytics-redefinition"></a>Log Analytics の再定義
Log Analytics は、Azure の管理において中心的役割を果たします。たとえば、さまざまなソースからテレメトリなどのデータを収集します。また、アプリケーションやリソースの運用に欠かせない分析情報を得る手段としてクエリ言語や分析エンジンを備えていることも、そうした役割の 1 つです。 この重要な役割は、Azure Monitor の機能として今後も継続されます。 エージェントやソリューションなど、Log Analytics の一部と見なされていた他の機能は、Azure Monitor の機能に変更されます。 機能は、Azure portal のエクスペリエンスに加えられる機能の改善以外に変更点はありません。

## <a name="retirement-of-operations-management-suite-brand"></a>Operations Management Suite ブランドの廃止
Operations Management Suite (OMS) は、ライセンス目的で以下の Azure 管理サービスをバンドルしたものです。

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[これらのサービスに新しい価格設定が導入されました](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。新規のお客様は OMS バンドルを入手できなくなりました。 前述の Azure Monitor への統合を除いて、OMS の一部だったサービスは変更されていません。 



## <a name="next-steps"></a>次の手順

- さまざまなコンポーネントと機能については、[Azure Monitor の 概要](overview.md)に関するページを参照してください。
- [OMS ポータルの移行](../azure-monitor/platform/oms-portal-transition.md)に関するページを参照してください。