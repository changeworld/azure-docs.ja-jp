---
title: シナリオ:Azure Firewall - Interhub ルーティング
titleSuffix: Azure Virtual WAN
description: ルーティングのシナリオ - Azure Firewall がある複数のハブ間のルーティング
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567625"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>シナリオ:Azure Firewall - Interhub (プレビュー)

Virtual WAN の仮想ハブ ルーティングを使用する場合、多くのシナリオを利用できます。 このシナリオの目標は、Azure Firewall がある複数のハブ間でルーティングを行うことです。 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。

## <a name="scenario-architecture"></a><a name="architecture"></a>シナリオのアーキテクチャ

このシナリオでは、次の構成を想定しています。

* 複数のハブがあり、各ハブに Azure Firewall がある。
* セキュリティ保護付き仮想ハブを使用している。
* プライベート トラフィック (VNet)、インターネット、およびブランチ トラフィックについて適切なポリシーが設定されている。
* 各ハブにおいて、VNet 対インターネット (V2I)、VNet 対ブランチ (V2B)、ブランチ対 VNet (B2V) のすべてが Azure Firewall を経由する。

追加の考慮事項:

* Azure Firewall を使用するハブ間のシナリオでは、スポーク Vnet は個別のルート テーブルに関連付けられていないことを前提としています。 (たとえば、**VNET 1** は**ルート テーブル A** に、**VNET 2** は**ルート テーブル B** に関連付けられています) すべての Vnet は、Azure Firewall のルートが存在する同じルート テーブルに関連付けられます。
* 現時点では、Azure Firewall を経由した保護は、**ブランチ <-> VNet** と**インターネット** トラフィックのみに限定されています。 現在、Azure Firewall を経由したブランチ間フローはサポートされていません。

**図 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="アーキテクチャ":::

## <a name="scenario-workflow"></a><a name="workflow"></a>シナリオのワークフロー

このシナリオを構成するには、次の手順を考慮してください。

### <a name="step-1"></a><a name="step-1"></a>手順 1

Azure Firewall Manager を使用して接続を既にセキュリティで保護していることを前提として、最初の手順では、すべてのブランチと VNet の接続が **None** を伝達するようにします。 これは、Azure Firewall 経由でトラフィックが設定されるようにするために必要です。

1. ハブを選択し、**None** ルート テーブルを編集します。
1. **Propagation** を更新して、すべてのブランチとすべての Vnet を選択します。

### <a name="step-2"></a><a name="step-2"></a>手順 2

ハブごとにカスタム ルート テーブルを設定します。

1. **Hub 1** 用に、ルート テーブル **RT_Hub1** を作成します。

    * Azure Firewall Manager を使用した場合、ハブの既定のルート テーブルに次ホップ **AZFW1** を含む 0.0.0.0/0 のルートが自動的に作成されます。 この設定は、手順 3 で変更します。 **RT_Hub1** に、次ホップ **AZFW1** を含む 0.0.0.0/0 のエントリを明示的に作成します。 この設定により、V2V、B2V、および V2I が AZFW1 経由でアクティブになります。
    * **Hub 2** ブランチと Vnet には、AZFW1 経由ではなく **Hub 1** の **AZFW2** 経由で到達する必要があるため、**Hub 2** ブランチ (10.5.0.0/16-> AZFW2) と Vnet (10.2.0.0/16-> AZFW2) に対して、2 つの伝達ルートを別に追加する必要があります。

1. **Hub 2** 用に、ルート テーブル **RT_Hub2** を作成します。

    * Azure Firewall Manager を使用した場合、ハブの既定のルート テーブルに次ホップ **AZFW2** を含む 0.0.0.0/0 のルートが自動的に作成されます。 この設定は、手順 3 で変更します。 **RT_Hub2** に、次ホップ **AZFW2** を含む 0.0.0.0/0 のエントリを明示的に作成します。 この設定により、V2V、B2V、および V2I が **AZFW2** 経由でアクティブになります。
    * ハブ間のトラフィックは Hub 2 の **AZFW2** で保護する必要があるため、さきほど Hub 1 で実行した手順のうち、2 番目の箇条書きに示されている手順を明示的に実行する必要はありません。

### <a name="step-3"></a><a name="step-3"></a>手順 3

各ハブの**既定のルート テーブル**を変更し、Azure Firewall を経由する**ブランチ対 VNet** (B2V) フローと**ブランチ対インターネット** (B2I) フローの静的ルートを追加します。 現在、Azure Firewall を経由したブランチ間フローはサポートされていません。

1. **Hub 1 の既定のルート テーブル**:

    * **AZFW2 経由でのブランチ対 Hub 2 ブランチ**: 10.5.0.0/16->AZFW2。 この構成により、Hub 2 のファイアウォールへのルートが設定されます。
    * **AZFW2 経由でのブランチ対 Hub 2 VNet**: 10.2.0.0/16->AZFW2。 この構成により、Hub 2 のファイアウォールへのルートが設定されます。
    * **ブランチ対ブランチ (ローカル)/ブランチ対 VNet (ローカル)/ブランチ対インターネット**: 0.0.0.0/0->AZFW1。

2. **Hub 2 の既定のルート テーブル**:

    * ブランチ対ブランチ (ローカルおよびリモート)/ブランチ対 VNet (ローカルおよびリモート)/ブランチ対インターネット: 0.0.0.0/0->AZFW2。

これで、ルート構成が次の図に示すように変更されます。

**図 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="workflow":::

## <a name="next-steps"></a>次のステップ

* Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。
* 仮想ハブ ルーティングの詳細については、「[仮想ハブのルーティングについて](about-virtual-hub-routing.md)」を参照してください。
* 仮想ハブ ルーティングの構成方法の詳細については、「[仮想ハブのルーティングを構成する方法](how-to-virtual-hub-routing.md)」を参照してください。
