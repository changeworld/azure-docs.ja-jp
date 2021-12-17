---
title: Azure Load Balancer のフローティング IP の構成
description: Azure Load Balancer のフローティング IP の概要
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: a72eceba85e14e058fa7c8ba2b109009e46e6df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357453"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer のフローティング IP の構成

ロード バランサーは、UDP と TCP の両方のアプリケーションに対していくつかの機能を提供します。

## <a name="floating-ip"></a>フローティング IP

一部のアプリケーション シナリオでは、バックエンド プール内の 1 つの VM で複数のアプリケーション インスタンスによって同じポートが使用されることを選択するまたは使用されることが求められる場合があります。 ポート再利用の一般的な例を次に示します。 
- 高可用性を実現するためのクラスタリング
- ネットワーク仮想アプライアンス
- 再暗号化によらない複数 TLS エンドポイントの公開。 

複数の規則でバックエンド ポートを再利用するには、規則の定義で Floating IP を有効にする必要があります。

Floating IP を有効にすると、IP アドレスのマッピングが、バックエンド インスタンスの IP ではなく Load Balancer のフロントエンド IP アドレスに変更されます。 

Floating IP がない場合は、VM インスタンスの IP が公開されます。 Floating IP を有効にすると、IP アドレス マッピングがロード バランサーのフロントエンド IP に変更され、柔軟性が上がります。 [こちら](load-balancer-multivip-overview.md)をご覧ください。

## <a name="limitations"></a><a name = "limitations"></a>制限事項

- フローティング IP は現在、負荷分散シナリオのセカンダリ IP 構成ではサポートされていません

## <a name="next-steps"></a>次のステップ

- Load Balancer の使用を開始するには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参照してください。
- [Azure Load Balancer のアウトバウンド接続](load-balancer-outbound-connections.md)について学習する。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)の詳細を確認する。
