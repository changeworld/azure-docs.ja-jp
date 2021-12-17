---
title: Azure Firewall DNS プロキシの詳細
description: Azure Firewall DNS Proxy の仕組みについて説明します
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: victorh
ms.openlocfilehash: f6f17823b51200c21ee0975f50487ba1e3f58ced
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547927"
---
# <a name="azure-firewall-dns-proxy-details"></a>Azure Firewall DNS プロキシの詳細

Azure Firewall が DNS プロキシとして機能するように構成できます。 DNS プロキシは、クライアント仮想マシンから DNS サーバーへの DNS 要求の仲介役です。

次の情報では、Azure Firewall DNS プロキシの実装の詳細について説明します。

## <a name="fqdns-with-multiple-a-records"></a>複数の A レコードを持つ FQDN

Azure Firewall は標準 DNS クライアントとして機能します。 応答に複数の A レコードがある場合、ファイアウォールではすべてのレコードがキャッシュに格納されます。 応答ごとに 1 つのレコードがある場合、ファイアウォールは 1 つのレコードのみを格納します。 クライアントが応答で 1 つ以上の A レコードを期待する必要がある場合、クライアントが前もって知る方法はありません。

## <a name="fqdn-time-to-live-ttl"></a>FQDN の有効期間 (TTL)

FQDN TTL (有効期間) の有効期限が近い場合、レコードはキャッシュされ、その TTL に従って期限切れになります。 プリフェッチは使用されません。そのため、TTL の有効期限の前に、レコードを更新するためにファイアウォールで参照を実行することはありません。

## <a name="clients-not-configured-to-use-the-firewall-dns-proxy"></a>ファイアウォール DNS プロキシを使用するように構成されていないクライアント

ファイアウォール DNS プロキシではない DNS サーバーを使用するようにクライアント コンピューターが構成されている場合、結果は予測できない可能性があります。

たとえば、クライアント ワークロードが米国東部であり、米国東部でホストされているプライマリ DNS サーバーを使用するとします。 Azure Firewall DNS サーバーの設定は、米国西部でホストされているセカンダリ DNS サーバーに対して構成されます。 米国西部でホストされているファイアウォールの DNS サーバーは、米国東部のクライアントとは異なる応答になります。

これは一般的なシナリオであり、クライアントがファイアウォールの DNS プロキシ機能を使用しなければならない理由です。 ネットワーク規則で FQDN を使用する場合、クライアントはファイアウォールをリゾルバーとして使用する必要があります。 クライアントとファイアウォール自体によって、IP アドレス解決の一貫性を確保できます。

この例では、FQDN がネットワーク規則で構成されている場合、ファイアウォールは FQDN を IP1 (IP アドレス 1) に解決し、IP1 へのアクセスを許可するようにネットワーク規則を更新します。 DNS 応答の違いにより、クライアントが同じ FQDN を IP2 に解決した場合、その接続試行はファイアウォールの規則と一致しないので、拒否されます。 

アプリケーション規則の HTTP/S FQDN の場合、ファイアウォールはホストまたは SNI ヘッダーから FQDN を解析して解決し、その IP アドレスに接続します。 クライアントが接続しようとしていた宛先 IP アドレスは無視されます。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall の DNS 設定](dns-settings.md)