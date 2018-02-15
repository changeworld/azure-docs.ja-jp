---
title: "PowerShell で Azure DNS プライベート ゾーンの使用を開始する | Microsoft Docs"
description: "Azure DNS で、プライベート DNS ゾーンとレコードを作成する方法について説明します。 PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。"
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: 4613e152336eda7ce7cdc4c44b0c6b5e96abac10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>PowerShell で Azure DNS プライベート ゾーンの使用を開始する

この記事では、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  それらを "解決ネットワーク" と呼ぶことにします。  VM が作成されたときや IP が変更されたとき、または VM が破棄されたときに絶えず Azure DNS によってホスト名レコードが維持される一連の仮想ネットワークを指定することもできます。  それらを "登録ネットワーク" と呼ぶことにします。

この機能は管理プレビュー中です。[AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) 宛てにメールすると、プレビューの PowerShell モジュールが提供されます。

## <a name="get-the-preview-powershell-module"></a>プレビュー PowerShell モジュールの入手

PowerShell を使用してプライベート DNS ゾーンを作成するには、[AzureDNS-PrivateZone@microsoft.com](mailto:AzureDNS-PrivateZone@microsoft.com) 宛てにメールを送信して、プレビュー版の PowerShell モジュールを入手してください。

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。 コマンドを次に示します。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzureRmDnsZone` コマンドレットを使用して作成します。 次の例では、*MyResourceGroup* というリソース グループに *contoso.local* という DNS ゾーンを作成し、その DNS ゾーンを *MyAzureVnet* という仮想ネットワークで利用できるようにします。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

このゾーンにホスト名レコードを Azure で自動的に作成する必要がある場合は、*ResolutionVirtualNetworkId* ではなく *RegistrationVirtualNetworkId* パラメーターを使用してください。  登録仮想ネットワークの解決が自動的に有効になります。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>DNS レコードの作成

レコード セットは、`New-AzureRmDnsRecordSet` コマンドレットを使用して作成します。 下の例では、リソース グループ "MyResourceGroup" の DNS ゾーン "contoso.local" に、相対名が "db" のレコードを作成します。 レコード セットの完全修飾名は、"db.contoso.local" になります。 また、レコードの種類は "A"、IP アドレスは "10.0.0.4"、TTL は 3,600 秒です。

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

その他のレコードの種類、複数のレコードを持つレコード セット、既存のレコードの変更については、[Azure PowerShell を使用した DNS レコードおよびレコード セットの管理](dns-operations-recordsets.md)に関するページをご覧ください。 


## <a name="view-records"></a>レコードの表示

ゾーンで DNS レコードを表示するには、次を使用します。

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したすべてのリソースを削除するには、次の手順を実行します。

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。

Azure DNS での DNS レコードの管理の詳細については、[PowerShell を使用した Azure DNS での DNS レコードおよびレコード セットの管理](dns-operations-recordsets.md)に関するページをご覧ください。

