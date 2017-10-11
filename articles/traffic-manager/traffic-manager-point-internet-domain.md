---
title: "会社のインターネット ドメインを指す Traffic Manager ドメイン名 |Microsoft ドキュメント"
description: "この記事には、会社のドメイン名を Traffic Manager ドメイン名をポイントするのに役立ちます。"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 29822946-2d45-4434-ba47-fc180a445cc3
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 0322b3510cfd4f94031d8c1db8f1cc032b997fa8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>会社のインターネット ドメインを指す、Azure Traffic Manager ドメイン

Traffic Manager プロファイルを作成するときに Azure ではそのプロファイル用の DNS 名が自動的に割り当てます。 DNS ゾーンから名前を使用するには、Traffic Manager プロファイルのドメイン名にマップする CNAME DNS レコードを作成します。 Traffic Manager のドメイン名を見つけることができます、**全般**Traffic Manager プロファイルの [構成] ページのセクションでします。

たとえば、名 www.contoso.com を指す Traffic Manager の DNS 名 contoso.trafficmanager.net は、次の DNS リソース レコードを作成します。

    www.contoso.com IN CNAME contoso.trafficmanager.net

すべてのトラフィックを要求する*www.contoso.com*にダイレクト*contoso.trafficmanager.net*です。

> [!IMPORTANT]
> などの第 2 レベル ドメインを指すことはできません*contoso.com*、Traffic Manager ドメイン。 DNS プロトコルの標準では、第 2 レベル ドメイン名に対する CNAME レコードは許可されません。

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のルーティング メソッド](traffic-manager-routing-methods.md)
* [トラフィック マネージャー - 無効化、有効にするか、プロファイルの削除](disable-enable-or-delete-a-profile.md)
* [Traffic Manager の無効化またはエンドポイントを有効にします。](disable-or-enable-an-endpoint.md)
