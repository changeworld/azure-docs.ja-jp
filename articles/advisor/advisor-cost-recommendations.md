---
title: Azure Advisor のコストに関する推奨事項 | Microsoft Docs
description: Azure Advisor を使用して、Azure のデプロイにかかるコストを最適化します。
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: ade6ef996c00c0c06d5b8e44815520e6e4ab7e9f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2018
ms.locfileid: "34735869"
---
# <a name="advisor-cost-recommendations"></a>Advisor のコストに関する推奨事項

Advisor は、アイドル状態にあるリソースや活用されていないリソースを識別することによって Azure を最適化し、総合的な Azure の支出を削減します。 コストに関する推奨事項は、Advisor ダッシュボードの **[コスト]** タブで取得できます。

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>使用率が低いインスタンスをサイズ変更またはシャットダウンして仮想マシンの支出を最適化する 
特定のアプリケーション シナリオでは、設計によっては使用率が低くなる場合がありますが、多くの場合、仮想マシンのサイズと数を管理することによってコストを削減できます。 Advisor は、仮想マシンの使用状況を 14 日間にわたって監視して、使用率が低い仮想マシンを識別します。 CPU 使用率が 5% 以下で、ネットワークの使用率が 7 MB 以下である日が 4 日以上ある仮想マシンは、使用率が低い仮想マシンと見なされます。

Advisor には、そのような仮想マシンの使用を継続した場合の推定コストが示され、シャット ダウンまたはサイズ変更を選択できます。

使用率が低い仮想マシンを積極的に特定する場合は、サブスクリプションごとに平均 CPU 使用率ルールを調整できます。

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>プロビジョニングが解除された ExpressRoute 回線を排除してコストを削減する
Advisor は、プロバイダーの状態が 1 か月以上 "*未プロビジョニング*" である ExpressRoute 回線を特定し、接続プロバイダーを使用してその回線をプロビジョニングする予定がない場合は回線を削除するよう勧めます。

## <a name="buy-virtual-machine-reserved-instances-to-save-money-over-pay-as-you-go-costs"></a>従量課金のコストより費用を節約する仮想マシンの予約インスタンスを購入する
Advisor は、過去 30 日間の仮想マシンの使用率を確認し、予約インスタンスを購入することでコストを節約できるかどうかを判断します。 Advisor は、最も節約できる可能性がある領域とサイズを示し、予約インスタンスを購入することによって見込める節約を示します。 

予約インスタンスでは、お使いの仮想マシンの基本コストを事前購入できます。 割引は、予約インスタンスと同じサイズと領域の新しいまたは既存の VM に自動適用されます。 [Azure Reserved VM Instances の詳細を参照してください。](https://azure.microsoft.com/pricing/reserved-vm-instances/)

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Azure Advisor のコストに関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードで、**[コスト]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項の詳細については、以下を参照してください。
* [Advisor 入門](advisor-overview.md)
* [作業の開始](advisor-get-started.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor の高可用性に関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-cost-recommendations.md)
