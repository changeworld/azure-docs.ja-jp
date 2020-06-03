---
title: よく寄せられる質問 - Azure Load Balancer
description: Azure Load Balancer についてよく寄せられる質問とその回答を紹介します。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 3be8ce241817b3b2fa03976eebe3147c1dc9c877
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005151"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

## <a name="what-types-of-load-balancer-exist"></a>Azure Load Balancer には、どんな種類がありますか?
VNET 内のトラフィックを負荷分散する内部ロード バランサーと、インターネットに接続されたエンドポイントとの間のトラフィックを負荷分散する外部ロード バランサーがあります。 詳細については、[Load Balancer の種類](components.md#frontend-ip-configurations)に関するページを参照してください。 

これらのどちらの種類についても、Azure では、さまざまな機能、パフォーマンス、セキュリティ、正常性の追跡機能を含む Basic SKU と Standard SKU が提供されています。 これらの違いについては、記事「[SKU の比較](skus.md)」で説明しています。

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>Basic Load Balancer から Standard Load Balancer にアップグレードするには、どうすればよいですか?
Load Balancer の SKU をアップグレードするための自動スクリプトとガイダンスについては、[Basic から Standard へのアップグレード](upgrade-basic-standard.md)に関する記事を参照してください。

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>Azure の負荷分散オプションには、どのような違いがありますか?
使用可能な負荷分散サービスとそれぞれの推奨される使用方法については、[ロード バランサーのテクノロジ ガイド](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)を参照してください。

## <a name="where-can-i-find-load-balancer-arm-templates"></a>Azure Load Balancer の ARM テンプレートはどこで入手できますか?
一般的なデプロイの ARM テンプレートについては、[Azure Load Balancer クイックスタート テンプレート](https://docs.microsoft.com/azure/templates/microsoft.network/loadbalancers#quickstart-templates)の一覧を参照してください。

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>インバウンド NAT 規則と負荷分散規則には、どのような違いがありますか?
NAT 規則は、トラフィックをルーティングするバックエンド リソースを指定するために使用されます。 たとえば、特定の VM に RDP トラフィックを送信するように特定のロード バランサー ポートを構成することができます。 負荷分散規則は、トラフィックをルーティングするバックエンド リソースのプールを指定して、各インスタンス間で負荷を分散します。 たとえば、負荷分散規則では、ロード バランサーのポート 80 上の TCP パケットを Web サーバーのプール全体にルーティングできます。

## <a name="next-steps"></a>次の手順
ご不明な点が上記の一覧にない場合は、このページに関するフィードバックにご質問を添えてお送りください。 お送りいただくと、製品チームでは、お客様からの貴重なご質問すべてにお答えできるよう、GitHub 問題を作成しています。
