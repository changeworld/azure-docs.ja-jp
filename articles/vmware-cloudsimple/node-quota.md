---
title: Azure VMware Solution by CloudSimple - CloudSimple ノード クォータ
description: CloudSimple ノードのクォータ制限とクォータの増加を要求する方法について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 26a5876971749a1a63447ef813219dad8485781c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157947"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple ノードのクォータ制限

CloudSimple サービスのサブスクリプションが有効になったときに、購入可能な既定数量は 4 ノードです。  Azure portal から任意の[ノード タイプ](cloudsimple-node.md)を購入できます。  プライベート クラウドを作成するには、少なくとも同じ SKU の 3 つのノードが必要です。  ノードを購入済みの場合、追加のノードを購入しようとするとエラーが表示される場合があります。

## <a name="quota-increase"></a>クォータの増加

サポート リクエストを送信することで、ノード クォータを増やすことができます。 サービス運用チームが要求を評価し、お客様と連携してノード クォータを増やします。  新しいチケットをオープンするときは、次のオプションを選択してください。

* [問題の種類]: **テクニカル**
* サブスクリプション:**ご使用のサブスクリプション ID**
* サービスの種類:**VMware Solution by CloudSimple**
* 問題の種類:**Dedicated Nodes quota (専用ノードのクォータ)**
* 問題のサブタイプ:**Increase quota of dedicated nodes (専用ノードのクォータを増やす)**
* 件名:**Quota increase (クォータの増加)**

サポート チケットの詳細で、必要なノード数とノードの SKU を指定します。

[ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) で Microsoft アカウントの担当者に連絡してサブスクリプションのノードのクォータを増やすこともできます。  次の項目を指定する必要があります。

* サブスクリプション ID
* ノードの SKU
* クォータの増加を要求している追加ノードの数

## <a name="next-steps"></a>次の手順

* [ノードを購入する](create-nodes.md)
* [CloudSimple ノードの概要](cloudsimple-node.md)