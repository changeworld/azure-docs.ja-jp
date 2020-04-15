---
title: インクルード ファイル
description: インクルード ファイル
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/07/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: fb95301d4faa958a677c42bc8092ac52a7c95c8b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80813720"
---
| リソース | 制限 |
| --- | --- |
| データ スループット |30 Gbps<sup>1</sup> |
|ルール|10,000。 すべての規則の種類が結合されます。|
|最大 DNAT ルール|298<br>規則のプロトコルが TCP と UDP の両方に対して構成されている場合は、2 つの規則としてカウントされます。|
|AzureFirewallSubnet の最小サイズ |/26|
|ネットワークおよびアプリケーション ルールのポート範囲|1 - 65535|
|パブリック IP アドレス|最大 100 (現在、SNAT ポートは、最初の 5 つのパブリック IP アドレスに対してのみ追加されます。)|
|IP グループの IP アドレス|50 個以下の IP グループ: ファイアウォール インスタンスごとにそれぞれ最大 5000 個の個別の IP アドレス。<br>51 - 100 個の IP グループ: ファイアウォールごとにそれぞれ 500 個の個別の IP アドレス。<br><br>詳細については、「[Azure Firewall での IP グループ (プレビュー)](../articles/firewall/ip-groups.md#ip-address-limits)」を参照してください。
|ルート テーブル|AzureFirewallSubnet には既定で、NextHopType の値が **Internet** に設定された 0.0.0.0/0 ルートがあります。<br><br>Azure Firewall には、インターネットへの直接接続が必要です。 AzureFirewallSubnet が BGP 経由のオンプレミス ネットワークへの既定のルートを学習する場合は、インターネットへの直接接続を保持するために、**NextHopType** の値を **Internet** に設定した 0.0.0.0/0 UDR でそれをオーバーライドする必要があります。 既定では、Azure Firewall はオンプレミス ネットワークへの強制トンネリングをサポートしません。<br><br>ただし、オンプレミス ネットワークへの強制トンネリングが必要な構成の場合、Microsoft は状況に応じてサポートします。 サポートにお問い合わせいただければ、お客様の状況を確認させていただきます。 受け付けると、Microsoft ではサブスクリプションを許可し、必要なファイアウォールのインターネット接続が確保されていることを確認します。|

<sup>1</sup>これらの制限値を引き上げる必要がある場合は、Azure サポートにお問い合せください。
