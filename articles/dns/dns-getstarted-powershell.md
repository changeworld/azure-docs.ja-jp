---
title: クイック スタート - Azure PowerShell を使用して Azure DNS ゾーンとレコードを作成する
description: Azure DNS で、DNS ゾーンとレコードを作成する方法について説明します。 これは、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成して管理するための詳細なクイック スタートです。
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 839c97ccccbc1ce2cf646afcd27894a190eda1b0
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000893"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Azure DNS ゾーンとレコードを作成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このクイック スタートでは、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成します。 これらの手順は、[Azure portal](dns-getstarted-portal.md) または [Azure CLI](dns-getstarted-cli.md) を使用して実行することもできます。 

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

Azure DNS は、プライベート ドメインの作成もサポートします。 プライベート DNS ゾーンとレコードを初めて作成する方法に関する順を追った説明については、「[PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)」を参照してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzDnsZone` コマンドレットを使用して作成します。 次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```powershell
New-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>DNS レコードの作成

レコード セットは、`New-AzDnsRecordSet` コマンドレットを使用して作成します。 下の例では、リソース グループ "MyResourceGroup" で DNS ゾーン "contoso.com" に相対名 "www" を持つレコードを作成します。 レコード セットの完全修飾名は、"www.contoso.com" になります。 また、レコードの種類は "A"、IP アドレスは "1.2.3.4"、TTL は 3,600 秒です。

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4")
```

## <a name="view-records"></a>レコードの表示

ゾーンで DNS レコードを表示するには、次を使用します。

```powershell
Get-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyResourceGroup
```

## <a name="update-name-servers"></a>ネーム サーバーの更新

DNS ゾーンとレコードを正しく設定したら、Azure DNS ネーム サーバーを使用するようにドメイン名を構成する必要があります。 これにより、インターネット上の他のユーザーが DNS レコードを検索できるようになります。

ゾーンのネーム サーバーを指定するには、`Get-AzDnsZone` コマンドレットを使用します。

```powershell
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-b40d-0996b97ed101
Tags                  : {}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org., ns4-01.azure-dns.info.}
NumberOfRecordSets    : 3
MaxNumberOfRecordSets : 5000
```

このネーム サーバーは、ドメイン名レジストラー (ドメイン名を購入した場所) で構成する必要があります。 レジストラーにより、ドメインのネーム サーバーを設定するオプションが提供されます。 詳細については、「[チュートリアル: Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md#delegate-the-domain)」を参照してください。

## <a name="delete-all-resources"></a>すべてのリソースの削除

不要になった場合、リソース グループを削除することで、このクイック スタートで作成したすべてのリソースを削除できます。

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

これで、Azure PowerShell を使用して最初の DNS ゾーンとレコードを作成できました。カスタム ドメインで Web アプリのレコードを作成できます。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)

