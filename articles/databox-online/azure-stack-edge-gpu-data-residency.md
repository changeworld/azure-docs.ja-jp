---
title: Azure Stack Edge Pro GPU、Pro R、Mini R のデータ所在地と回復性
description: Azure Stack Edge のデータ所在地体制について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: alkohli
ms.openlocfilehash: 01f1a0ca013dad76510df99b70a462b96bec07a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078657"
---
# <a name="data-residency-and-resiliency-for-azure-stack-edge"></a>Azure Stack Edgeのデータ所在地と回復性 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge のデータ所在地と回復性の動作を理解するために必要な情報と、このサービスでデータ所在地を有効にする方法について説明します。  

## <a name="about-data-residency-for-azure-stack-edge"></a>Azure Stack Edge のデータ所在地について 

Azure Stack Edge サービスでは、サービスが利用可能なすべての地域に顧客データを格納して処理するときに [Azure リージョン ペア](../best-practices-availability-paired-regions.md#azure-regional-pairs)が使用されます。 東南アジア (シンガポール) リージョンの場合、サービスは現在、香港とペアになっています。 Azure リージョンのペアリングは、シンガポールに格納されているすべてのデータが香港でレプリケートされることを意味します。 シンガポールには、顧客データが国境を越えないことを要求する法律があります。 

顧客データが 1 つのリージョンのみに存在することを保証するため、Azure Stack Edge サービスで新しいオプションが有効になりました。 このオプションを選択すると、サービスで、シンガポール リージョンでのみ顧客データを格納および処理できます。 顧客データは香港にレプリケートされません。 ペア リージョンに引き続きレプリケートされるサービス固有のメタデータ (機密データではない) があります。  

このオプションを有効にすると、サービスでゾーン全体の停止に対する回復性が備わりますが、リージョン全体の停止に対してはそうではありません。 リージョン全体の停止が重要な場合は、リージョン ペア ベースのレプリケーションを選択する必要があります。

単一リージョンのデータ所在地オプションは、東南アジア (シンガポール) でのみ使用できます。 その他のリージョンではすべて、Azure Stack Edge により、顧客が指定した地域に顧客データが格納および処理されます。

サービスの次の側面について、Azure Stack Edge サービスのデータ所在地体制をまとめることができます。

- 既存の Azure Stack Edge の注文と管理サービス。
- 今後の新しい注文に使用される新しい Azure Edge Hardware Center (プレビュー)。
<!--- Telemetry for the device and the service.
- Proactive Support log collection where any logs that the service generates are stored in a single region and are not replicated to the paired region.-->

また、Azure Stack Edge サービスは、次の依存サービスと統合され、それらの動作もまとめられています。 

- Azure Arc 対応 Kubernetes
- Azure IoT Hub と Azure IoT Edge
<!--- Azure Key Vault -->


## <a name="azure-stack-edge-classic-ordering-and-management-resource"></a>Azure Stack Edge の従来の注文と管理リソース 

従来のエクスペリエンスを使用して Azure Stack Edge の注文を行う場合、サービスでは現在、リージョン全体の停止に対するデータ回復性を実装するために Azure リージョン ペアが使用されます。 シンガポールの既存の Azure Stack Edge リソースの場合、データは香港にレプリケートされます。

新しい Azure Stack Edge リソースを作成する場合は、シンガポールでのみデータを有効にするオプションがあります。 このオプションを選択した場合、データは香港にレプリケートされません。 リージョン全体でサービスが停止している場合は、次の 2 つの選択肢があります。

- シンガポール リージョンが復元されるまで待ちます。

- 別のリージョンにリソースを作成し、デバイスをリセットして、新しいリソースを介してデバイスを管理します。 詳細な手順については、「[Azure Stack Edge デバイスをリセットして再アクティブ化する](azure-stack-edge-reset-reactivate-device.md)」を参照してください。

## <a name="azure-edge-hardware-center-ordering-and-management-resource"></a>Azure Edge Hardware Center の注文と管理リソース 

新しい Azure Edge Hardware Center サービス (プレビュー) が使用できるようになり、Azure Stack Edge リソースを作成および管理できるようになりました。 東南アジア リージョンで注文する場合、データがシンガポール内にのみ存在し、レプリケートされないようにするオプションを選択できます。 

リージョン全体が停止した場合、注文リソースにアクセスすることはできなくなります。 リソースを返却、キャンセル、または削除することはできません。 注文ステータスの更新を要求した場合、またはサービスの停止中にデバイスの返却をすぐに開始する必要がある場合、Microsoft サポートがそれらの要求を処理します。

詳細な手順については、[Azure Edge Hardware Center での注文の作成](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)に関する記事を参照してください。


<!--## Azure Stack Edge telemetry

As Azure Stack Edge is a first-party Microsoft device, the telemetry from the device is automatically collected (without the user consent) and sent to Microsoft. This telemetry is stored in a common central location. This gathered telemetry provides valuable insights into enterprise deployments of Azure Stack Edge. This telemetry is also used for security, health, quality, and performance analysis.

- Microsoft collects telemetry for the infrastructure VMs (for example, Kubernetes master VM and Kubernetes worker VM) deployed on your Azure Stack Edge device and hosts. Telemetry is also gathered for other services that run on Azure Stack Edge device (for example, local Azure Resource Manager, Kubernetes dashboard). 
- The telemetry data is encrypted-in-transit as well at rest.
- Raw telemetry data sent to Microsoft is retained for 90 days. Aggregated data is retained for longer.
- For all the containerized workloads (deployed via IoT Edge and Kubernetes) and VM workloads, the application data is considered as the customer data. This data can only be accessed by the customer unless it pertains to the underlying infrastructure. 

For more information, see [Use the Kubernetes dashboard to monitor the Kubernetes cluster health on your Azure Stack Edge Pro device](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).-->

## <a name="azure-stack-edge-dependent-services"></a>Azure Stack Edge 依存サービス

Azure Arc 対応 Kubernetes、Azure IoT Hub および Azure IoT Edge、Azure Key Vault は、Azure Stack Edge と統合するサービスです。

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 

Azure Arc 対応 Kubernetes は、Azure Stack Edge のアドオンとして使用できます。 シンガポール (東南アジア) の場合、Azure Arc データはシンガポール内にのみ存在し、香港ではレプリケートされません。 <!--If there is a region-wide outage, the service is not resilient.-->

<!--For all other regions, Azure Arc supports Azure Regional Pair and is resilient to any region-wide outages.--> 
<!--For more information, see [Data residency and resiliency for Azure Arc-enabled Kubernetes clusters]().-->


### <a name="azure-iot"></a>Azure IoT

Azure IoT は、Azure Stack Edge のアドオンとして使用できます。 シンガポール (東南アジア) の場合、Azure IoT により、ペア リージョンが使用され、データは香港にレプリケートされます。 これは、Azure IoT がリージョン全体の停止に対して回復性があることを意味します。 

<!--For more information, see [Data residency and resiliency for Azure IoT]().-->


<!--### Azure Key Vault

Azure Key Vault currently uses Azure Regional Pair for region outage resiliency. For new Azure Key Vault resources, an option is now available that can be enabled at the subscription level. When enabled, if your service is deployed in Singapore (Southeast Asia), you can control the data replication to Hong Kong. 

If you choose to store and process the data only in Singapore region, then the service will not be resilient to region-wide outages. -->
<!--For more information, see [Data residency and resiliency for Azure Key Vault]().-->

## <a name="next-steps"></a>次のステップ

- [Azure でのデータ所在地](https://azure.microsoft.com/global-infrastructure/data-residency/)について詳細を確認する。