---
title: VNet データソース用に Azure Analysis Services を構成する | Microsoft Docs
description: Azure Virtual Network (VNet) 上のデータソースに対してゲートウェイを使用するように Azure Analysis Services サーバーを構成する方法について説明します。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572277"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Azure Virtual Network (VNet) 上のデータソースに対してゲートウェイを使用する

この記事では、データソースが **Azure Virtual Network (VNet)** 上に存在する場合に使用する Azure Analysis Services の [AlwaysUseGateway](../virtual-network/virtual-networks-overview.md) サーバー プロパティについて説明します。

## <a name="server-access-to-vnet-data-sources"></a>VNet データソースへのサーバー アクセス

データソースが VNet 経由でアクセスされる場合、Azure Analysis Services サーバーは、それらがオンプレミスであり、独自の環境内に存在しているかのように接続する必要があります。 **AlwaysUseGateway** サーバー プロパティを構成して、サーバーが[オンプレミス ゲートウェイ](analysis-services-gateway.md)経由ですべてのデータ ソースにアクセスするように指定できます。 

Azure SQL Database Managed Instance データ ソースは、プライベート IP アドレスを使用して Azure VNet 内で実行されます。 インスタンスでパブリック エンドポイントが有効な場合、ゲートウェイは必要ありません。 パブリック エンドポイントが有効な場合は、オンプレミス データ ゲートウェイが必要であり、AlwaysUseGateway プロパティを true に設定する必要があります。

> [!NOTE]
> このプロパティは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)がインストールされ、構成されている場合にのみ有効です。 ゲートウェイは、VNet 上に配置できます。

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway プロパティを構成する

1. [SSMS] > サーバー > **[プロパティ]**  >  **[全般]** で、 **[Show Advanced (All) Properties]\(詳細な (すべての) プロパティの表示\)** を選択します。
2. **[ASPaaS\AlwaysUseGateway]** で、 **[true]** を選択します。

    ![AlwaysUseGateway プロパティ](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>参照
[オンプレミス データソースへの接続](analysis-services-gateway.md)   
[オンプレミス データ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

