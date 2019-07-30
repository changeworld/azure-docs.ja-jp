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
ms.openlocfilehash: fd1416befb74a7299136ea497eccc8a06b7f0f6a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164862"
---
# <a name="cloudsimple-node-quota-limits"></a>CloudSimple ノードのクォータ制限

CloudSimple サービスのサブスクリプションが有効になったときに、プロビジョニング可能な既定数量は 4 ノードです。  Azure portal から任意の[ノード タイプ](cloudsimple-node.md)をプロビジョニングできます。  プライベート クラウドを作成するには、少なくとも同じ SKU の 3 つのノードが必要です。  ノードをプロビジョニング済みの場合、追加のノードをプロビジョニングしようとするとエラーが表示される場合があります。

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

* [ノードをプロビジョニングする](create-nodes.md)
* [CloudSimple ノードの概要](cloudsimple-node.md)