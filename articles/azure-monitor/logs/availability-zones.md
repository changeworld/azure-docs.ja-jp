---
title: Azure Monitor の可用性ゾーン
description: Azure Monitor の可用性ゾーン
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: 6e10ace3ca0932cf5803719429a68b89a4118dfa
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059323"
---
# <a name="availability-zones-in-azure-monitor"></a>Azure Monitor の可用性ゾーン

[Azure 可用性ゾーン](../../availability-zones/az-overview.md)を使用すると、データセンターの障害からアプリケーションとデータが保護されて、Application Insights などの Azure Monitor 機能と、Log Analytics ワークスペースに依存するその他すべての機能に回復性を提供できます。 ワークスペースが可用性ゾーンにリンクされていると、特定のデータセンターが正常に機能していなかったり完全にダウンしたりした場合でも、ワークスペースはリージョン内の他のゾーンの可用性に頼り、アクティブで運用可能な状態に留まります。 代替のゾーンに切り替えるために何かをする必要はなく、インシデントに気付く必要さえありません。 


## <a name="regions"></a>リージョン
可用性ゾーンがある Azure リージョンについては、「[Azure のリージョンと Availability Zones](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)」を参照してください。 Azure Monitor では現在、次のリージョンがサポートされています。 

- 米国東部 2
- 米国西部 2

## <a name="dedicated-clusters"></a>Dedicated クラスター
Azure Monitor での可用性ゾーンのサポートを利用するには、[Azure Monitor 専用クラスター](logs-dedicated-clusters.md)にリンクされた Log Analytics ワークスペース必要です。 専用クラスターは、Azure Monitor ログに関して、可用性ゾーンを含む高度な機能を利用できるようにするデプロイ オプションです。

どの専用クラスターでも可用性ゾーンを使用できるではありません。 2020 年 10 月中旬以降に作成される専用クラスターでは、作成時に可用性ゾーンをサポートするように設定できます。 その日以降に作成される新しいクラスターについては、Azure Monitor でサポートされているリージョンでは既定で可用性ゾーンが有効になります。


> [!NOTE]
> Application Insights リソースで可用性ゾーンを使用できるのは、リソースがワークスペース ベースであり、そのワークスペースが専用クラスターを使用している場合のみです。 Application Insights の従来のリソースでは可用性ゾーンを使用できません。


## <a name="determine-current-cluster-for-your-workspace"></a>ワークスペースの現在のクラスターを確認する
ワークスペースの現在のワークスペース リンクの状態を確認するには、[CLI、PowerShell、または REST](logs-dedicated-clusters.md#check-workspace-link-status) を使用して[クラスターの詳細](logs-dedicated-clusters.md#check-cluster-provisioning-status)を取得します。 クラスターで可用性ゾーンが使用されている場合は、`isAvailabilityZonesEnabled` というプロパティの値が `true` になります。 いったんクラスターが作成されると、このプロパティは変更できません。

## <a name="create-dedicated-cluster-with-availability-zone"></a>可用性ゾーンを使用する専用クラスターを作成する
可用性ゾーンをサポートしているリージョン内に[新しい専用クラスターを作成](logs-dedicated-clusters.md#create-a-dedicated-cluster)することで、ワークスペースを可用性ゾーンに移動します。 そのクラスターでは可用性ゾーンが自動的に有効になります。 次に、[ワークスペースを新しいクラスターにリンクします](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster)。

> [!IMPORTANT]
> 可用性ゾーンは作成時にクラスターで定義されており、変更することはできません。

新しいクラスターへの移行は、段階的なプロセスとなる場合があります。 以前のクラスターは、すべてのデータが消去されるまで削除しないでください。 たとえば、ワークスペースのデータ保持が 60 日に設定されている場合、古いクラスターは、削除する前にその期間の間、運用を続ける必要があります。

ワークスペースに対するクエリでは、必要に応じて両方のクラスターに対してクエリが実行され、統合された 1 つの結果セットが提供されます。 つまり、ワークスペースに依存する、ブックやダッシュボードなどのすべての Azure Monitor 機能では、両方のクラスターのデータに基づいて統合された完全な結果セットの取得を続けることになります。

## <a name="billing"></a>課金
[専用クラスターの使用にはコスト](logs-dedicated-clusters.md#create-a-dedicated-cluster)がかかります。 1 日あたりの容量予約として 500 GB 必要です。 

専用クラスターが既にあり、そのデータにアクセスするためにそれを保持することを選択すると、両方の専用クラスターに対して課金されます。 2021 年 8 月 4 日から、専用クラスターに必要な最小限の容量予約は 1 日あたり 1000 GB から 1 日あたり 500 GB に削減されます。そのため、料金を削減するために、古いクラスターにはその最小値を適用することをお勧めします。

構成中の二重請求を回避するため、新しいクラスターへの課金は最初の日には行われません。 移行日に引き続き請求されるのは、移行の完了前に取り込まれたデータだけになります。 


## <a name="next-steps"></a>次のステップ

- Log Analytics でユーザーがクエリ パックを操作する方法については、「[Azure Monitor Log Analytics でのクエリの使用](queries.md)」を参照してください。

