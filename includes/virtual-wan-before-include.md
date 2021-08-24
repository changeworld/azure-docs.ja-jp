---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ef4edb99ab2d6d4a25f2151c5b63c1c31ff91ef4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724971"
---
* Azure サブスクリプションがある。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* 接続先の仮想ネットワークが用意されていること。 

   * オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 
   * Azure portal で仮想ネットワークを作成するには、[クイックスタート](../articles/virtual-network/quick-create-portal.md)記事を参照してください。

* 仮想ネットワークにまだ仮想ネットワーク ゲートウェイが存在しないこと。 

   * 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が既に存在する場合は、すべてのゲートウェイを削除してから先に進む必要があります。 
   * この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイにのみ接続されている必要があります。

* 仮想ハブは、仮想 WAN によって作成および使用される仮想ネットワークです。 リージョン内の Virtual WAN ネットワークの核となります。 

   * 仮想ハブ リージョンの IP アドレス範囲を取得します。 
   * ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 
   * このアドレス範囲が、接続するオンプレミスのアドレス範囲と重複することはできません。 
   * オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。
