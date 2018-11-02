---
title: Azure Marketplace 向け Cloud パートナー ポータルでの仮想マシンの [プランの設定] タブ | Microsoft Docs
description: Azure Marketplace の VM オファーの作成で使用する [プランの設定] タブについて説明します。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 43c830bea57a4c6f3b6c56552dbcacaccc75d54e
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639081"
---
# <a name="virtual-machine-offer-settings-tab"></a>仮想マシンの [プランの設定] タブ

仮想マシンに対する **[新しいプラン]** ページの最初のタブは **[プランの設定]** という名前です。  フィールド名にアスタリスク (*) が付加されていると、それが必須であることを示します。 

![仮想マシンの [新しいプラン] ページ](./media/publishvm_004.png)

**[プランの設定]** タブでは、次の必須フィールドを指定する必要があります。

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
| **[プラン ID]**       | オファーの (発行元プロファイル内での) 一意識別子です。 この ID は、製品 URL、Azure Resource Manager テンプレート、および課金レポートに表示されます。 最大長は 50 文字で、小文字の英数字とハイフン (-) だけを使用でき、ダッシュで終わることはできません。 このフィールドは、オファーの運用開始後に変更することはできません。 <br> たとえば、Contoso がオファー ID **sample-vm** でオファーを発行した場合、そのオファーには Azure Marketplace URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` が割り当てられます。 |
| **発行元**     | Azure Marketplace での組織の一意識別子です。 すべてのオファリングには、発行元 ID が関連付けられる必要があります。 この値は、オファーを保存した後は変更できません。 |
| **名前**          | オファーの表示名です。 この名前は、Azure Marketplace と Cloud パートナー ポータルに表示されます。 最大で 50 文字の長さにできます。 製品の覚えやすいブランド名を含めることをお勧めします。 販売方法である場合を除き、組織名はここに含めないでください。 他の Web サイトやパブリケーションでこのオファーを販売している場合は、すべてのパブリケーションで名前が正確に同じであることを確認します。 |
|  |  |
 
**[保存]** をクリックしてここまでの作業を保存します。 次のタブでは、[SKU](./cpp-skus-tab.md) をオファーに追加します。
