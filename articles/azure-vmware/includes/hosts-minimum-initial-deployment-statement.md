---
title: 初期デプロイ時の最小ホスト数
description: 初期デプロイ時の最小ホスト数は 3 つです。
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 32ed19a8ac3535f407549bea87acb6b433004b02
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111429777"
---
<!-- Used in production-ready-deployment-steps.md and concepts-private-clouds-clusters.md -->

作成された各プライベート クラウドには、既定で 1 つの vSAN クラスターがあります。 クラスターは、追加したり、削除したり、スケーリングしたりすることができます。  クラスターあたりのホストの最小数と初期デプロイは 3 です。 

試用版クラスターは評価用に利用でき、プライベート クラウドあたり 3 台のホストに制限されます。

クラスターの構成や操作のその他ほとんどの側面を管理するには、vSphere と NSX-T Manager を使用します。 クラスター内の各ホストのローカル ストレージはすべて、vSAN の管理下にあります。

>[!TIP]
>最初のデプロイ数を超える数が必要な場合は、後でいつでもクラスターを拡張したり、クラスターを追加したりできます。
