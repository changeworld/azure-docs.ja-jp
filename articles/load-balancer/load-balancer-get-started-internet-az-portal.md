---
title: "Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する | Microsoft Docs"
description: "Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する方法について説明します。"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Azure Portal でゾーン冗長パブリック IP アドレス フロントエンドを使用してパブリック Load Balancer Standard を作成する

この記事では、パブリック IP Standard アドレスでゾーン冗長フロントエンドを使用してパブリック [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) を作成する手順について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>可用性ゾーン、Load Balancer Standard、パブリック IP Standard プレビューの登録
 
>[!NOTE]
[Load Balancer Standard SKU](https://aka.ms/azureloadbalancerstandard) は現在プレビュー段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 運用サービスには、一般公開の [Load Balancer Basic SKU](load-balancer-overview.md) を使用してください。 

> [!NOTE]
> 可用性ゾーンはプレビュー段階であり、開発とテストのシナリオで使用できます。 一部の Azure リソース、リージョン、VM サイズ ファミリについては、サポートを利用できます。 利用を開始する方法のほか、可用性ゾーンを試すことができる Azure リソース、リージョン、VM サイズ ファミリについては、[可用性ゾーンの概要](https://docs.microsoft.com/azure/availability-zones/az-overview)に関するページを参照してください。 サポートが必要な場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) を参照するか、[Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json)ことができます。  

Load Balancer のフロントエンド パブリック IP アドレスのゾーンまたはゾーン冗長オプションを選択する前に、まず[可用性ゾーン プレビューの登録に関するページ](https://docs.microsoft.com/azure/availability-zones/az-overview)の手順を完了する必要があります。

## <a name="log-in-to-azure"></a>Azure へのログイン 

Azure Portal (https://portal.azure.com) にログインします。

## <a name="create-a-zone-redundant-load-balancer"></a>ゾーン冗長ロード バランサーの作成

1. ブラウザーから Azure ポータル ( [http://portal.azure.com](http://portal.azure.com) ) に移動し、Azure アカウントでログインします。
2. 画面の左上で、**[新規]** > **[ネットワーク]** > **[Load Balancer]** の順に選択します。
3. **[ロード バランサーの作成] の **[名前]** に「**myPublicLB**」と入力します。
4. **[種類]** から **[パブリック]** を選択します。
5. [SKU] から **[Standard (プレビュー)]** を選択します。
6. **[パブリック IP アドレス]**、**[新規作成]** の順にクリックします。**[パブリック IP アドレスの作成]** ページの [名前] に「**myPublicIPStandard**」と入力し、**[可用性ゾーン (プレビュー)]** で **[ゾーン冗長]** を選択します。
7. **[場所]** から **[米国東部 2]** を選択し、**[OK]** をクリックします。 ロード バランサーのデプロイが開始されます。デプロイが正常に完了するまでに数分かかります。

    ![Azure Portal を使用してゾーン冗長 Load Balancer Standard を作成する](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>次のステップ
- [可用性ゾーン内にパブリック IP を作成する](../virtual-network/create-public-ip-availability-zone-portal.md)方法について学びます




