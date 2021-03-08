---
title: Azure Monitor for containers のリージョン マッピング
description: Azure Monitor for containers、Log Analytics ワークスペース、カスタム メトリックの間でサポートされるリージョン マッピングについて説明します。
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: 9b77242de3e7845a97874b663266103bf00a8e66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100604134"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Azure Monitor for containers でサポートされるリージョン マッピング

 Azure Monitor for containers を有効にした場合、Log Analytics ワークスペースと AKS クラスターをリンクし、Azure Monitor に送信されたカスタム メトリックを収集するために、特定のリージョンのみサポートされます。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics ワークスペースでサポートされるマッピング

サポートされている AKS リージョンの一覧は[リージョン別に利用できる製品](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)に関するページを参照してください。 Log Analytics ワークスペースは、次の表に示すリージョンを除き、同じリージョンにある必要があります。 更新プログラムについては、[AKS リリース ノート](https://github.com/Azure/AKS/releases)をご覧ください。


|**AKS クラスター リージョン** | **Log Analytics ワークスペース リージョン** |
|-----------------------|------------------------------------|
|**アフリカ** | |
|SouthAfricaNorth |西ヨーロッパ |
|SouthAfricaWest |西ヨーロッパ |
|**オーストラリア** | |
|AustraliaCentral2 |AustraliaCentral |
|**ブラジル** | |
|BrazilSouth | SouthCentralUS |
|**カナダ** ||
|CanadaEast |CanadaCentral |
|**ヨーロッパ** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**インド** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanWest |JapanEast |
|**韓国** | |
|KoreaSouth |KoreaCentral |
|**米国** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 容量の制約により、新しいリソースを作成するときにリージョンを使用できません。 これには Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="custom-metrics-supported-regions"></a>カスタム メトリックでサポートされるリージョン

Azure Kubernetes Services (AKS) クラスター ノードおよびポッドからのメトリックの収集は、次の [Azure リージョン](../essentials/metrics-custom-overview.md#supported-regions)でのみカスタム メトリックとしての公開がサポートされています。

## <a name="next-steps"></a>次のステップ

AKS クラスターの監視を開始するには、「[Azure Monitor for containers を有効にする方法](container-insights-onboard.md)」を参照し、監視を有効にするための要件と使用できる方法を把握してください。  
