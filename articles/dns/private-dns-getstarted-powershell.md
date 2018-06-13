---
title: PowerShell で Azure DNS プライベート ゾーンの使用を開始する | Microsoft Docs
description: Azure DNS で、プライベート DNS ゾーンとレコードを作成する方法について説明します。 PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
ms.locfileid: "30177656"
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>PowerShell で Azure DNS プライベート ゾーンの使用を開始する

この記事では、Azure PowerShell を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  それらを "解決仮想ネットワーク" と呼ぶことにします。  VM が作成されたときや IP が変更されたとき、または VM が破棄されたときに絶えず Azure DNS によってホスト名レコードが維持される仮想ネットワークを指定することもできます。  それを "登録仮想ネットワーク" と呼ぶことにします。

# <a name="get-the-preview-powershell-modules"></a>プレビュー PowerShell モジュールの入手
以降の手順は、Private Zone 機能の必須モジュールがあることを含め、Azure PowerShell がインストール済みで、既にサインインしていることを前提としています。 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。 そのコマンドの例を次に示します。

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>DNS プライベート ゾーンの作成

`New-AzureRmDnsZone` コマンドレットを使用し、ZoneType パラメーターに "Private" という値を指定して DNS ゾーンを作成します。 次の例では、*MyResourceGroup* というリソース グループに *contoso.local* という DNS ゾーンを作成し、その DNS ゾーンを *MyAzureVnet* という仮想ネットワークで利用できるようにします。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

ZoneType パラメーターを省略した場合、そのゾーンはパブリック ゾーンとして作成されることに注意してください。そのため、プライベート ゾーンを作成する場合には、このパラメーターを必ず指定する必要があります。 

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

# <a name="list-dns-private-zones"></a>DNS プライベート ゾーンの一覧表示

`Get-AzureRmDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

`Get-AzureRmDnsZone` でゾーン名とリソース グループ名の両方を省略すると、Azure サブスクリプションにすべてのゾーンを列挙できます。

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>DNS プライベート ゾーンの更新

DNS ゾーンのリソースへの変更は、 `Set-AzureRmDnsZone`を使用して行うことができます。 このコマンドレットによって、ゾーン内の DNS レコード セットが更新されることはありません (「[DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現在、書き込み可能ゾーンのプロパティは、[ゾーンのリソースの Azure Resource Manager の "タグ"](dns-zones-records.md#tags) のほか、プライベート ゾーンの "RegistrationVirtualNetworkId" パラメーターと "ResolutionVirtualNetworkId" パラメーターに限定されています。

以下の例では、ゾーンにリンクされた登録仮想ネットワークを、新しい MyNewAzureVnet に置き換えています。

作成の場合とは異なり、更新の場合は ZoneType パラメーターの指定が不要であることに注意してください。 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

以下の例では、ゾーンにリンクされた解決仮想ネットワークを、"MyNewAzureVnet" という名前の新しいものに置き換えています。

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>DNS プライベート ゾーンの削除

DNS プライベート ゾーンを削除するには、パブリック ゾーンの場合と同様、`Remove-AzureRmDnsZone` コマンドレットを使用します。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 削除操作は元に戻すことができません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。

DNS ゾーンを削除するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>ゾーン名とリソース グループ名を使用してゾーンを指定する

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

削除するゾーンを指定するには、`Get-AzureRmDnsZone` によって返された `$zone` オブジェクトを使用します。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

ゾーン オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>確認のプロンプト

`New-AzureRmDnsZone`、`Set-AzureRmDnsZone`、および `Remove-AzureRmDnsZone` コマンドレットは、いずれも確認のプロンプトをサポートしています。

`New-AzureRmDnsZone` と `Set-AzureRmDnsZone` の両方が、`$ConfirmPreference` PowerShell 設定変数の値が `Medium` 以下である場合に、確認のプロンプトを表示します。 DNS ゾーン削除の影響は大きくなる可能性があるため、`Remove-AzureRmDnsZone` コマンドレットは、`$ConfirmPreference` PowerShell 変数の値が `None` 以外の場合に確認のプロンプトを表示します。

`$ConfirmPreference` の既定値は `High` であるため、既定では `Remove-AzureRmDnsZone` のみが確認のプロンプトを表示します。

現在の `$ConfirmPreference` 設定は `-Confirm` パラメーターを使用して上書きできます。 `-Confirm` または `-Confirm:$True` を指定した場合は、コマンドレットによって実行前に確認のプロンプトが表示されます。 `-Confirm:$False` を指定した場合は、コマンドレットによって確認のプロンプトが表示されません。

`-Confirm` と `$ConfirmPreference` の詳細については、「[About Preference Variables (設定変数について)](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables)」を参照してください。


## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したすべてのリソースを削除するには、次の手順を実行します。

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。

[プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関するページで、Azure DNS の Private Zones を使って実現できるいくつかの一般的なシナリオをご覧ください。

Azure DNS での DNS レコードの管理の詳細については、[PowerShell を使用した Azure DNS での DNS レコードおよびレコード セットの管理](dns-operations-recordsets.md)に関するページをご覧ください。

