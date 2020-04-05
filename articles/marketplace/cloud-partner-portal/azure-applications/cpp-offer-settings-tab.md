---
title: Azure アプリケーションの [オファーの設定] | Azure Marketplace
description: Azure アプリケーション オファー用にオファーの設定を構成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: d111471e909a091d8c887f13ad89d1da50cf72b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280118"
---
# <a name="azure-application-offer-settings-tab"></a>Azure アプリケーションの [プランの設定] タブ

この記事では、Azure アプリケーション用に [プランの設定] を構成する方法について説明します。

**[Azure Applications]\(Azure アプリケーション\) > [新しいプラン]** ページが開くと、 **[プランの設定]** タブにフォーカスが設定されています。フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

![[Offer Identity]\(プラン ID\) フォーム](./media/azureapp-offer-settings-tab.png)

## <a name="offer-identity-settings"></a>オファー ID の設定

**[Offer Identity]\(プラン ID\)** では、次の表で説明するフィールドの情報を提供する必要があります。  

|    フィールド         |       説明                                                            |
|  ---------       |     ---------------                                                          |
| **オファー ID\***       | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品の URL と分析情報レポートに表示されます。 最大長は 50 文字で、小文字の英数字とダッシュ (-) を使用できます。 (ID の最後をダッシュにすることはできません。)**注:** このフィールドは、オファーの運用開始後に変更することはできません。 <br> たとえば、Contoso がオファー ID **sample-container** でオファーを発行した場合、そのオファーには Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-container?tab=Overview` が割り当てられます。 |
| **発行元 ID\***     | Azure Marketplace での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 オファーの保存後にこの値を変更することはできません。 |
| **[名前]\***          | オファーの表示名です。 この名前は、Azure Marketplace と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用することをお勧めします。 それが製品の販売方法である場合を除き、組織の名前は含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が正確に同じであることを確認します。 |
|  |  |

**[保存]** を選択してオファーの設定を保存します。

## <a name="next-steps"></a>次のステップ

[[SKU]](./cpp-skus-tab.md) タブを使用して、オファーの SKU を構成します。
