---
title: Azure での既定の送信アクセス
titleSuffix: Azure Virtual Network
description: Azure での既定の送信アクセスについて説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 7bf14494052cb425a8bdbe3650cf75a6b7aaed5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367743"
---
# <a name="default-outbound-access-in-azure"></a>Azure での既定の送信アクセス

Azure では、明示的な送信接続が定義されていない仮想ネットワークで作成された仮想マシンには、既定の送信パブリック IP アドレスが割り当てられます。 この IP アドレスにより、リソースからインターネットへの送信接続が可能になります。 このアクセスは、既定の送信アクセスと呼ばれます。 

明示的な送信接続の例として、以下の仮想マシンがあります。

* NAT Gateway に関連付けられているサブネット内に作成されている。
* アウトバウンド規則が定義されている標準ロード バランサーのバックエンド プール内。
* 基本的なパブリック ロード バランサーのバックエンド プール内。
* パブリック IP アドレスが明示的に関連付けられている仮想マシン。

## <a name="how-is-default-outbound-access-provided"></a>既定の送信アクセスの提供方法

アクセスに使用されるパブリック IPv4 アドレスは、既定の送信アクセス IP と呼ばれます。 この IP は暗黙的であり、Microsoft に属しています。 この IP アドレスは変更される可能性があるため、実稼働ワークロードでこれに依存することはお勧めしません。

## <a name="when-is-default-outbound-access-provided"></a>既定の送信アクセスが提供されるタイミング

Azure に仮想マシンをデプロイし、それが明示的な送信接続を持っていなかった場合に、既定の送信アクセス IP が割り当てられます。
## <a name="why-is-disabling-default-outbound-access-recommended"></a>既定の送信アクセスを無効にすることが推奨される理由

* 既定でのセキュリティ保護
    
    * ゼロ トラスト ネットワーク セキュリティ原則を使用して、既定でインターネットへの仮想ネットワークを開くことはお勧めしません。

* 明示的と暗黙的

    * 仮想ネットワーク内のリソースにアクセスを許可する場合は、暗黙的ではなく明示的な接続方法を使用することをお勧めします。

* IP アドレスの損失

    * 既定の送信アクセス IP は、お客様が所有しているわけではありません。 IP は変更されることがあります。  この IP に依存すると、将来問題が発生する可能性があります。

## <a name="how-can-i-disable-default-outbound-access"></a>既定の送信アクセスを無効にする方法

既定の送信アクセスをオフにする方法は複数あります。

1.  明示的な送信接続の方法を追加する

    * 仮想マシンのサブネットに NAT ゲートウェイを関連付ける。

    * 構成されたアウトバウンド規則に標準ロード バランサーを関連付ける。

    * 仮想マシンのネットワーク インターフェイスにパブリック IP を関連付ける。

2.  仮想マシン スケール セットのフレキシブル オーケストレーション モードを使用する。

    * フレキシブル スケール セットは、既定でセキュリティによって保護されています。 フレキシブル スケール セットを使用して作成されたインスタンスには、既定の送信アクセス IP は関連付けされません。 詳細については、[仮想マシン スケール セットのフレキシブル オーケストレーション モード](../../virtual-machines/flexible-virtual-machine-scale-sets.md)に関するページをご覧ください。

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>送信アクセスが必要な場合に、推奨される方法

NAT ゲートウェイは、明示的な送信接続を持たせるために推奨される方法です。 ファイアウォールの使用によっても、このアクセスを提供できます。

## <a name="limitations"></a>制限事項

* 接続には、Windows Update が必要となる場合があります。
* 既定の送信アクセス IP では、フラグメント化されたパケットはサポートされていません。 

## <a name="next-steps"></a>次の手順

Azure および Azure Virtual Network NAT (NAT ゲートウェイ) での送信接続の詳細については、以下をご覧ください。

* [アウトバウンド接続での送信元ネットワーク アドレス変換 (SNAT)](../../load-balancer/load-balancer-outbound-connections.md)。

* [Azure Virtual Network NAT とは](../nat-gateway/nat-overview.md)
