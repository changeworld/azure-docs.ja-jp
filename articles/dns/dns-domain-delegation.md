---
title: "Azure DNS 委任の概要 |Microsoft ドキュメント"
description: "ドメインの委任の変更し、ドメイン ホスティングを提供する Azure の DNS ネーム サーバーを使用する方法を理解します。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: gwallace
ms.openlocfilehash: 31a500502a4d3e729ecb79929ed6c1de156d6259
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="delegation-of-dns-zones-with-azure-dns"></a>Azure DNS での DNS ゾーンの委任

Azure DNS を使用すると、DNS ゾーンをホストし、Azure 内のドメインの DNS レコードを管理できます。 Azure DNS に到達するドメインの DNS クエリの順序は、Azure DNS 親ドメインを委任するドメインにあります。 Azure DNS に注意してくださいは、ドメイン レジストラーではないです。 この記事では、ドメインの委任のしくみと、Azure dns ドメインを委任する方法について説明します。

## <a name="how-dns-delegation-works"></a>DNS 委任のしくみ

### <a name="domains-and-zones"></a>ドメインとゾーン

ドメイン ネーム システムは、ドメインの階層です。 階層を 'root'、ドメインの名前を持つは単に起動 '**.**' です。  この下 'com'、'net'、'org'、'uk' または 'jp' などの最上位ドメインを取得します。  これらのトップレベル ドメイン以下に 'org.uk' または 'co.jp' などの第 2 レベル ドメインを示します。  などなど。 DNS 階層内のドメインでは、別の DNS ゾーンを使用してホストされています。 これらのゾーンがグローバル配布、世界中の DNS ネーム サーバーによってホストされています。

**DNS ゾーン**-ドメインとは、システムでは、ドメイン名、たとえば 'contoso.com' 一意の名前。 DNS ゾーンを使用して、特定のドメインの DNS レコードをホストします。 たとえば、ドメイン 'contoso.com' には、(メール サーバー) の場合に、' mail.contoso.com' と web サイト) (www.contoso.com などのいくつかの DNS レコードが含まれる可能性があります。

**ドメイン レジストラー** -ドメイン レジストラーが、会社のインターネット ドメイン名を指定できます。 これらは、かどうかに使用するインターネット ドメインを使用、購入することを確認します。 ドメイン名が登録されると、ドメイン名の有効な所有者であります。 インターネット ドメインがある場合は、Azure DNS に委任する現在のドメイン レジストラーを使用します。

指定したドメイン名を所有しているユーザーまたはドメインを購入する方法についての詳細を調べるには、次を参照してください。 [Azure AD でのインターネット ドメイン管理](https://msdn.microsoft.com/library/azure/hh969248.aspx)です。

### <a name="resolution-and-delegation"></a>解像度と委任

DNS サーバーの 2 つの種類があります。

* *権限のある*DNS サーバーは DNS ゾーンをホストします。 それらのゾーンのみ内のレコードの DNS クエリを応答します。
* A*再帰*DNS サーバーが DNS ゾーンをホストしていません。 すべての DNS クエリに必要なデータを収集するための権限のある DNS サーバーを呼び出すことによって応答します。

Azure DNS は、権限を持つの DNS サービスを提供します。  再帰的な DNS サービスは提供されません。 クラウド サービスと Azure の Vm は Azure のインフラストラクチャの一部として個別に用意されている再帰的な DNS サービスを使用する自動的に構成されます。 これらの DNS 設定を変更する方法については、次を参照してください。 [azure 名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)です。

Pc またはモバイル デバイスの DNS クライアントは、通常、クライアント アプリケーションが必要なすべての DNS クエリを実行する再帰 DNS サーバーを呼び出します。

再帰 DNS サーバーは、www.contoso.com などの DNS レコードのクエリを受信したときに 'contoso.com' ドメインのゾーンをホストしている名前のサーバーを見つけることが必要があります。 ネーム サーバーを検索するには、は、ルート ネーム サーバーから開始され、そこからゾーンをホストして 'com' ネーム サーバーを検索します。 次に、'com' ネーム サーバー 'contoso.com' ゾーンをホスト ネーム サーバーを検索するクエリ。  最後に、www.contoso.com のこれらの名前のサーバーを照会することができます。

この手順は、DNS 名の解決と呼ばれます。 厳密には、DNS 解決には、次の Cname などの追加手順が含まれていますが、DNS 委任のしくみを理解するために重要ではありません。

方法は、親ゾーン ' ポイント '、子ゾーンのネーム サーバーにしますか。 これは、特殊な種類の NS レコード (「サーバーの名前」NS は) と呼ばれる DNS レコードを使用します。 たとえば、ルート ゾーンは 'com' の NS レコードが含まれていて、'com' ゾーンのネーム サーバーを示しています。 さらに、'com' ゾーンには、'contoso.com' 'contoso.com' ゾーンのネーム サーバーに示すの NS レコードが含まれています。 親ゾーンに、子ゾーンの NS レコードを設定すると、ドメインの委任が呼び出されます。

次の図は、DNS クエリの例を示します。 Contoso.net と partners.contoso.net は、Azure DNS ゾーンがします。

![Dns ネーム サーバー](./media/dns-domain-delegation/image1.png)

1. クライアントからの要求`www.partners.contoso.net`がローカルの DNS サーバーからです。
1. ローカル DNS サーバーには、レコードはないので、ルート名のサーバーに要求を行います。
1. ルートのネーム サーバー レコードがありませんのアドレスを知っている、`.net`ネーム サーバー、DNS サーバーにそのアドレスを提供
1. DNS への要求を送信する、`.net`ネーム サーバーがない contoso.net ネーム サーバーのアドレスを知っているレコードではありません。 ここでは Azure の DNS にホストされている DNS ゾーンを勧めします。
1. ゾーン`contoso.net`、レコードがありませんのネーム サーバーは認識して`partners.contoso.net`し、その応答します。 ここでは Azure の DNS にホストされている DNS ゾーンを勧めします。
1. DNS サーバーの IP アドレスを要求する`partners.contoso.net`から、`partners.contoso.net`ゾーンです。 A レコードを含むし、IP アドレスで応答します。
1. DNS サーバーをクライアントに IP アドレスを提供します。
1. クライアントが、web サイトに接続`www.partners.contoso.net`です。

各委任は、NS レコードの 2 つのコピーを実際には子ドメインと、子ゾーン内の別のポイントは親ゾーンに 1 つです。 'Contoso.net' ゾーンには、('net' 内の NS レコード) だけでなく 'contoso.net' の NS レコードが含まれています。 これらのレコードには、権威 NS レコードと呼ばれ、子ゾーンの頂点の前に座っています。

## <a name="next-steps"></a>次のステップ

学習方法[Azure DNS ドメインの委任](dns-delegate-domain-azure-dns.md)

