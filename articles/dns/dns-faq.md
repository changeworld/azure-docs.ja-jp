---
title: "Azure DNS に関する FAQ | Microsoft Docs"
description: "Azure DNS に関してよく寄せられる質問"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: a8cff450730d05970398989ceb3e8585aefd6771
ms.sourcegitcommit: 4ea06f52af0a8799561125497f2c2d28db7818e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2017
---
# <a name="azure-dns-faq"></a>Azure DNS に関する FAQ

## <a name="about-azure-dns"></a>Azure DNS について

### <a name="what-is-azure-dns"></a>Azure DNS とは

ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 エニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 これによって、ドメインには高速なパフォーマンスと高可用性の両方が提供されます。

この Azure DNS サービスは、Azure Resource Manager に基づいています。 そのため、ロールに基づくアクセス制御、監査ログ、リソース ロックなどの Resource Manager 機能を利用できます。 ドメインとレコードは、Azure ポータル、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS の料金はいくらですか。

Azure DNS の課金モデルは、Azure DNS でホストされている DNS ゾーンの数と、受信する DNS クエリの数に基づきます。 使用量に基づいて割引が提供されます。

詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」ページをご覧ください。

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS の SLA はどうなっていますか。

DNS 要求が有効な場合、99.99% 以上の時間において Azure DNS ネーム サーバーの少なくとも 1 つから応答を受信することを保証します。

詳細については、「[Azure DNS のSLA](https://azure.microsoft.com/support/legal/sla/dns)」ページをご覧ください。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>"DNS ゾーン" とは何ですか。 DNS ドメインと同じですか。 

ドメインとは、"contoso.com" など、ドメイン ネーム システム内で一意の名前です。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 たとえば、ドメイン "contoso.com" に、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、複数の DNS レコードを含めることができます。 これらは DNS ゾーン "contoso.com" でホストされます。

ドメイン名は "*ただの名前*" であり、DNS ゾーンがドメイン名の DNS レコードを含むデータ リソースです。 Azure DNS を使用すると、DNS ゾーンをホストし、Azure のドメインの DNS レコードを管理できます。 また、インターネットからの DNS クエリに応答する DNS ネーム サーバーも提供します。

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS を使用するために DNS ドメイン名を購入する必要がありますか。 

必ずしもその必要はありません。

Azure DNS で DNS ゾーンをホストするためにドメインを購入する必要はありません。 ドメイン名を所有することなく、いつでも DNS ゾーンを作成することができます。 このゾーンに対する DNS クエリが解決されるのは、ゾーンに割り当てられた Azure DNS ネーム サーバーに送られた場合のみです。

お客様の DNS ゾーンをグローバル DNS 階層にリンクする場合は、ドメイン名を購入する必要があります。このリンクにより、世界中のどこからでも DNS クエリを実行してお客様の DNS ゾーンを検索し、お客様の DNS レコード使用して回答が得られるようになります。

## <a name="azure-dns-features"></a>Azure DNS の機能

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS では DNS ベースのトラフィック ルーティングまたはエンドポイント フェールオーバーがサポートされますか。

DNS ベースのトラフィック ルーティングとエンドポイント フェールオーバーは Azure Traffic Manager によって提供されます。 これは、Azure DNS と組み合わせて使用できる別の Azure サービスです。 詳細については、[Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を参照してください。

Azure DNS では、"静的な" DNS ドメインしかサポートされません。つまり、特定の DNS レコードに対する各 DNS クエリでは常に同じ DNS 応答が返されます。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS ではドメイン名の登録がサポートされますか。

いいえ。 Azure DNS では、現在、ドメイン名の購入はサポートされていません。 ドメインを購入する場合、サードパーティのドメイン名レジストラーを利用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストし、DNS レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

これはバックログで追跡中の機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)できます。

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS では "プライベート" ドメインはサポートされますか。
"プライベート" ドメインのサポートは、プライベート DNS ゾーンを使用して実装されます。  この機能は、現在プレビューとして使用できます。  プライベート DNS ゾーンは、インターネットに接続された Azure DNS ゾーンと同じツールを使用して管理されていますが、指定した仮想ネットワーク内からのみ解決できます。  詳細については、こちらの[概要](private-dns-overview.md)を参照してください。

Azure での他の内部 DNS オプションの詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS では DNSSEC はサポートされますか。

いいえ。 現在、Azure DNS で DNSSEC はサポートされません。

これはバックログで追跡中の機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)できます。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS ではゾーン転送 (IXFR AXFR/) はサポートされますか。

いいえ。 現在、Azure DNS でゾーン転送はサポートされません。 DNS ゾーンは [Azure CLI を使用して Azure DNS にインポート](dns-import-export.md)できます。 その後、DNS レコードは、[Microsoft Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、[CLI ツール](dns-operations-recordsets-cli.md)で管理できます。

これはバックログで追跡中の機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)できます。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS では URL リダイレクトはサポートされますか。

いいえ。 URL リダイレクト サービスは実際には DNS サービスではありません。DNS レベルではなく HTTP レベルで処理されます。 DNS プロバイダーによって、製品全体に含めて URL リダイレクト サービスをバンドルします。 これは現在、Azure DNS ではサポートされていません。

この機能はバックログで追跡されます。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)できます。

## <a name="using-azure-dns"></a>Azure DNS の使用

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Azure DNS と別の DNS プロバイダーを使用して 1 つのドメインを共同ホスティングできますか。

はい。 Azure DNS では、他の DNS サービスとのドメインの共同ホスティングがサポートされます。

これを行うには、ドメインの NS レコードを変更して、両方のプロバイダーのネーム サーバーを指すようにする必要があります (NS レコードによって、ドメインに対する DNS クエリを受け取るプロバイダーが制御されます)。 このような NS レコードは、Azure DNS、もう 1 つのプロバイダー、親ゾーン (通常、ドメイン名レジストラーで構成) の 3 箇所で変更する必要があります。 DNS 委任の詳細については、[DNS へのドメインの委任](dns-domain-delegation.md)に関する記事を参照してください。

また、ドメインの DNS レコードが両方の DNS プロバイダー間で同期するように確認する必要があります。 現在、Azure DNS では DNS ゾーン転送はサポートされません。 [Microsoft Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、[CLI ツール](dns-operations-recordsets-cli.md)のいずれかを使用して、DNS レコードを同期する必要があります。

### <a name="do-i-have-to-delegate-my-domain-to-all-4-azure-dns-name-servers"></a>自分のドメインを 4 個の Azure DNS ネーム サーバーすべてに委任する必要がありますか。

はい。 Azure DNS は、フォールトの分離と復元性の向上のために 4 個のネーム サーバーを各 DNS ゾーンに割り当てます。 Azure DNS の SLA を満たすには、ドメインを 4 個すべてのネーム サーバーに委任する必要があります。

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Azure DNS の使用制限とは何ですか。

Azure DNS を使用する際は、次の制限が既定で適用されます。

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Azure DNS ゾーンをリソース グループ間またはサブスクリプション間で移動できますか。

はい。 DNS ゾーンは、リソース グループ間またはサブスクリプション間で移動できます。

DNS ゾーンを移動しても DNS クエリへの影響はありません。 ゾーンに割り当てられたネーム サーバーは同じままで、DNS クエリは最初から最後まで通常どおりに処理されます。

DNS ゾーンの移動に関する詳細と手順については、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>DNS の変更が有効になるまでどれくらいかかりますか。

通常、新しい DNS ゾーンと DNS レコードは、Azure DNS ネーム サーバーに数秒以内ですぐに反映されます。

既存の DNS レコードへの変更は少し時間がかかりますが、60 秒以内には Azure DNS ネーム サーバーに反映されます。 この場合、Azure DNS 外部の DNS キャッシュ (DNS クライアントと DNS 再帰リゾルバーによる) も、DNS の変更が有効になるまでの時間に影響することがあります。 このキャッシュ期間は、各レコード セットの Time-To-Live (TTL) プロパティを使用して制御できます。

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>誤って削除されないように DNS ゾーンを保護するにはどうすればよいですか。

Azure DNS は Azure Resource Manager を使用して管理され、Azure Resource Manager が提供するアクセス制御機能のメリットを受けています。 ロールに基づくアクセス制御を使用して、DNS ゾーンとレコード セットに対する読み取りアクセスまたは書き込みアクセスを持つユーザーを制御できます。 DNS ゾーンとレコード セットを誤って変更または削除しないためにリソース ロックを適用することができます。

詳細については、「[DNS ゾーンとレコードを保護する方法](dns-protect-zones-recordsets.md)」を参照してください。

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Azure DNS 内で SPF レコードを設定するにはどうすればよいですか。

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6-"></a>Azure DNS ネーム サーバーは IPv6 で解決しますか。 

はい。 Azure DNS ネーム サーバーはデュアルスタックです (IPv4 と IPv6 の両方のアドレスを持ちます)。 DNS ゾーンに割り当てられている Azure DNS ネーム サーバーの IPv6 アドレスを調べるには、nslookup などのツールを使用することができます (たとえば、`nslookup -q=aaaa <Azure DNS Nameserver>`)。

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Azure DNS 内で国際化ドメイン名 (IDN) を設定するにはどうすればよいですか。

国際化ドメイン名 (IDN) は、"[punycode](https://en.wikipedia.org/wiki/Punycode)" を使用して各 DNS 名をエンコードすることによって機能します。 DNS クエリが、このような punycode エンコーディング名を使用して作成されます。

Azure DNS に国際化ドメイン名 (IDN) を構成するには、まずゾーン名またはレコード セット名を punycode に変換します。 現在、Azure DNS では punycode に対する組み込みの変換機能はサポートされていません。

## <a name="next-steps"></a>次のステップ

[Azure DNS について学ぶ](dns-overview.md)
<br>
[プライベート ドメインに Azure DNS を使用する方法を学ぶ](private-dns-overview.md)
<br>
[DNS ゾーンとレコードについて学ぶ](dns-zones-records.md)
<br>
[Azure DNS を使ってみる](dns-getstarted-portal.md)

