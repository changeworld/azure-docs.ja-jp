---
title: GPU 搭載の Microsoft Azure Stack Edge Pro に対するリージョンの選択 | Microsoft Docs
description: GPU 搭載の Azure Stack Edge Pro、Azure Stack Edge Pro R、および Azure Stack Edge Mini R の Azure Stack Edge サービス、データ ストレージ、およびデバイスに対するリージョンの選択について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: alkohli
ms.openlocfilehash: 7b585c61dd46339be71e66be66b402a0e107c194
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429835"
---
# <a name="choosing-a-region-for-azure-stack-edge"></a>Azure Stack Edge に対するリージョンの選択

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

この記事では、Azure Stack Edge デバイスの Azure Stack Edge サービス、データ ストレージ、デバイスの出荷に対して利用可能なリージョンについて説明します。また、選択したリージョンがサービス、パフォーマンス、および請求にどのように影響するかについても説明します。


## <a name="overview"></a>概要

Azure データセンターは、お客様のパフォーマンスの要望、要件、およびデータの場所に関する希望を満たすために世界中の複数の地理的地域で運用されています。 Azure の geo とは、少なくとも 1 つの Azure リージョンを含む、世界の定義済みの地域です。 Azure リージョンは、geo に含まれる領域で、1 つ以上のデータセンターが含まれます。

Azure リージョンの選択はとても重要であり、リージョンの選択は、データの保存地域と主権、サービスの可用性、パフォーマンス、コスト、冗長性などの要因に影響を受けます。 リージョンの詳細な選択方法については、[適切な Azure リージョンの選択](https://azure.microsoft.com/overview/datacenters/how-to-choose/)に関するページを参照してください。

Azure Stack Edge Pro GPU デバイスの場合、リージョンの選択は次の要因によって決まります。

- Azure Stack Edge サービスが利用可能なリージョン。
- 最適なパフォーマンスを得るために、Azure Stack Edge データを格納するストレージ アカウントを配置すべきリージョン。
- Azure Stack Edge デバイスを出荷できる国または地域。


## <a name="region-availability-for-the-service"></a>サービスを利用可能なリージョン

Azure Stack Edge サービスは現在、米国東部、西ヨーロッパ、東南アジアの各パブリック リージョンでサポートされています。 **これら 3 つのリージョンは、世界中の地理的な場所に対応しています。**

サービスのリージョンは、Azure Stack Edge 管理リソースに割り当てられている国または地域です。 管理リソースでは、Azure Stack Edge サービスを使用して、Azure Stack Edge デバイスがアクティブ化およびデプロイされ、返されます。

Azure Stack Edge サービスでは、デバイスの正常性 (デバイスの問題、エラー、アラート、デバイスが "起動中" かどうか) の監視も行われます。 また、このサービスにより、課金の使用量と消費量 (デバイスのコントロール プレーン情報) が監視されます。 管理リソースに割り当てられているリージョンは、請求が発生するリージョンです。

デバイスをアクティブ化するには、Azure Stack Edge サービスに接続する必要があります。 サービスをこれ以上使用しない場合は、デバイスを切断モードに切り替えることができます。 

データ フローは Azure Stack Edge サービスを通過しません。 データ フローは、デバイスと、顧客のデータ元のリージョンにデプロイされているストレージ アカウントとの間を通過します。 

一般的に、デバイスをデプロイする地域に最も近い場所を選択します。 ただし、デバイスとサービスを別の場所にデプロイすることもできます。

## <a name="region-availability-for-data-storage"></a>データ ストレージを利用可能なリージョン

Azure Stack Edge データは Azure ストレージ アカウントに格納されており、Azure ストレージ アカウントはすべての Azure リージョンで使用できます。 Azure ストレージ アカウントを作成するときに、ストレージ アカウントのプライマリの場所を選択します。 この選択により、データが存在するリージョンが決まります。

[デバイスで共有を作成する](azure-stack-edge-gpu-deploy-add-shares.md#add-a-share)ときに、ストレージ アカウントを選択します。 Azure Stack Edge サービスと Azure ストレージは 2 つの別々の場所に配置できます。

一般的に、ストレージ アカウントのサービスに最も近いリージョンを選択します。 ただし、最も近い Microsoft Azure リージョンが、実際には待機時間が最短のリージョンではないことがあります。 ネットワーク サービスのパフォーマンスに影響し、結果としてデバイスのパフォーマンスにも影響するのが待機時間です。 そのため、別の地域のストレージ アカウントを選択する場合、使用するサービスと、ストレージ アカウントに関連付けられているリージョン間の待機時間を把握することが重要です。

## <a name="region-of-device"></a>デバイスのリージョン

Azure Stack Edge モデルを利用可能な国と地理的地域を確認するには、製品の概要を参照してください。

- [GPU 搭載の Azure Stack Edge Pro を利用可能なリージョン](azure-stack-edge-gpu-overview.md#region-availability)
- [Azure Stack Edge Pro R を利用可能なリージョン](azure-stack-edge-pro-r-overview.md#region-availability)
- [Azure Stack Edge Mini R を利用可能なリージョン](azure-stack-edge-mini-r-overview.md#region-availability)

Microsoft は、これらの地理的地域に対して物理ハードウェアを出荷し、Azure Stack Edge のハードウェア予備部品を交換することができます。

> [!IMPORTANT]
> Azure Stack Edge がサポートされていないリージョンに Azure Stack Edge 物理デバイスを配置しないでください。 Microsoft は、Azure Stack Edge がサポートされていない国またはリージョンに交換部品を出荷しません。


## <a name="next-steps"></a>次の手順

* [さまざまな Azure Stack Edge モデルの価格](https://azure.microsoft.com/pricing/details/azure-stack/edge/)について学習します。
* [Azure Stack Edge Pro デバイスのデプロイを準備します](azure-stack-edge-gpu-deploy-prep.md)。
