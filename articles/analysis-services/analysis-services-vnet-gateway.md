---
title: Azure Virtual Network データソースに対してオンプレミス データ ゲートウェイを使用する | Microsoft Docs
description: VNET 上のデータソースに対してゲートウェイを使用するようにサーバーを構成する方法を説明します。
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 61c4463060016ecbd319b027d48c87f3a6a8ad48
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="use-gateway-for-datasources-on-an-azure-virtual-network-vnet"></a>Azure Virtual Network (VNET) 上のデータソースに対してゲートウェイを使用する

この記事では、データソースが [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md) 上に存在する場合に使用する **AlwaysUseGateway** サーバー プロパティについて説明します。

## <a name="server-access-to-vnet-datasources"></a>VNET データソースへのサーバー アクセス

データソースが VNET 経由でアクセスされる場合、Azure Analysis Services サーバーは、それらがオンプレミスであり、独自の環境内に存在しているかのように接続する必要があります。 **AlwaysUseGateway** サーバー プロパティを構成して、サーバーが[オンプレミス ゲートウェイ](analysis-services-gateway.md)経由ですべてのデータソース データにアクセスするように指定できます。 

> [!NOTE]
> このプロパティは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)がインストールされ、構成されている場合にのみ有効です。 ゲートウェイは、VNET 上に配置できます。

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway プロパティを構成する

1. [SSMS] > サーバー > **[プロパティ]** > **[全般]** で、**[Show Advanced (All) Properties]\(詳細な (すべての) プロパティの表示\)** を選択します。
2. **[ASPaaS\AlwaysUseGateway]** で、**[true]** を選択します。

    ![AlwaysUseGateway プロパティ](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>関連項目
[オンプレミス データソースへの接続](analysis-services-gateway.md)   
[オンプレミス データ ゲートウェイをインストールして構成する](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

