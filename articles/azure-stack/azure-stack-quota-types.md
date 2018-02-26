---
title: "Azure Stack のクォータの種類 | Microsoft Docs"
description: "Azure Stack のサービスとリソースで使用可能なさまざまなクォータの種類を確認します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/23/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: da4105fa88848b14e5d5d0289859cfd0f85c8fee
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack のクォータの種類

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

[クォータ](azure-stack-plan-offer-quota-overview.md#plans)は、ユーザー サブスクリプションがプロビジョニングまたは使用できるリソースの制限を定義します。 たとえば、1 つのクォータでユーザー 1 人に最大 5 つの仮想マシンの作成を許可できます。 各リソースは、独自のクォータの種類を持つことが可能です。

## <a name="compute-quota-types"></a>Compute クォータの種類
| **種類** | **既定値** | **説明** |
| --- | --- | --- |
| [Max number of virtual machines]\(仮想マシンの最大数\) | 20 | 1 つのサブスクリプションでこの場所に作成できる仮想マシンの最大数です。 |
| [Max number of virtual machine cores]\(仮想マシン コアの最大数\) | 50 | 1 つのサブスクリプションでこの場所に作成できるコアの最大数です (たとえば、A3 VM のコア数は 4 です)。 |
| [Max number of availability sets]\(可用性セットの最大数\) | 10 | この場所に作成できる可用性セットの最大数です。 |
| [Max number of virtual machine scale sets]\(仮想マシン スケール セットの最大数\) | 20 | この場所に作成できる仮想マシン スケールセットの最大数です。 |

> [!NOTE]
> このテクニカル プレビューでは、Compute クォータは強制されていません。
> 
> 

## <a name="storage-quota-types"></a>Storage クォータの種類
| **項目** | **既定値** | **説明** |
| --- | --- | --- |
| 最大容量 (GB) |500 |1 つのサブスクリプションがこの場所で使用できる記憶域の最大容量です。 |
| [Total number of storage accounts]\(ストレージ アカウントの合計数\) |20 |1 つのサブスクリプションでこの場所に作成できるストレージ アカウントの最大数です。 |

## <a name="network-quota-types"></a>Network クォータの種類
| **項目** | **既定値** | **説明** |
| --- | --- | --- |
| [Max public IPs]\(パブリック IP の最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるパブリック IP の最大数です。 |
| [Max virtual networks]\(仮想ネットワークの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できる仮想ネットワークの最大数です。 |
| [Max virtual network gateways]\(仮想ネットワーク ゲートウェイの最大数\) |1 |1 つのサブスクリプションでこの場所に作成できる仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) の最大数です。 |
| [Max network connections]\(ネットワーク接続の最大数\) |2 |1 つのサブスクリプションで、すべての仮想ネットワーク ゲートウェイにまたがってこの場所に作成できるネットワーク接続 (ポイント対ポイントまたはサイト対サイト) の最大数です。 |
| [Max load balancers]\(ロード バランサーの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるロード バランサーの最大数です。 |
| 最大 NIC 数 |100 |1 つのサブスクリプションでこの場所に作成できるネットワーク インターフェイスの最大数です。 |
| [Max network security groups]\(ネットワーク セキュリティ グループの最大数\) |50 |1 つのサブスクリプションでこの場所に作成できるネットワーク セキュリティ グループの最大数です。 |

## <a name="view-an-existing-quota"></a>[View an existing quota]\(既存のクォータの表示\)
1. **[その他のサービス]** > **[リソース プロバイダー]** をクリックします。
2. 参照するクォータを使用したサービスを選択します。
3. **[クォータ]** をクリックして、参照するクォータを選択します。

## <a name="next-steps"></a>次の手順
[プラン、オファーおよびクォータについて](azure-stack-plan-offer-quota-overview.md)

[プランの作成時にクォータを作成する](azure-stack-create-plan.md)
