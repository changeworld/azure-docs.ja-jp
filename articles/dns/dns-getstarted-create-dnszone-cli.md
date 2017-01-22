---
title: "CLI を使用した DNS ゾーンの作成 | Microsoft Docs"
description: "Azure DNS に DNS ゾーンを作成する方法について説明します。 Azure CLI を使用して最初の DNS ゾーンを作成して管理するためのステップ バイ ステップ ガイドです。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: 1514426a-133c-491a-aa27-ee0962cea9dc
ms.service: dns
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f156e4d4c5ffddb7e93ebf21baa75864e0e260e9
ms.openlocfilehash: d00792a4bb19e194dbbcee8b9c11e4a744891388

---

# <a name="create-an-azure-dns-zone-using-cli"></a>CLI を使用した Azure DNS ゾーンの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI](dns-getstarted-create-dnszone-cli.md)

この記事では、Windows、Mac、Linux で使用できるクロスプラットフォーム Azure CLI を使用して DNS ゾーンを作成する手順について説明します。 DNS ゾーンは、[Azure PowerShell](dns-getstarted-create-dnszone.md) または [Azure Portal](dns-getstarted-create-dnszone-portal.md) を使用して作成することもできます。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-cli-setup](../../includes/dns-cli-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。 このコマンドのヘルプを表示するには、「`azure network dns zone create -h`」と入力します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
azure network dns zone create MyResourceGroup contoso.com
```

## <a name="verify-your-dns-zone"></a>DNS ゾーンの確認

### <a name="view-records"></a>レコードの表示

DNS ゾーンを作成すると、次の DNS レコードも作成されます。

* *Start of Authority* (SOA) レコード。 このレコードは、すべての DNS ゾーンのルートに存在します。
* 権威ネーム サーバー (NS) レコード。 これらのレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。 Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

このレコードを表示するには、 `azure network dns-record-set list`を使用します。

```azurecli
azure network dns record-set list MyResourceGroup contoso.com

info:    Executing command network dns record-set list
+ Looking up the DNS Record Sets
data:    Name                            : @
data:    Type                            : NS
data:    TTL                             : 172800
data:    Records:
data:      ns1-01.azure-dns.com.
data:      ns2-01.azure-dns.net.
data:      ns3-01.azure-dns.org.
data:      ns4-01.azure-dns.info.
data:
data:    Name                            : @
data:    Type                            : SOA
data:    TTL                             : 3600
data:    Email                           : azuredns-hostmaster.microsoft.com
data:    Host                            : ns1-01.azure-dns.com.
data:    Serial Number                   : 2
data:    Refresh Time                    : 3600
data:    Retry Time                      : 300
data:    Expire Time                     : 2419200
data:    Minimum TTL                     : 300
data:
info:    network dns record-set list command OK
```

> [!NOTE]
> DNS ゾーンのルート (または " *頂点*") にあるレコード セットは、レコード セット名として **@** を使用します。

### <a name="test-name-servers"></a>ネーム サーバーのテスト

Azure DNS ネーム サーバーに DNS ゾーンが存在することをテストするには、nslookup、dig、`Resolve-DnsName` PowerShell コマンドレットなどの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。 ゾーンのネーム サーバーは、`azure network dns record-set list` で一覧表示されるように、NS レコードで与えられます。

次の例では、dig を使用し、DNS ゾーンに割り当てられたネーム サーバーを使用して、ドメイン contoso.com をクエリします。 実際のゾーンの正しい値に置き換えてください。

     > dig @ns1-01.azure-dns.com contoso.com
     
     <<>> DiG 9.10.2-P2 <<>> @ns1-01.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
     Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
     flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
     WARNING: recursion requested but not available

     OPT PSEUDOSECTION:
     EDNS: version: 0, flags:; udp: 4000
      QUESTION SECTION:
    contoso.com.                        IN      A

     AUTHORITY SECTION:
    contoso.com.         3600     IN      SOA     ns1-01.azure-dns.com. azuredns-hostmaster.microsoft.com. 1 3600 300 2419200 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## <a name="next-steps"></a>次のステップ

DNS ゾーンを作成した後は、ゾーンで [DNS レコード セットとレコードの作成](dns-getstarted-create-recordset-cli.md)を行います。




<!--HONumber=Dec16_HO3-->


