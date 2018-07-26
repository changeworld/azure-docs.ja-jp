---
title: Azure DNS に関する FAQ | Microsoft Docs
description: Azure DNS に関してよく寄せられる質問
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: victorh
ms.openlocfilehash: 747b2e2499a9bafcf7a7b03bc2ce144828c55c75
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172502"
---
# <a name="azure-dns-faq"></a>Azure DNS に関する FAQ

## <a name="about-azure-dns"></a>Azure DNS について

### <a name="what-is-azure-dns"></a>Azure DNS とは

ドメイン ネーム システム (DNS) は、Web サイトまたはサービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。 Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure DNS 内の DNS ドメインは、DNS ネーム サーバーから成る Azure のグローバル ネットワーク上でホストされます。 ここではエニーキャスト ネットワークが使用されるため、各 DNS クエリには、使用できる最も近い DNS サーバーが応答します。 Azure DNS はドメインに高速なパフォーマンスと高可用性の両方を提供します。

この Azure DNS サービスは、Azure Resource Manager に基づいています。 そのため、ロールに基づくアクセス制御、監査ログ、リソース ロックなどの Resource Manager 機能を利用できます。 ドメインとレコードは、Azure ポータル、Azure PowerShell コマンドレット、およびクロス プラットフォームの Azure CLI を使用して管理できます。 DNS の自動管理を必要とするアプリケーションは、REST API および SDK を使用してサービスと統合できます。

### <a name="how-much-does-azure-dns-cost"></a>Azure DNS の料金はいくらですか。

Azure DNS の課金モデルは、Azure DNS でホストされている DNS ゾーンの数と、受信する DNS クエリの数に基づきます。 使用量に基づいて割引が提供されます。

詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」ページをご覧ください。

### <a name="what-is-the-sla-for-azure-dns"></a>Azure DNS の SLA はどうなっていますか。

DNS 要求が有効な場合、Azure は 99.99% 以上の時間において Azure DNS ネーム サーバーの少なくとも 1 つから応答を受信することを保証します。

詳細については、「[Azure DNS のSLA](https://azure.microsoft.com/support/legal/sla/dns)」ページをご覧ください。

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>"DNS ゾーン" とは何ですか。 DNS ドメインと同じですか。 

ドメインとは、"contoso.com" など、ドメイン ネーム システム内で一意の名前です。


DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 たとえば、ドメイン "contoso.com" に、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、複数の DNS レコードを含めることができます。 これらのレコードは DNS ゾーン "contoso.com" でホストされます。

ドメイン名は "*ただの名前*" であり、DNS ゾーンがドメイン名の DNS レコードを含むデータ リソースです。 Azure DNS を使用すると、DNS ゾーンをホストし、Azure のドメインの DNS レコードを管理できます。 また、インターネットからの DNS クエリに応答する DNS ネーム サーバーも提供します。

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Azure DNS を使用するために DNS ドメイン名を購入する必要がありますか。 

必ずしもその必要はありません。

Azure DNS で DNS ゾーンをホストするためにドメインを購入する必要はありません。 ドメイン名を所有することなく、いつでも DNS ゾーンを作成することができます。 このゾーンに対する DNS クエリが解決されるのは、ゾーンに割り当てられた Azure DNS ネーム サーバーに送られた場合のみです。

お客様の DNS ゾーンをグローバル DNS 階層にリンクする場合は、ドメイン名を購入する必要があります。このリンクにより、世界中のどこからでも DNS クエリを実行してお客様の DNS ゾーンを検索し、お客様の DNS レコードを使用して回答が得られるようになります。

## <a name="azure-dns-features"></a>Azure DNS の機能

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Azure DNS では DNS ベースのトラフィック ルーティングまたはエンドポイント フェールオーバーがサポートされますか。

DNS ベースのトラフィック ルーティングとエンドポイント フェールオーバーは Azure Traffic Manager によって提供されます。 Azure Traffic Manager は、Azure DNS と組み合わせて使用できる別の Azure サービスです。 詳細については、[Traffic Manager の概要](../traffic-manager/traffic-manager-overview.md)を参照してください。

Azure DNS では、"静的な" DNS ドメインしかサポートされません。つまり、特定の DNS レコードに対する各 DNS クエリでは常に同じ DNS 応答が返されます。

### <a name="does-azure-dns-support-domain-name-registration"></a>Azure DNS ではドメイン名の登録がサポートされますか。

いいえ。 Azure DNS では、現在、ドメイン名の購入はサポートされていません。 ドメインを購入する場合、サードパーティのドメイン名レジストラーを利用する必要があります。 レジストラーは、通常、少額の年間料金がかかります。 購入後、ドメインを Azure DNS でホストし、DNS レコードを管理できます。 詳細については、「 [Azure DNS へのドメインの委任](dns-domain-delegation.md) 」を参照してください。

ドメインの購入は Azure バックログで追跡される機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar)できます。

### <a name="does-azure-dns-support-dnssec"></a>Azure DNS では DNSSEC はサポートされますか。

いいえ。 現在、Azure DNS で DNSSEC はサポートされません。

DNSSEC は Azure DNS のバックログで追跡される機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support)できます。

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Azure DNS ではゾーン転送 (IXFR AXFR/) はサポートされますか。

いいえ。 現在、Azure DNS でゾーン転送はサポートされません。 DNS ゾーンは [Azure CLI を使用して Azure DNS にインポート](dns-import-export.md)できます。 その後、DNS レコードは、[Microsoft Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、[CLI ツール](dns-operations-recordsets-cli.md)で管理できます。

ゾーン転送は Azure DNS のバックログで追跡される機能です。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c)できます。

### <a name="does-azure-dns-support-url-redirects"></a>Azure DNS では URL リダイレクトはサポートされますか。

いいえ。 URL リダイレクト サービスは実際には DNS サービスではありません。DNS レベルではなく HTTP レベルで処理されます。 DNS プロバイダーによって、製品全体に含めて URL リダイレクト サービスをバンドルします。 これは現在、Azure DNS ではサポートされていません。

URL リダイレクト機能は Azure DNS バックログで追跡されます。 フィードバック サイトを使用して[この機能のサポートを登録](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape)できます。

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Azure DNS では、TXT レコードセットの拡張 ASCII エンコード (8 ビット) セットはサポートされますか。

はい。 最新バージョンの Azure REST API、SDK、PowerShell、CLI (2017-10-01 または SDK 2.1 より前のバージョンでは、拡張 ASCII セットはサポートされません) を使用している場合は、Azure DNS で TXT レコードセットの拡張 ASCII エンコード セットがサポートされます。 たとえば、ユーザーが TXT レコードの値として拡張 ASCII 文字\128 (例: "abcd\128efgh") を含む文字列を指定した場合、Azure DNS ではこの文字のバイト値 (128) を内部表現で使用します。 DNS 解決時にも、応答でこのバイト値が返されます。 また、解決に関する限り、"abc" と "\097\098\099" は代替可能です。 

TXT レコードについては、[RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) のゾーン ファイルのマスター形式のエスケープ規則に従います。 たとえば、RFC に従い、"\" は実際にはあらゆるものをエスケープします。 TXT レコード値として "A\B" を指定した場合、これは "AB" として表され、解決されます。 解決時に TXT レコードに "A\B" を含める必要がある場合は、"\" を再度エスケープする必要があります。 つまり、"A\\B" と指定します。 

現在、このサポートは、Azure Portal から作成された TXT レコードでは使用できません。 

## <a name="using-azure-dns"></a>Azure DNS の使用

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Azure DNS と別の DNS プロバイダーを使用して 1 つのドメインを共同ホスティングできますか。

はい。 Azure DNS では、他の DNS サービスとのドメインの共同ホスティングがサポートされます。

これを行うには、ドメインの NS レコードを変更して、両方のプロバイダーのネーム サーバーを指すようにする必要があります (NS レコードによって、ドメインに対する DNS クエリを受け取るプロバイダーが制御されます)。 これらの NS レコードは、Azure DNS、他のプロバイダー、親ゾーン (通常、ドメイン名レジストラーで構成) の 3 か所で変更できます。 DNS 委任の詳細については、[DNS へのドメインの委任](dns-domain-delegation.md)に関する記事を参照してください。

また、ドメインの DNS レコードが両方の DNS プロバイダー間で同期するように確認する必要があります。 現在、Azure DNS では DNS ゾーン転送はサポートされません。 DNS レコードは、[Azure DNS 管理ポータル](dns-operations-recordsets-portal.md)、[REST API](https://docs.microsoft.com/powershell/module/azurerm.dns)、[SDK](dns-sdk.md)、[PowerShell コマンドレット](dns-operations-recordsets.md)、[CLI ツール](dns-operations-recordsets-cli.md)のいずれかを使用して同期する必要があります。

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>自分のドメインを 4 個の Azure DNS ネーム サーバーすべてに委任する必要がありますか。

はい。 Azure DNS は、フォールトの分離性と復元性の向上のために 4 個のネーム サーバーを各 DNS ゾーンに割り当てます。 Azure DNS の SLA を満たすには、ドメインを 4 個すべてのネーム サーバーに委任する必要があります。

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

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Azure DNS ネーム サーバーは IPv6 で解決しますか。 

はい。 Azure DNS ネーム サーバーはデュアルスタックです (IPv4 と IPv6 の両方のアドレスを持ちます)。 DNS ゾーンに割り当てられている Azure DNS ネーム サーバーの IPv6 アドレスを調べるには、nslookup などのツールを使用することができます (たとえば、`nslookup -q=aaaa <Azure DNS Nameserver>`)。

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Azure DNS 内で国際化ドメイン名 (IDN) を設定するにはどうすればよいですか。

国際化ドメイン名 (IDN) は、"[punycode](https://en.wikipedia.org/wiki/Punycode)" を使用して各 DNS 名をエンコードすることによって機能します。 DNS クエリが、このような punycode エンコーディング名を使用して作成されます。

Azure DNS に国際化ドメイン名 (IDN) を構成するには、まずゾーン名またはレコード セット名を punycode に変換します。 現在、Azure DNS では punycode に対する組み込みの変換機能はサポートされていません。

## <a name="private-dns"></a>プライベート DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Azure DNS では "プライベート" ドメインはサポートされますか。
"プライベート" ドメインのサポートは、Private Zones の機能を使用して実装されます。  現在、この機能はパブリック プレビューでご利用いただけます。  プライベート ゾーンは、インターネットに接続された Azure DNS ゾーンと同じツールを使用して管理されていますが、指定した仮想ネットワーク内からのみ解決できます。  詳細については、こちらの[概要](private-dns-overview.md)を参照してください。

現時点では、Azure Portal で Private Zones はサポートされていません。 

Azure での他の内部 DNS オプションの詳細については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>プライベート ゾーンのコンテキストで、登録仮想ネットワークと解決仮想ネットワークの違いは何ですか。 
仮想ネットワークは、登録仮想ネットワークまたは解決仮想ネットワークとして、DNS プライベート ゾーンにリンクできます。 いずれの場合でも、仮想ネットワーク内の仮想マシンがプライベート ゾーン内のレコードに対して正常に解決できます。 ただし、仮想ネットワークを登録仮想ネットワークとして指定すると、Azure は仮想ネットワーク内の仮想マシンのゾーンの中に DNS レコードを自動的に登録します (動的な登録)。 さらに、登録仮想ネットワーク内の仮想マシンが削除されると、Azure はリンクされたプライベート ゾーンから対応する DNS レコードも自動的に削除します。 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones は Azure リージョンを超えて機能しますか。
はい。 Private Zones は、プライベート ゾーンのすべての仮想ネットワークが解決仮想ネットワークとして指定されている限り、それらの仮想ネットワークが明示的にピアリングされていなくても、Azure のリージョンを超える複数の仮想ネットワーク間の DNS の解決をサポートします。 お客様によっては、TCP/HTTP トラフィックがあるリージョンから別のリージョンにフローするように、仮想ネットワークがピアリングされている必要がある場合があります。 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Private Zones では仮想ネットワークがインターネットに接続されている必要はありますか?
いいえ。 Private Zones は仮想ネットワークと連携して動作し、お客様は仮想ネットワーク内および複数の仮想ネットワーク間の Virtual Machines のドメインやその他リソースを管理できます。 名前解決にインターネットへの接続は不要です。 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>複数の仮想ネットワークの解決に同じプライベート ゾーンを使用できますか? 
はい。 1 つのプライベート ゾーンに最大で 10 の解決仮想ネットワークを関連付けることができます。

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>別のサブスクリプションに属している仮想ネットワークを、あるプライベート ゾーンの解決仮想ネットワークとして追加できますか? 
はい、ユーザーに仮想ネットワークとプライベート DNS ゾーンの両方の書き込み操作のアクセス許可が付与されていれば追加できます。 書き込みアクセス許可は複数の RBAC ロールに割り当てることができます。 たとえば、従来のネットワーク共同作成者の RBAC ロールには仮想ネットワークに対する書き込みアクセス許可が付与されています。 RBAC ロールについて詳しくは、[ロールベースのアクセス制御](../role-based-access-control/overview.md)に関するページをご覧ください。

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>プライベート ゾーンに自動的に登録された仮想マシンがお客様によって削除されると、その仮想マシンの DNS レコードは自動的に削除されますか。
はい。 登録仮想ネットワーク内にある仮想マシンを削除すると、これが登録仮想ネットワークであるため、そのゾーンに登録されていた DNS レコードも自動的に削除されます。 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>(登録仮想ネットワークから) プライベート ゾーンに自動的に登録された仮想マシンのレコードは手動で削除できますか。 
いいえ。 現時点では、登録仮想ネットワークから プライベート ゾーンに自動的に登録された仮想マシンの DNS レコードはお客様に表示されず、編集できません。 ただし、そのような自動的に登録された DNS レコードを、そのゾーンに手動で作成した DNS レコードで置換 (上書き) できます。 それに対応する次の質問と回答をご覧ください。

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>登録仮想ネットワーク内の (自動的に登録された) 既存の仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとするとどうなりますか。 
登録仮想ネットワーク内の既存の (自動的に登録された) 仮想マシンと同じホスト名を持つプライベート ゾーンに新しい DNS レコードを手動で作成しようとした場合、新しい DNS レコードで自動的に登録された仮想マシン レコードを上書きすることが許可されます。 さらに、後でそのゾーンから手動で作成した DNS レコードを削除しようとすると、削除は成功し、その仮想マシンが存在し、プライベート IP がアタッチされている限り、自動登録が再度発生します (そのゾーンに DNS レコードが自動的に再作成されます)。 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>プライベート ゾーンから登録仮想ネットワークのリンクを解除するとどうなりますか。 その仮想ネットワークから自動的に登録された仮想マシンのレコードもそのゾーンから削除されますか。
はい。 プライベート ゾーンから登録仮想ネットワークのリンクを解除 (その DNS ゾーンを更新して関連付けられた登録仮想ネットワークを削除) した場合、Azure はそのゾーンから自動的に登録された仮想マシンのレコードをすべて削除します。 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>プライベート ゾーンにリンクされている登録 (または解決) 仮想ネットワークを削除するとどうなりますか。 プライベート ゾーンを手動で更新して、そのゾーンから登録 (または解決) 仮想ネットワークとしての仮想ネットワークのリンクを解除する必要がありますか。
はい。 リンクを解除する前に登録 (または解決) 仮想ネットワークをプライベート ゾーンから削除すると、Azure では削除操作が成功しますが、プライベート ゾーンに残っている仮想ネットワークのリンクは自動的には解除されません。 仮想ネットワークのリンクはプライベート ゾーンから手動で解除する必要があります。 この理由から、仮想ネットワークを削除する前に、まずプライベート ゾーンから仮想ネットワークのリンクを解除することをおすすめします。

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>既定の FQDN (internal.cloudapp.net) を使用している DNS 解決は、プライベート ゾーン (例: contoso.local) が仮想ネットワークにリンクされている場合でも機能しますか。 
はい。 Private Zones の機能は Azure が提供する internal.cloudapp.net ゾーンを使用した既定の DNS 解決に置き換わるものではなく、追加の機能または機能強化として提供されたものです。 どちらのケースでも (Azure 提供の internal.cloudapp.net に依存または独自のプライベート ゾーンに依存する場合のいずれでも)、解決するゾーンに対して FQDN を使用することをおすすめします。 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>リンクされた仮想ネットワーク内の仮想マシンの DNS サフィックスは、そのプライベート ゾーンの DNS サフィックスに変更されますか。 
いいえ。 現時点では、リンクされた仮想ネットワーク内の仮想マシン上の DNS サフィックスは、Azure 提供の既定のサフィックス ("*.internal.cloudapp.net") のまま残ります。 ただし、仮想マシン上のこの DNS サフィックスをプライベート ゾーンの DNS サフィックスに手動で変更できます。 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>このプレビュー中の Private Zones に制限事項はありますか。
はい。 パブリック プレビュー中は、次の制限事項が存在します。
* プライベート ゾーンにつき 1 つの登録仮想ネットワーク
* プライベート ゾーンにつき最大で 10 の解決仮想ネットワーク
* 1 つの仮想ネットワークは、登録仮想ネットワークとして 1 つのプライベート ゾーンにのみリンク可能
* 1 つの仮想ネットワークは、解決仮想ネットワークとして最大で 10 のプライベート ゾーンにリンク可能
* 登録仮想ネットワークが指定された場合、プライベート ゾーンに登録されているその仮想ネットワーク内の仮想マシンの DNS レコードは PowerShell/CLI/API から表示することも取得することもできないが、VM レコードは登録が行われ、問題なく解決される
* 逆引き DNS は登録仮想ネットワーク内のプライベート IP 空間に対してのみ機能する
* プライベート ゾーンに登録されていないプライベート IP (例: プライベート ゾーンに解決仮想ネットワークとしてリンクされている仮想ネットワーク内の仮想マシンのプライベート IP) の逆引き DNS は、DNS サフィックスとして "internal.cloudapp.net" を返すが、このサフィックスは解決できない。   
* 仮想ネットワークは、 最初から登録また解決仮想ネットワークとしてプライベート ゾーンにリンクされているときは、 空である (NIC がアタッチされている仮想マシンがない) 必要がある。 ただし、その他のプライベート ゾーンに登録または解決仮想ネットワークとしてさらにリンクする場合、仮想ネットワークは空でなくてもかまいません。 
* 現時点では、条件付きの転送 (例: Azure とオンプレミス ネットワーク間の解決を有効にする) はサポートされていない。 他のメカニズムを介してこのシナリオを実現する方法については、「[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Private Zones のゾーンやレコードにクォータや制限はありますか。
Private Zones には、サブスクリプションごとのゾーンの数や、ゾーンごとのレコード セットの数について、別個の制限はありません。 Public Zones と Private Zones は両方とも[ここ](../azure-subscription-service-limits.md#dns-limits)に示す DNS 全体の制限が適用されます。

### <a name="is-there-portal-support-for-private-zones"></a>Private Zones のポータル サービスはありますか。
非ポータル メカニズム (API/PowerShell/CLI/SDK) によってすでに作成されている Private Zones は Azure Portal に表示されますが、新しいプライベート ゾーンを作成したり、仮想ネットワークとの関連付けを管理したりすることはできません。 さらに、登録仮想ネットワークとして関連付けられている仮想ネットワークについては、自動的に登録された仮想マシンのレコードは Azure Portal に表示されません。 

## <a name="next-steps"></a>次の手順

[Azure DNS について学ぶ](dns-overview.md)
<br>
[プライベート ドメインに Azure DNS を使用する方法を学ぶ](private-dns-overview.md)
<br>
[DNS ゾーンとレコードについて学ぶ](dns-zones-records.md)
<br>
[Azure DNS の概要](dns-getstarted-portal.md)

