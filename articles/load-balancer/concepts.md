---
title: Azure Load Balancer の概念
description: Azure Load Balancer の概念の概要
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
ms.openlocfilehash: 946741c8aa70040dd0186deceab0fb090cf38c11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271747"
---
# <a name="azure-load-balancer-algorithm"></a>Azure Load Balancer のアルゴリズム

ロード バランサーは、UDP と TCP の両方のアプリケーションに対していくつかの機能を提供します。

## <a name="load-balancing-algorithm"></a>負荷分散アルゴリズム

ロード バランサー規則を作成することによって、受信トラフィック フローをロード バランサーのフロントエンドからそのバックエンド プールに分散させることができます。 Azure Load Balancer では、受信フロー (バイトではなく、フロー) の分散のために 5 タプル ハッシュ アルゴリズムを使用します。  ロード バランサーでは、トラフィックをバックエンド プール インスタンスに転送するときに、TCP/UDP ヘッダー フローのヘッダーを書き換えます (ロード バランサーでは HTTP/HTTPS ヘッダーを書き換えません)。 ロード バランサーの正常性プローブが正常なバックエンド エンドポイントを示している場合は、バックエンド インスタンスを使用して新しいトラフィック フローを受信できます。

既定では、ロード バランサーは 5 タプル ハッシュを使用します。

ハッシュには次のものが含まれます。

- **ソース IP アドレス**
- **ソース ポート**
- **宛先 IP アドレス**
- **宛先ポート**
- **使用可能なサーバーにフローをマップするための IP プロトコル番号**

## <a name="session-persistence"></a>セッション永続化

同じフローのパケットは、同じバックエンド プール インスタンスに到達します。 ただし、クライアントが同じソース IP から新しいフローを開始した場合は、ソース ポートが変更されます。 その結果、5 タプルのハッシュによって、トラフィックは別のバックエンド エンドポイントに向かうことになります。 

ソース IP アドレスへのセッション永続化は、2 または 3 タプル ハッシュを使用して作成されます。 セッション永続化が有効になっている場合、同じクライアント IP アドレスからの連続した要求は同じ仮想マシンによって処理されます。 ロード バランサーの分散モードの詳細については、「[Azure Load Balancer の分散モードを構成する](./load-balancer-distribution-mode.md)」を参照してください。

次の図は、ハッシュベースの分散を示しています。

![ハッシュベースの分散](./media/load-balancer-overview/load-balancer-distribution.png)

*図:ハッシュベースの分散*

## <a name="application-independence-and-transparency"></a>アプリケーションの独立と透過性

ロード バランサーは任意の TCP/UDP アプリケーション シナリオをサポートしており、フローを閉じたり、開始したりはしません。 ロード バランサーはまた、どのフローのペイロードとも対話しません。 

- アプリケーション ペイロードはロード バランサーに対して透過的です。 任意の UDP または TCP アプリケーションをサポートできます。

ロード バランサーはレイヤー 4 で動作し、アプリケーション レイヤーのゲートウェイ機能は提供しません。 プロトコル ハンドシェイクは、常にクライアントとバックエンド プール インスタンスの間で直接発生します。 

- ロード バランサーでは TCP ペイロードと対話したり、TLS オフロードを提供したりしないため、包括的な暗号化シナリオを構築できます。 ロード バランサーを使用すると、VM 自体の TLS 接続が終了されることで、TLS アプリケーションの大規模なスケールアウトを実現できます。 たとえば、TLS セッションのキー容量は、バックエンド プールに追加する VM の数と種類によってのみ制限されます。

受信フローへの応答は常に、仮想マシンからの応答です。 仮想マシンにフローが到着するときは、元のソース IP アドレスも保持されます。

- すべてのエンドポイントは、VM によって応答されます。 たとえば、TCP ハンドシェイクは、クライアントと選択されたバックエンド VM との間で行われます。 フロントエンドへの要求に対する応答は、バックエンドの VM によって生成される応答です。 フロントエンドへの接続を正常に検証するときは、少なくとも 1 つのバックエンド仮想マシンへの接続を全体にわたって検証していることになります。


## <a name="next-steps"></a>次のステップ

- Azure Load Balancer を構成する[コンポーネント](components.md)について確認します。
- Load Balancer の使用を始めるには、[パブリック Standard Load Balancer の作成](quickstart-load-balancer-standard-public-portal.md)に関するページを参考に Load Balancer を 1 つ作成し、カスタム IIS 拡張機能がインストールされている VM を作成して、Web アプリを VM 間で負荷分散します。
- [Azure Load Balancer のアウトバウンド接続](load-balancer-outbound-connections.md)について学習する。
- [Azure Load Balancer](load-balancer-overview.md) についてさらに詳しく学習する。
- [正常性プローブ](load-balancer-custom-probe-overview.md)について学習する。
- [Standard Load Balancer の診断](load-balancer-standard-diagnostics.md)について学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)の詳細を確認する。
