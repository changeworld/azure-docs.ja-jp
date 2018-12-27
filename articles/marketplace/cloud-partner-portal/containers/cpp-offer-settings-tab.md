---
title: Azure コンテナー イメージ用のオファーの設定 | Microsoft Docs
description: Azure コンテナーのオファーの設定を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 9a669ebad674f938d11c8e372e6f586ca3c67f03
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "50980250"
---
# <a name="container-offer-settings-tab"></a>コンテナーの [プランの設定] タブ

**[コンテナー] > [新しいプラン]** ページが開くと、**[プランの設定]** タブにフォーカスが設定されています。 

![プラン ID](./media/containers-offer-settings.png)

## <a name="offer-identity-settings"></a>オファー ID の設定

**[Offer Identity]\(プラン ID\)** では、次の表で説明するフィールドの情報を提供する必要があります。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。 

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **[プラン ID]**       | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品の URL と分析情報レポートに表示されます。 最大長は 50 文字で、小文字の英数字とダッシュ (-) を使用できます。 (ID の最後をダッシュにすることはできません。)**注:** このフィールドは、オファーの運用開始後に変更することはできません。 <br> たとえば、Contoso がオファー ID **sample-container** でオファーを発行した場合、そのオファーには Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview` が割り当てられます。 |
| **[発行元 ID]**     | Azure Marketplace での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 オファーの保存後にこの値を変更することはできません。 |
| **名前**          | オファーの表示名です。 この名前は、Azure Marketplace と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用することをお勧めします。 それが製品の販売方法である場合を除き、組織の名前は含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が正確に同じであることを確認します。 |
|  |  |

**[保存]** を選択してオファーの設定を保存します。

## <a name="next-steps"></a>次の手順

[[SKU]](./cpp-skus-tab.md) タブを使用して、オファーの SKU を構成します。
