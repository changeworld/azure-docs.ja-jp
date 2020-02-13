---
title: Azure VMware Solutions (AVS) - AVS ノードのクォータ
description: AVS ノードのクォータ制限とクォータの増加を要求する方法について説明する
author: sharaths-cs
ms.author: dikamath
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fa1b056c8c96fb09def63ca1cd696fc2da5e9bed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019640"
---
# <a name="avs-node-quota-limits"></a>AVS ノードのクォータ制限

AVS サービスのサブスクリプションが有効になったときに、購入可能な既定の数量は 4 ノードです。 Azure portal から、すべての[ノードの種類](cloudsimple-node.md)を購入できます。 AVS プライベート クラウドを作成するには、少なくとも同じ SKU の 3 つのノードが必要です。 ノードを購入済みの場合、追加のノードを購入しようとするとエラーが表示される場合があります。

## <a name="quota-increase"></a>クォータの増加

サポート リクエストを送信することで、ノード クォータを増やすことができます。 サービス運用チームが要求を評価し、お客様と連携してノード クォータを増やします。 新しいチケットをオープンするときは、次のオプションを選択してください。

* [問題の種類]\:**テクニカル**
* サブスクリプション:**ご使用のサブスクリプション ID**
* サービスの種類:**VMware Solution by AVS**
* 問題の種類:**Dedicated Nodes quota (専用ノードのクォータ)**
* 問題のサブタイプ:**Increase quota of dedicated nodes (専用ノードのクォータを増やす)**
* 件名:**Quota increase (クォータの増加)**

サポート チケットの詳細で、必要なノード数とノードの SKU を指定します。

* ノードの SKU
* クォータの増加を要求している追加ノードの数

## <a name="next-steps"></a>次のステップ

* [ノードを購入する](create-nodes.md)
* [AVS ノードの概要](cloudsimple-node.md)
