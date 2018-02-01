---
title: "Azure DNS へのドメインの委任 | Microsoft Docs"
description: "ドメインの委任を変更し、ドメインのホストに Azure DNS ネーム サーバーを使用する方法を説明します。"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.assetid: 257da6ec-d6e2-4b6f-ad76-ee2dde4efbcc
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: kumud
ms.openlocfilehash: a13d9b360e2c43aa2a3d4f62215e4570ce42cd5b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2018
---
# <a name="delegate-a-domain-to-azure-dns"></a>Azure DNS へのドメインの委任

Azure DNS を使用すると、DNS ゾーンをホストし、Azure のドメインの DNS レコードを管理できます。 ドメインの DNS クエリを Azure DNS に到達させるには、ドメインを親ドメインから Azure DNS に委任する必要があります。 Azure DNS はドメイン レジストラーではないことに注意してください。 この記事では、ドメインを Azure DNS に委任する方法について説明します。

レジストラーから購入するドメインの場合は、レジストラーから、NS レコードを設定するオプションが提供されます。 ドメインを所有していなくても、Azure DNS 内にそのドメイン名で DNS ゾーンを作成できます。 ただし、レジストラーを使用して Azure DNS への委任を設定するには、ドメインを所有する必要があります。

たとえば、ドメイン contoso.net を購入し、Azure DNS に contoso.net という名前のゾーンを作成するとします。 自分がドメインの所有者であるので、ドメインのネーム サーバー アドレス (つまり、NS レコード) を構成するオプションがレジストラーから提供されます。 レジストラーはこれらの NS レコードを親ドメイン (.net) に格納します。 その後は、クライアントが世界のどこにあっても、contoso.net 内の DNS レコードを解決しようとした時点で、Azure DNS ゾーン内のドメインに転送することができます。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
1. **ハブ** メニューで、**[新規]** > **[ネットワーク]** > **[DNS ゾーン]** の順に選択して、**[DNS ゾーンの作成]** ページを開きます。

   ![[DNS ゾーン]](./media/dns-domain-delegation/dns.png)

1. **[DNS ゾーンの作成]** ページで次の値を入力してから、**[作成]** を選択します。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|contoso.net|DNS ゾーンの名前を指定します。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事を参照してください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの場所は、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="retrieve-name-servers"></a>ネーム サーバーの取得

DNS ゾーンを Azure DNS に委任するには、ゾーンのネーム サーバーを把握する必要があります。 Azure DNS は、ゾーンが作成されるたびに、プールからネーム サーバーを割り当てます。

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページで **contoso.net** DNS ゾーンを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**contoso.net**」と入力すると、アプリケーション ゲートウェイがすぐに見つかります。 

1. [DNS ゾーン] ページでネーム サーバーを取得します。 この例では、ゾーン contoso.net に、ネーム サーバー ns1-01.azure-dns.com、ns2-01.azure-dns.net、ns3-01.azure-dns.org、ns4-01.azure-dns.info が割り当てられています。

   ![ネーム サーバーの一覧](./media/dns-domain-delegation/viewzonens500.png)

割り当てられたネーム サーバーが含まれたゾーンに、権限のある NS レコードが自動的に作成されます。 Azure PowerShell または Azure CLI を使用してネーム サーバーを確認するには、これらのレコードを取得します。

以下の例は、PowerShell と Azure CLI を使用して Azure DNS ゾーンのネーム サーバーを取得する手順を示したものです。

### <a name="powershell"></a>PowerShell

```powershell
# The record name "@" is used to refer to records at the top of the zone.
$zone = Get-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -Zone $zone
```

次の例は応答です。

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
az network dns record-set list --resource-group contosoRG --zone-name contoso.net --type NS --name @
```

次の例は応答です。

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

## <a name="delegate-the-domain"></a>ドメインの委任

DNS ゾーンを作成してネーム サーバーを取得したところで、Azure DNS ネーム サーバーを使用して親ドメインを更新する必要があります。 各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。 レジストラーの DNS 管理ページで、NS レコードを編集し、その NS レコードを、Azure DNS で作成されたレコードに置き換えます。

ドメインを Azure DNS に委任する場合、Azure DNS によって提供されるネーム サーバーを使用する必要があります。 対象のドメインの名前に関係なく、4 つのネーム サーバーすべてを使用することをお勧めします。 ドメインの委任では、対象のドメインと同じトップレベル ドメインがネーム サーバーに使用される必要はありません。

Azure DNS ネーム サーバーの IP アドレスは後で変更される可能性があるため、これらの IP アドレスを指すために "*グルー レコード*" を使用しないでください。 独自のゾーンのネーム サーバー ("*バニティ ネーム サーバー*" と呼ばれることもあります) を使用した委任は、現在、Azure DNS ではサポートされていません。

## <a name="verify-that-name-resolution-is-working"></a>名前解決の動作確認

委任が完了したら、nslookup などのツールを使用してゾーンの SOA レコードを照会することで、名前解決が動作していることを確認できます  (SOA レコードは、ゾーンの作成時に自動的に作成されます)。

Azure DNS ネーム サーバーを指定する必要がありません。 委任が正しく設定されている場合は、通常の DNS 解決プロセスで自動的にネーム サーバーが検出されます。

```
nslookup -type=SOA contoso.com
```

前のコマンドから返される応答のサンプルを次に示します。

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

## <a name="delegate-subdomains-in-azure-dns"></a>Azure DNS でのサブドメインの委任

別の子ゾーンを設定する必要がある場合は、Azure DNS でサブドメインを委任できます。 たとえば、Azure DNS で contoso.net を設定して委任したとします。 次は、別の子ゾーン partners.contoso.net を設定する必要があります。

1. Azure DNS で子ゾーン partners.contoso.net を作成します。
2. 子ゾーンで権限のある NS レコードを検索し、Azure DNS で子ゾーンをホストするネーム サーバーを取得します。
3. 子ゾーンを指す親ゾーンで NS レコードを構成することで、子ゾーンを委任します。

### <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
1. **ハブ** メニューで、**[新規]** > **[ネットワーク]** > **[DNS ゾーン]** の順に選択して、**[DNS ゾーンの作成]** ページを開きます。

   ![[DNS ゾーン]](./media/dns-domain-delegation/dns.png)

1. **[DNS ゾーンの作成]** ページで次の値を入力してから、**[作成]** を選択します。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|partners.contoso.net|DNS ゾーンの名前を指定します。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成先となるサブスクリプションを選択します。|
   |**[リソース グループ]**|**[既存のものを使用]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

> [!NOTE]
> リソース グループの場所は、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

### <a name="retrieve-name-servers"></a>ネーム サーバーの取得

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページで **partners.contoso.net** DNS ゾーンを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**partners.contoso.net**」と入力すると、DNS ゾーンがすぐに見つかります。

1. [DNS ゾーン] ページでネーム サーバーを取得します。 この例では、ゾーン contoso.net に、ネーム サーバー ns1-01.azure-dns.com、ns2-01.azure-dns.net、ns3-01.azure-dns.org、ns4-01.azure-dns.info が割り当てられています。

   ![ネーム サーバーの一覧](./media/dns-domain-delegation/viewzonens500.png)

割り当てられたネーム サーバーが含まれたゾーンに、権限のある NS レコードが自動的に作成されます。  Azure PowerShell または Azure CLI を使用してネーム サーバーを確認するには、これらのレコードを取得します。

### <a name="create-a-name-server-record-in-the-parent-zone"></a>親ゾーンでのネーム サーバー レコードの作成

1. Azure Portal で **contoso.net** DNS ゾーンに移動します。
1. **+ [レコード セット]** を選択します。
1. **[レコード セットの追加]** ページで、次の値を入力し、**[OK]** を選択します。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|partners|子 DNS ゾーンの名前を入力します。|
   |**種類**|NS|ネーム サーバー レコードを表す NS を使用します。|
   |**TTL**|1|有効期限を入力します。|
   |**TTL の単位**|時間|有効期限の単位を時間に設定します。|
   |**ネーム サーバー**|<partners.contoso.net ゾーンから取得したネーム サーバー>|partners.contoso.net ゾーンから取得した 4 つのネーム サーバーをすべて入力します。 |

   ![ネーム サーバー レコードの値](./media/dns-domain-delegation/partnerzone.png)


### <a name="delegate-subdomains-in-azure-dns-by-using-other-tools"></a>その他のツールを使用した Azure DNS でのサブドメインの委任

次の例では、PowerShell と Azure CLI を使用して Azure DNS でサブドメインを委任する手順について説明します。

#### <a name="powershell"></a>PowerShell

これを PowerShell で行う例を次に示します。 同じ手順を、Azure Portal またはクロスプラットフォームの Azure CLI ツールを使用して完了できます。

```powershell
# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
$parent = New-AzureRmDnsZone -Name contoso.net -ResourceGroupName contosoRG
$child = New-AzureRmDnsZone -Name partners.contoso.net -ResourceGroupName contosoRG

# Retrieve the authoritative NS records from the child zone as shown in the next example. This information contains the name servers assigned to the child zone.
$child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

# Create the corresponding NS record set in the parent zone to complete the delegation. The record set name in the parent zone matches the child zone name (in this case, "partners").
$parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
$parent_ns_recordset.Records = $child_ns_recordset.Records
Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset
```

`nslookup` を使用して子ゾーンの SOA レコードを検索することで、すべてが正しく設定されていることを確認します。

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

# Create the parent and child zones. These can be in the same resource group or different resource groups, because Azure DNS is a global service.
az network dns zone create -g contosoRG -n contoso.net
az network dns zone create -g contosoRG -n partners.contoso.net
```

`partners.contoso.net` ゾーンのネーム サーバーを出力から取得します。

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

レコード セットと、各ネーム サーバーの NS レコードを作成します。

```azurecli
#!/bin/bash

# Create the record set
az network dns record-set ns create --resource-group contosorg --zone-name contoso.net --name partners

# Create an NS record for each name server.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns1-09.azure-dns.com.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns2-09.azure-dns.net.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns3-09.azure-dns.org.
az network dns record-set ns add-record --resource-group contosorg --zone-name contoso.net --record-set-name partners --nsdname ns4-09.azure-dns.info.
```

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したリソースをすべて削除するには、次の手順を実行します。

1. Azure ポータルの **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページで、**contosorg** リソース グループを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに「**contosorg**」と入力すると、リソース グループがすぐに見つかります。
1. **contosorg** ページで **[削除]** ボタンを選択します。
1. 削除の意図を確認するために、リソース グループの名前を入力するよう求められます。 リソース グループの名前として「**contosorg**」と入力し、**[削除]** を選択します。 

リソース グループを削除すると、リソース グループ内にあるすべてのリソースが削除されます。 必ず、削除する前にリソース グループの内容を確認するようにしてください。 リソース グループ内のすべてのリソースが削除され、次にリソース グループそのものが削除されます。 このプロセスには数分かかります。

## <a name="next-steps"></a>次の手順

[DNS ゾーンの管理](dns-operations-dnszones.md)

[DNS レコードの管理](dns-operations-recordsets.md)
