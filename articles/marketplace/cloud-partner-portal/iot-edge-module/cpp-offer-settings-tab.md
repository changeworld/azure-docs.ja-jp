---
title: Azure IoT Edge モジュール用のオファーの設定 | Azure Marketplace
description: IoT Edge モジュール用のオファーの設定を構成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d6d2306dfd44bad6e1f903c3cef3eeb4993f4cd9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148039"
---
# <a name="iot-edge-module-offer-settings-tab"></a>IoT Edge モジュールの [プランの設定] タブ

>[!Important]
>2020 年 4 月 13 日以降、IoT Edge モジュールのパートナー センターへの移行管理が開始されます。 移行後は、パートナー センターにてオファーを作成・管理することになります。 「[IoT Edge モジュール オファーを作成する](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)」の手順に従い、移行後のオファーを管理します。

**[IoT Edge モジュール] > [新しいプラン]** ページが開くと、 **[プランの設定]** タブにフォーカスが設定されています。

![IoT Edge モジュールの [新しいプラン] ページ](./media/iot-edge-module-offer-settings-tab.png)


## <a name="offer-identity-settings"></a>オファー ID の設定

**[Offer Identity]\(プラン ID\)** では、次の表で説明するフィールドの情報を提供する必要があります。 フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。 

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **オファー ID\***       | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品の URL と分析情報レポートに表示されます。 最大長は 50 文字で、小文字の英数字とダッシュ (-) を使用できます。 (ID の最後をダッシュにすることはできません。)**注:** このフィールドは、オファーの運用開始後に変更することはできません。 <br> たとえば、Contoso がオファー ID **sample-iot-edge-module** でオファーを発行した場合、そのオファーには Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-iot-edge-module?tab=Overview` が割り当てられます。 |
| **発行者\***     | Azure Marketplace での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 オファーの保存後にこの値を変更することはできません。 |
| **[名前]\***          | オファーの表示名です。 この名前は、Azure Marketplace と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を使用することをお勧めします。 それが製品の販売方法である場合を除き、組織の名前は含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が正確に同じであることを確認します。 |
|  |  |


**[保存]** を選択してオファーの設定を保存します。


## <a name="next-steps"></a>次のステップ

[[SKU]](./cpp-skus-tab.md) タブを使用して、オファーの SKU を構成します。
