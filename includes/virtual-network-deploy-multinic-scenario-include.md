---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 5fc9412713bc57c3fd7753e133b2587ea7a68938
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>シナリオ
ここでは、VM で複数の NIC を使用するデプロイメントについて、具体的なシナリオを使って説明します。 このシナリオでは、Azure でホストされる 2 層の IaaS ワークロードを使用します。 各層は、仮想ネットワーク (VNet) の専用サブネットにデプロイされています。 フロントエンド層は複数の Web サーバーで構成されていて、Web サーバーは高可用性のために 1 つのロード バランサー セットにグループ化されています。 バックエンド層は、複数のデータベース サーバーで構成されています。 データベース サーバーは、それぞれ 2 つの NIC を使用してデプロイされています。1 つはデータベース アクセス用、もう 1 つは管理用です。 また、各サブネットおよびデプロイメント内の NIC に対して許可されるトラフィックを制御するためのネットワーク セキュリティ グループ (NSG) も含まれます。 次の図に、このシナリオの基本的なアーキテクチャを示します。  

![MultiNIC シナリオ](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

