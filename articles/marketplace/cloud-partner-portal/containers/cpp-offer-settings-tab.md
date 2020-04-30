---
title: Azure Containers イメージ用のオファーの設定 | Azure Marketplace
description: Azure コンテナーのオファーの設定を構成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: dsindona
ms.openlocfilehash: 61d9fa535d2bec0a52351ba6199183ea899a0e1c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146258"
---
# <a name="container-offer-settings-tab"></a>コンテナーの [プランの設定] タブ

> [!IMPORTANT]
> 2020 年 4 月 13 日以降、Azure Container オファーの管理のパートナー センターへの移行が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[Azure Container オファーを作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer)」の手順に従って、移行されたオファーを管理します。

**[コンテナー] > [新しいプラン]** ページが開くと、 **[プランの設定]** タブにフォーカスが設定されています。 

![プラン ID](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>オファー ID の設定

**[Offer Identity]\(プラン ID\)** では、次の表で説明するフィールドの情報を提供する必要があります。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。 

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **オファー ID\***       | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品の URL と分析情報レポートに表示されます。 最大長は 50 文字で、小文字の英数字とダッシュ (-) を使用できます。 (ID の最後をダッシュにすることはできません。)**注:** このフィールドは、オファーの運用開始後に変更することはできません。 <br> たとえば、Contoso がオファー ID **sample-container** でオファーを発行した場合、そのオファーには Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview` が割り当てられます。 |
| **発行元 ID\***     | Azure Marketplace での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 オファーの保存後にこの値を変更することはできません。 |
| **[名前]\***          | オファーの表示名です。 この名前は、Azure Marketplace と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用することをお勧めします。 それが製品の販売方法である場合を除き、組織の名前は含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が正確に同じであることを確認します。 |
|  |  |

**[保存]** を選択してオファーの設定を保存します。


## <a name="next-steps"></a>次のステップ

[[SKU]](./cpp-skus-tab.md) タブを使用して、オファーの SKU を構成します。
