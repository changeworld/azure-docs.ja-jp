---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/17/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d9c8a0e6a3bd6d79a11ee0d0dab0500a209e5571
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943951"
---
サブネットまたは仮想マシン (VM) ネットワーク インターフェイスでネットワーク フィルターを作成して、Azure で VM へのポートを開くか、エンドポイントを作成します。 着信および発信の両方のトラフィックを制御するこれらのフィルターを、トラフィックを受信するリソースに接続されているネットワーク セキュリティ グループに配置します。

ポート 80 での Web トラフィックの一般的な例を使用して説明します。 標準の TCP ポート 80 の Web 要求を処理するように構成されている仮想マシンがある場合 (適切なサービスを開始し、仮想マシン上の OS ファイアウォール規則を開いてください) の手順は、以下のとおりです。

1. ネットワーク セキュリティ グループを作成します。
2. 以下によってトラフィックを許可する受信の規則を作成します。
   * 宛先ポート範囲 "80"
   * 発信元ポート範囲"*"(任意のポートを許可)
   * 65,500 より小さな優先順位値 (既定の包括的な受信拒否規則より優先されるように設定)
3. ネットワーク セキュリティ グループと仮想マシンのネットワーク インターフェイスまたはサブネットを関連付けます。

ネットワーク セキュリティ グループと規則を使用して、複雑なネットワーク構成を作成して環境を保護できます。 この例では、HTTP トラフィックまたはリモート管理を許可する 1 つまたは 2 つのルールのみを使用します。 詳細については、後述の[詳細情報](#more-information-on-network-security-groups)セクションまたは[ネットワーク セキュリティ グループ](../articles/virtual-network/security-overview.md)に関する記事を参照してください。

