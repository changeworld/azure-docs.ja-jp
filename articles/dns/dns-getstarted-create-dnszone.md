---
title: "Azure DNS の概要 | Microsoft Docs"
description: "Azure DNS に DNS ゾーンを作成する方法について説明します。 Azure PowerShell を使用して最初の DNS ゾーンを作成し管理するためのステップ バイ ステップ ガイドです。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: d78583b7-e669-435c-819b-7605cf791b0e
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 36fa9cd757b27347c08f80657bab8a06789a3c2f
ms.openlocfilehash: 798717eca5180e445b14351bbfc694ed206ee9dc
ms.lasthandoff: 02/27/2017

---

# <a name="create-a-dns-zone-using-powershell"></a>PowerShell を使用した DNS ゾーンの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
> * [PowerShell](dns-getstarted-create-dnszone.md)
> * [Azure CLI 1.0](dns-getstarted-create-dnszone-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-create-dnszone-cli.md)

この記事では、Azure PowerShell を使用して DNS ゾーンを作成する手順を説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-getstarted-create-dnszone-cli.md) または [Azure Portal](dns-getstarted-create-dnszone-portal.md) を使用して、作成することもできます。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzureRmDnsZone` コマンドレットを使用して作成します。 次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="verify-your-dns-zone"></a>DNS ゾーンの確認

### <a name="view-records"></a>レコードの表示

DNS ゾーンを作成すると、次の DNS レコードも作成されます。

* *Start of Authority* (SOA) レコード。 このレコードは、すべての DNS ゾーンのルートに存在します。
* 権威ネーム サーバー (NS) レコード。 これらのレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。 Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。 詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

このレコードを表示するには、 `Get-AzureRmDnsRecordSet`を使用します。

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 172800
Etag              : f573237b-088c-424a-b53c-08567d87d049
RecordType        : NS
Records           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
Metadata          : 

Name              : @
ZoneName          : contoso.com
ResourceGroupName : MyAzureResourceGroup
Ttl               : 3600
Etag              : bf88a27d-0eec-4847-ad42-f0c83b9a2c32
RecordType        : SOA
Records           : {[ns1-01.azure-dns.com.,azuredns-hostmaster.microsoft.com,3600,300,2419200,300]}
Metadata          : 
```

> [!NOTE]
> DNS ゾーンのルート (または " *頂点*") にあるレコード セットは、レコード セット名として **@** を使用します。

### <a name="test-name-servers"></a>ネーム サーバーのテスト

Azure DNS ネーム サーバーに DNS ゾーンが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの&1; つに DNS クエリを直接送信する必要があります。 ゾーンのネーム サーバーは、上の `Get-AzureRmDnsRecordSet` で一覧表示されているように、NS レコードで与えられます。 次の例を実際のゾーンの正しい値に置き換えてください。

```
nslookup
> set type=SOA
> server ns1-01.azure-dns.com
> contoso.com

Server: ns1-01.azure-dns.com
Address:  40.90.4.1

contoso.com
        primary name server = ns1-01.azure-dns.com
        responsible mail addr = azuredns-hostmaster.microsoft.com
        serial  = 1
        refresh = 3600 (1 hour)
        retry   = 300 (5 mins)
        expire  = 2419200 (28 days)
        default TTL = 300 (5 mins)
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンを作成したら、[レコード セットとレコードを作成](dns-getstarted-create-recordset.md)し、インターネット ドメインの DNS レコードを作成します。

