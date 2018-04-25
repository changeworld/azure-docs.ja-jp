---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
## <a name="scenario"></a>シナリオ
ここでは、VM で複数の NIC を使用するデプロイメントについて、具体的なシナリオを使って説明します。 このシナリオでは、Azure でホストされる 2 層の IaaS ワークロードを使用します。 各層は、仮想ネットワーク (VNet) の専用サブネットにデプロイされています。 フロントエンド層は複数の Web サーバーで構成されていて、Web サーバーは高可用性のために 1 つのロード バランサー セットにグループ化されています。 バックエンド層は、複数のデータベース サーバーで構成されています。 データベース サーバーは、それぞれ 2 つの NIC を使用してデプロイされています。1 つはデータベース アクセス用、もう 1 つは管理用です。 また、各サブネットおよびデプロイメント内の NIC に対して許可されるトラフィックを制御するためのネットワーク セキュリティ グループ (NSG) も含まれます。 次の図に、このシナリオの基本的なアーキテクチャを示します。

![MultiNIC シナリオ](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

