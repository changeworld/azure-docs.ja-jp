---
title: "Azure の DNS ドメインの委任 |Microsoft ドキュメント"
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
ms.openlocfilehash: 33b3ec24432ff1268860b9a2e9d5098600a8dedc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="delegate-a-domain-to-azure-dns"></a>Azure dns ドメインを委任します。

Azure DNS を使用すると、DNS ゾーンをホストし、Azure 内のドメインの DNS レコードを管理できます。 Azure DNS に到達するドメインの DNS クエリの順序は、Azure DNS 親ドメインを委任するドメインにあります。 Azure DNS に注意してくださいは、ドメイン レジストラーではないです。 この記事では、Azure の DNS ドメインを委任する方法について説明します。

ドメインのレジストラーから購入した場合は、レジストラーは、これらの NS レコードを設定するオプションを提供します。 Azure DNS では、そのドメイン名で DNS ゾーンを作成するドメインを所有する必要はありません。 ただしは、レジストラーで Azure dns 委任を設定するドメインを所有する必要があります。

たとえば、'contoso.net' ドメインを購入し、Azure DNS で名前 'contoso.net' でゾーンを作成するとします。 ドメインの所有者では、レジストラーは、オプションをネーム サーバー アドレス (つまり、NS レコード) を構成するドメインに参加します。 レジストラーでは、ここ '.net' では、親ドメインにこれらの NS レコードを格納します。 'Contoso.net' 内の DNS レコードを解決しようとするとき、世界中のクライアントを Azure DNS ゾーンで、ドメインに送信し、ことができます。

## <a name="create-a-dns-zone"></a>DNS ゾーンを作成します。

1. Azure ポータルにサインインするには
1. ハブ メニューで、をクリックし、をクリックして**新規 > ネットワーク >**  をクリックし、 **DNS ゾーン**を作成する DNS ゾーンのブレードを開きます。

    ![DNS ゾーン](./media/dns-domain-delegation/dns.png)

1. **作成 DNS ゾーン**ブレードは、次の値を入力し、クリックして**作成**:

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|contoso.net|DNS ゾーンの名前|
   |**サブスクリプション**|[サブスクリプション]|アプリケーション ゲートウェイを作成するサブスクリプションを選択します。|
   |**リソース グループ**|**[新規作成]:** contosoRG|リソース グループを作成します。 リソース グループ名を選択したサブスクリプション内で一意にする必要があります。 リソース グループの詳細については、読み取り、[リソース マネージャー](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)概要の資料です。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループはリソース グループの場所を参照し、DNS ゾーンに影響を与えません。 DNS ゾーンの場所は常に「グローバル」でありは表示されません。

## <a name="retrieve-name-servers"></a>サーバーの名前を取得します。

Azure DNS に、DNS ゾーンを委任することができます、前にまず、ゾーンの名前のサーバー名を把握する必要があります。 Azure DNS ゾーンが作成されるたびにプールからサーバーを名前を割り当てます。

1. Azure ポータルで作成された DNS ゾーンを含む**お気に入り** ウィンドウで、をクリックして**すべてのリソース**です。 クリックして、 **contoso.net**での DNS ゾーン、**すべてのリソース**ブレードです。 既に選択したサブスクリプションでは、それにいくつかのリソースが含まれる場合は、入力**contoso.net**名でフィルター処理にしています. アプリケーション ゲートウェイを簡単にアクセスするボックスです。 

1. DNS ゾーンのブレードからネーム サーバーを取得します。 この例では、ゾーン 'contoso.net' が割り当てられてネーム サーバー 'ns1-01.azure-dns.com'、'ns2 01.azure dns .net'、' ns3-01.azure-dns.org'、および' ns4-01.azure-dns.info'。

 ![Dns ネーム サーバー](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS は、自動的に割り当てられた名前のサーバーを含むゾーンでの権威 NS レコードを作成します。  Azure PowerShell または Azure CLI を使用して名前のサーバー名を表示するには、単にこれらのレコードを取得する必要があります。

次の例では、PowerShell および Azure CLI Azure dns ゾーンのネーム サーバーを取得する手順も提供します。

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

次の例では、応答を示します。

```
Name              : @
ZoneName          : contoso.net
ResourceGroupName : contosorg
Ttl               : 172800
Etag              : 03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5
RecordType        : NS
Records           : {ns1-07.azure-dns.com., ns2-07.azure-dns.net., ns3-07.azure-dns.org.,
                    ns4-07.azure-dns.info.}
Metadata          :
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az network dns record-set show --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

次の例では、応答を示します。

```json
{
  "etag": "03bff8f1-9c60-4a9b-ad9d-ac97366ee4d5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosoRG/providers/Microsoft.Network/dnszones/contoso.net/NS/@",
  "metadata": null,
  "name": "@",
  "nsRecords": [
    {
      "nsdname": "ns1-07.azure-dns.com."
    },
    {
      "nsdname": "ns2-07.azure-dns.net."
    },
    {
      "nsdname": "ns3-07.azure-dns.org."
    },
    {
      "nsdname": "ns4-07.azure-dns.info."
    }
  ],
  "resourceGroup": "contosoRG",
  "ttl": 172800,
  "type": "Microsoft.Network/dnszones/NS"
}
```

## <a name="delegate-the-domain"></a>ドメインを委任します。

これで、DNS ゾーンを作成し、名前のサーバーがある場合、親ドメインを Azure の DNS ネーム サーバーで更新する必要があります。 各レジストラー、ドメインのネーム サーバー レコードを変更する、独自の DNS 管理ツールがあります。 レジストラーの DNS 管理ページで、NS レコードを編集し、NS レコードを Azure の DNS に作成したものに置き換えます。

Azure dns ドメインを委任する場合は、Azure DNS によって提供される名前のサーバー名を使用する必要があります。 すべて次の 4 つの名前、サーバー名、ドメインの名前に関係なくを使用することをお勧めします。 ドメインの委任では、自分のドメインとして同じ最上位ドメインを使用する名前のサーバー名は必要ありません。

'のレコードを接着' を使用する必要がありますいないを指す、Azure DNS ネーム サーバー IP アドレス、これらの IP アドレスは後で変更するためです。 'バニティ ネーム サーバー' とも呼ばれる独自のゾーンでネーム サーバー名を使用して委任は、Azure DNS で現在はサポートされません。

## <a name="verify-name-resolution-is-working"></a>名前解決が機能を確認してください。

委任を完了すると後も自動的に作成される、ゾーンの作成時に)、ゾーンの SOA レコードを照会する 'nslookup' などのツールを使用して、名前解決が動作しているを確認できます。

委任が設定されている場合、正しく、通常 DNS 解決プロセス検索ネーム サーバーに自動的に Azure DNS ネーム サーバーを指定する必要はできません。

```
nslookup -type=SOA contoso.com
```

前のコマンドからの応答の例を次に示します。

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.com
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="delegate-sub-domains-in-azure-dns"></a>Azure dns サブドメインを委任します。

1 つの子ゾーンを設定する場合は、Azure DNS 内の下位ドメインを委任できます。 たとえば、設定が、Azure DNS の委任 'contoso.net' と、1 つの子ゾーンを設定するには 'partners.contoso.net' です。

1. Azure DNS 内には、子ゾーン 'partners.contoso.net' を作成します。
2. Azure DNS で、子ゾーンをホストしているネーム サーバーを取得する子ゾーンの権限を持つの NS レコードを検索します。
3. 子ゾーンを指している親ゾーンの NS レコードを構成するには、子ゾーンを委任します。

### <a name="create-a-dns-zone"></a>DNS ゾーンを作成します。

1. Azure ポータルにサインインするには
1. ハブ メニューで、をクリックし、をクリックして**新規 > ネットワーク >**  をクリックし、 **DNS ゾーン**を作成する DNS ゾーンのブレードを開きます。

    ![DNS ゾーン](./media/dns-domain-delegation/dns.png)

1. **作成 DNS ゾーン**ブレードは、次の値を入力し、クリックして**作成**:

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|partners.contoso.net|DNS ゾーンの名前|
   |**サブスクリプション**|[サブスクリプション]|アプリケーション ゲートウェイを作成するサブスクリプションを選択します。|
   |**リソース グループ**|**使用する既存:** contosoRG|リソース グループを作成します。 リソース グループ名を選択したサブスクリプション内で一意にする必要があります。 リソース グループの詳細については、読み取り、[リソース マネージャー](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups)概要の資料です。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループはリソース グループの場所を参照し、DNS ゾーンに影響を与えません。 DNS ゾーンの場所は常に「グローバル」でありは表示されません。

### <a name="retrieve-name-servers"></a>サーバーの名前を取得します。

1. Azure ポータルで作成された DNS ゾーンを含む**お気に入り** ウィンドウで、をクリックして**すべてのリソース**です。 クリックして、 **partners.contoso.net**での DNS ゾーン、**すべてのリソース**ブレードです。 既に選択したサブスクリプションでは、それにいくつかのリソースが含まれる場合は、入力**partners.contoso.net**名でフィルター処理にしています. DNS ゾーンを簡単にアクセスするボックスです。

1. DNS ゾーンのブレードからネーム サーバーを取得します。 この例では、ゾーン 'contoso.net' が割り当てられてネーム サーバー 'ns1-01.azure-dns.com'、'ns2 01.azure dns .net'、' ns3-01.azure-dns.org'、および' ns4-01.azure-dns.info'。

 ![Dns ネーム サーバー](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS は、自動的に割り当てられた名前のサーバーを含むゾーンでの権威 NS レコードを作成します。  Azure PowerShell または Azure CLI を使用して名前のサーバー名を表示するには、単にこれらのレコードを取得する必要があります。

### <a name="create-name-server-record-in-parent-zone"></a>親ゾーンでネーム サーバー レコードを作成します。

1. 移動し、 **contoso.net** Azure ポータルで DNS ゾーンです。
1. をクリックして**セットを記録 +**
1. **レコード セットを追加**ブレードでは、次の値を入力し、クリックして**OK**:

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|パートナー|子の DNS ゾーンの名前|
   |**種類**|NS|ネーム サーバー レコードを NS を使用します。|
   |**TTL**|1|有効期限の時間です。|
   |**TTL ユニット**|時間|時間を時間単位をライブに設定します。|
   |**ネーム サーバー**|{partners.contoso.net ゾーンのネーム サーバー}|Partners.contoso.net ゾーンのネーム サーバーの 4 つすべてを入力します。 |

   ![Dns ネーム サーバー](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegating-sub-domains-in-azure-dns-with-other-tools"></a>Azure DNS 内の他のツールとサブドメインの委任

次の例では、PowerShell または CLI を持つ DNS が Azure でサブドメインを委任するのには、手順を提供します。

#### <a name="powershell"></a>PowerShell

次の PowerShell の例では、このしくみを示しています。 クロスプラット フォームの Azure cli または Azure ポータルを使用して、同じ手順を実行できます。

```powershell
# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name, in this case "partners".
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

使用して`nslookup`すべてが正しく設定されている子ゾーンの SOA レコードを検索することを確認します。

```
nslookup -type=SOA partners.contoso.com
```

```
Server: ns1-08.azure-dns.com
Address: 208.76.47.8

partners.contoso.com
    primary name server = ns1-08.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
#!/bin/bash

# Create the parent and child zones. These can be in same resource group or different resource groups as Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

名前のサーバーを取得、`partners.contoso.net`ゾーン出力からします。

```
{
  "etag": "00000003-0000-0000-418f-250de2b2d201",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contosorg/providers/Microsoft.Network/dnszones/partners.contoso.net",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "partners.contoso.net",
  "nameServers": [
    "ns1-09.azure-dns.com.",
    "ns2-09.azure-dns.net.",
    "ns3-09.azure-dns.org.",
    "ns4-09.azure-dns.info."
  ],
  "numberOfRecordSets": 2,
  "resourceGroup": "contosorg",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

各ネーム サーバー NS レコードとレコード セットを作成します。

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create a ns record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>すべてのリソースを削除します。

この記事で作成されたすべてのリソースを削除するには、次の手順を実行します。

1. Azure ポータルで**お気に入り** ウィンドウで、をクリックして**すべてのリソース**です。 クリックして、 **contosorg**リソース グループすべてのリソース ブレードにします。 既に選択したサブスクリプションでは、それにいくつかのリソースが含まれる場合は、入力**contosorg**で、**名でフィルター処理しています.** リソース グループを簡単にアクセスするボックスです。
1. **Contosorg**ブレードで、をクリックして、**削除**ボタンをクリックします。
1. ポータルでは、それを削除することを確認するリソース グループの名前を入力する必要があります。 型*contosorg*リソース グループ名の順にクリックして**削除**です。 リソース グループを削除すると、リソース グループ内のすべてのリソースを削除してので、常に削除する前に、リソース グループの内容を確認してください。 ポータルでは、リソース グループに含まれるすべてのリソースを削除し、リソース グループ自体を削除します。 このプロセスには数分をかかります。

## <a name="next-steps"></a>次のステップ

[DNS ゾーンを管理します。](dns-operations-dnszones.md)

[DNS レコードを管理します。](dns-operations-recordsets.md)
