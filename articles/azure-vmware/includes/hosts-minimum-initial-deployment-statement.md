---
title: 初期デプロイ時の最小ホスト数
description: 初期デプロイ時の最小ホスト数は 3 つです。
ms.topic: include
ms.date: 09/29/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 76b24de732f9668580d5a42f4279d9882a9ae702
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638228"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

作成された各プライベート クラウドには、既定で 1 つの vSAN クラスターがあります。 クラスターは、追加したり、削除したり、スケーリングしたりすることができます。  クラスターあたりのホストの最小数と初期デプロイは 3 です。 

クラスターの構成や操作のその他ほとんどの側面を管理するには、vSphere と NSX-T Manager を使用します。 クラスター内の各ホストのローカル ストレージはすべて、vSAN の管理下にあります。

>[!TIP]
>最初のデプロイ数を超える数が必要な場合は、後でいつでもクラスターを拡張したり、クラスターを追加したりできます。
