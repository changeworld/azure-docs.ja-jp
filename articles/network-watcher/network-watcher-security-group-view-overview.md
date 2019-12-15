---
title: Azure Network Watcher のセキュリティ グループ ビューの概要 | Microsoft Docs
description: このページでは、Network Watcher のセキュリティ ビュー機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: kumud
ms.openlocfilehash: 856135c6cf903b1530f0ae922edddd09e5a1b0d3
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539211"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Azure Network Watcher の有効なセキュリティ規則ビューの概要

ネットワーク セキュリティ グループは、サブネット レベルまたは NIC レベルで関連付けられています。 サブネット レベルに関連付けた場合、サブネット内のすべての VM インスタンスに適用されます。 有効なセキュリティ規則ビューは、仮想マシン用に NIC レベルまたはサブネット レベルで関連付けられた、すべての構成済み NSG と規則を返して、構成に対する洞察を提供します。 さらに、有効なセキュリティ規則を VM の各 NIC に返します。 有効なセキュリティ規則ビューを使用することで、開いているポートなどのネットワークの脆弱性について VM を評価できます。 また、ネットワーク セキュリティ グループが、[構成されたセキュリティ規則と承認されたセキュリティ規則の比較](network-watcher-nsg-auditing-powershell.md)に基づいて期待どおりに機能しているかどうかを検証することもできます。

さらに用途を広げて、セキュリティのコンプライアンスと監査に活用することもできます。 組織のセキュリティ ガバナンスのモデルとして、セキュリティ規則の規範的なセットを定義できます。 定期的なコンプライアンス監査は、規範的な規則とネットワーク内の各 VM の有効な規則とを比較することによって、プログラムによる方法で実装できます。

ポータルでは、ネットワーク インターフェイスごとに規則が表示され、インバウンドかアウトバウンドかによってグループ化されます。 このため、仮想マシンに適用される規則が分かりやすく表示されています。 どのタブからでも [ダウンロード] ボタンを使用でき、すべてのセキュリティ規則を CSV ファイルで簡単にダウンロードできます。

![セキュリティ グループ ビュー][1]

規則を選択して新しいブレードを開くと、ネットワーク セキュリティ グループ、送信元、送信先のプレフィックスが表示されます。 このブレードからは、ネットワーク セキュリティ グループのリソースに直接移動できます。

![ドリルダウン][2]

### <a name="next-steps"></a>次の手順

ネットワーク セキュリティ グループを監査する方法については、[PowerShell を使用したネットワーク セキュリティ グループの監査の設定](network-watcher-nsg-auditing-powershell.md)に関する記事をご覧ください。

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









