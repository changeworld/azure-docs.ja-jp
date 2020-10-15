---
title: Azure Network Watcher のネットワーク構成診断の概要 | Microsoft Docs
description: このページでは、Network Watcher のネットワーク構成診断の概要について説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90993170"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure Network Watcher のネットワーク構成診断の概要

ネットワーク構成診断ツールは、お客様がデバッグのための詳細情報と共に、Azure Virtual Network でどのトラフィック フローが許可または拒否されるのかを理解するために役立ちます。 これは、NSG 規則が適切に構成されているかどうかを理解するのに役立ちます。 

## <a name="pre-requisites"></a>前提条件
ネットワーク構成診断を使用するには、サブスクリプションで Network Watcher が有効になっている必要があります。 有効にするには、「[Azure Network Watcher のインスタンスの作成](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)」を参照してください。

## <a name="background"></a>背景

- Azure のリソースは、仮想ネットワーク (VNet) とサブネット経由で接続されます。 これらの VNet とサブネットのセキュリティは、ネットワーク セキュリティ グループ (NSG) を使用して管理できます。
- NSG には、接続先のリソースへのネットワーク トラフィックを許可または拒否するセキュリティ規則の一覧が含まれています。 NSG は、サブネット、個々の VM、または VM に接続された個々のネットワーク インターフェイス (NIC) に関連付けることができます。 
- ネットワーク内のすべてのトラフィック フローは、該当する NSG のルールを使用して評価されます。
- 規則は、優先順位番号 (昇順) に基づいて評価されます。 

## <a name="how-does-network-configuration-diagnostic-work"></a>ネットワーク構成診断のしくみ 

指定されたフローに対して、NCD ツールはフローのシミュレーションを実行し、そのフローが許可 (または拒否) されるかどうかと、そのフローを許可または拒否する規則に関する詳細情報を返します。  お客様は、フローの詳細 (ソース、宛先、プロトコルなど) を指定する必要があります。このツールは、トラフィックが許可または拒否されたかどうか、指定されたフローに対して評価された NSG 規則、すべての規則の評価結果を返します。

## <a name="next-steps"></a>次のステップ

他のインターフェイスでネットワーク構成診断を使用する
 - [REST API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)

