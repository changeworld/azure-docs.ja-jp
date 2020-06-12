---
title: よく寄せられる質問 - Azure Load Balancer
description: Azure Load Balancer についてよく寄せられる質問とその回答を紹介します。
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: 94a2398879007e7ecd6d2f1920157eb4627f33cb
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014929"
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

## <a name="what-is-ip-1686312916"></a>IP 168.63.129.16 とは何か?
Azure の正常性プローブが開始される Azure インフラストラクチャのロード バランサーとしてタグ付けされたホストの仮想 IP アドレスです。 バックエンド インスタンスを構成するとき、正常性プローブに正常に応答するために、この IP アドレスからのトラフィックを許可する必要があります。 この規則は、Load Balancer フロントエンドへのアクセスには影響しません。 Azure ロード バランサーを使用していない場合は、この規則を無視できます。 サービス タグの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)を参照してください。

## <a name="next-steps"></a>次の手順
ご不明な点が上記の一覧にない場合は、このページに関するフィードバックにご質問を添えてお送りください。 お送りいただくと、製品チームでは、お客様からの貴重なご質問すべてにお答えできるよう、GitHub 問題を作成しています。
