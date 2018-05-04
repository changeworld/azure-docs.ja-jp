---
title: ネットワーク セキュリティ グループの作成 - Azure Portal | Microsoft Docs
description: Azure Portal を使用してネットワーク セキュリティ グループを作成してデプロイする方法を説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Azure Portal を使用してネットワーク セキュリティ グループを作成する

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

この記事では、リソース マネージャーのデプロイ モデルについて説明します。 [クラシック デプロイ モデルで NSG を作成](virtual-networks-create-nsg-classic-ps.md)することもできます。

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>NSG-FrontEnd NSG を作成する
上記のシナリオのように **NSG-FrontEnd** という名前の NSG を作成するには、次の手順を完了します。

1. ブラウザーで https://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. **[+ リソースの作成] >** > **[ネットワーク セキュリティ グループ]** を選択します。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. **[ネットワーク セキュリティ グループ]** で、**[追加]** を選択します。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. **[ネットワーク セキュリティ グループの作成]** で、*RG-NSG* リソース グループに *NSG FrontEnd* という名前の NSG を作成し、**[作成]** を選択します。
   
    ![Azure ポータル - NSG](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>既存の NSG に規則を作成する
Azure ポータルから既存の NSG に規則を作成するには、次の手順を完了します。

1. **[すべてのサービス]** を選択し、**[ネットワーク セキュリティ グループ]** を検索します。 **[ネットワーク セキュリティ グループ]** が表示されたら、それを選択します。
2. NSG の一覧で、 **[NSG-FrontEnd]** > **[受信セキュリティ規則]** を選択します。
   
    ![Azure ポータル - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. **[受信セキュリティ規則]** の一覧で **[追加]** をクリックします。
   
    ![Azure ポータル - 規則の追加](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. **[受信セキュリティ規則の追加]** で、ポート *80* の *TCP* 経由で任意のソースから任意の VM にアクセスできるようにする *web-rule* という名前の規則を *200* の優先度で作成して、**[OK]** を選択します。 これらの設定のほとんどは、既定値が設定されています。
   
    ![Azure ポータル - 規則の設定](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. 数秒後に、NSG の新しい規則が表示されます。
   
    ![Azure ポータル - 新しい規則](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. 手順 6 までを繰り返し、ポート *3389* の *TCP* 経由で任意のソースから任意の VM にアクセスできるようにする *rdp-rule* という名前の受信規則を *250* の優先度で作成します。

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>NSG を FrontEnd サブネットに関連付ける

1. **[すべてのサービス] >** を選択し、「**リソース グループ**」と入力します。**[リソース グループ]** が表示されたらそれを選択し、次に **[RG-NSG]** を選択します。
2. **[RG-NSG]** で、**[...]** > **[TestVNet]** を選択します。
   
    ![Azure ポータル - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. **[設定]** で、**[サブネット]** > **[FrontEnd]** > **[ネットワーク セキュリティ グループ]** > **[NSG-FrontEnd]** を選択します。
   
    ![Azure ポータル - サブネット設定](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. **[FrontEnd]** ブレードで、**[保存]** を選択します。
   
    ![Azure ポータル - サブネット設定](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>NSG-BackEnd NSG を作成する
**NSG-BackEnd** NSG を作成し、**BackEnd** サブネットに関連付けるには、次の手順を完了します。

1. [NSG-BackEnd](#Create-the-NSG-FrontEnd-NSG) という名前の NSG を作成するには、「*NSG-FrontEnd NSG を作成する*」の手順を繰り返します。
2. 次の表のように [受信](#Create-rules-in-an-existing-NSG) 規則を作成するには、「**既存の NSG に規則を作成する** 」の手順を繰り返します。
   
   | 受信規則 | 送信規則 |
   | --- | --- |
   | ![Azure ポータル - 受信規則](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure ポータル - 送信規則](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. **NSG-Backend** NSG を [BackEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) サブネットに関連付けるには、「**NSG を FrontEnd サブネットに関連付ける**」の手順を繰り返します。

## <a name="next-steps"></a>次の手順
* [既存の NSG の管理](manage-network-security-group.md)
* [ログを有効にします](virtual-network-nsg-manage-log.md) 。