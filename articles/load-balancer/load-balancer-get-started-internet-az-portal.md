---
title: Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する | Microsoft Docs
description: Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 10a264609469245d4743886b58730304da3df7bb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="register-for-availability-zones-preview"></a>可用性ゾーン (プレビュー) の登録
 
可用性ゾーンはプレビュー段階にあり、開発とテストのシナリオのために準備されています。 選択された Azure リソース、リージョン、および VM サイズ ファミリに対するサポートが使用できます。 使用を開始する方法、および可用性ゾーンを試行する場合にどの Azure リソース、リージョン、および VM サイズ ファミリを使用できるかの詳細については、「[Overview of Availability Zones (可用性ゾーンの概要)](https://docs.microsoft.com/azure/availability-zones/az-overview)」を参照してください。 サポートについては、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) でアクセスするか、または [Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

## <a name="log-in-to-azure"></a>Azure にログインする 

Azure Portal (https://portal.azure.com) にログインします。

## <a name="create-a-zone-redundant-load-balancer"></a>ゾーン冗長ロード バランサーの作成

1. ブラウザーから Azure Portal ([http://portal.azure.com](http://portal.azure.com)) に移動し、Azure アカウントでログインします。
2. 画面の左上で、**[リソースの作成]** > **[ネットワーク]** > **[Load Balancer]** を選択します。
3. **[ロード バランサーの作成] の **[名前]** に「**myPublicLB**」と入力します。
4. **[種類]** から **[パブリック]** を選択します。
5. [SKU] から **[Standard (プレビュー)]** を選択します。
6. **[パブリック IP アドレス]**、**[新規作成]** の順にクリックします。**[パブリック IP アドレスの作成]** ページの [名前] に「**myPublicIPStandard**」と入力し、**[可用性ゾーン (プレビュー)]** で **[ゾーン冗長]** を選択します。
7. **[場所]** から **[米国東部 2]** を選択し、**[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。

    ![Azure Portal を使用してゾーン冗長 Load Balancer Standard を作成する](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>次の手順
- [可用性ゾーン内にパブリック IP を作成する](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)方法を確認する



