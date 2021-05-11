---
title: Azure DNS での DNS ゾーンの管理 - PowerShell | Microsoft Docs
description: Azure Powershell を使用して DNS ゾーンを管理できます。 この記事では、Azure DNS で DNS ゾーンを更新、削除、および作成する方法について説明します
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 02c6518a1ccfaa678c42369ae22f67670aaf0566
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108203266"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell を使用して DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [ポータル](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure クラシック CLI](./dns-operations-dnszones-cli.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)

この記事では、Azure PowerShell を使用して DNS ゾーンを管理する方法について説明します。 DNS ゾーンは、クロスプラットフォームの [Azure CLI](dns-operations-dnszones-cli.md) または Azure Portal を使用して、管理することもできます。

このガイドでは、特にパブリック DNS ゾーンについて説明します。 Azure PowerShell を使用する Azure DNS での Private Zones の管理については、「[PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)」を参照してください。

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、 `New-AzDnsZone` コマンドレットを使用して作成します。

次の例では、*MyDNSResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

次の例では、*project = demo* と *env = test* の 2 つ [Azure Resource Manager タグ](dns-zones-records.md#tags)を含む DNS ゾーンの作成方法を示します。

```azurepowershell-interactive
New-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `Get-AzDnsZone` コマンドレットを使用します。 この操作により、Azure DNS 内の既存のゾーンに対応する DNS ゾーン オブジェクトが返されます。 このオブジェクトには、ゾーンに関するデータ (レコード セットの数など) が含まれますが、レコード セット自体は含まれません (`Get-AzDnsRecordSet` を参照)。

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com –ResourceGroupName MyDNSResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

`Get-AzDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```azurepowershell-interactive
$zoneList = Get-AzDnsZone -ResourceGroupName MyDNSResourceGroup
$zoneList
```

`Get-AzDnsZone` でゾーン名とリソース グループ名の両方を省略すると、Azure サブスクリプションにすべてのゾーンを列挙できます。

```azurepowershell-interactive
$zoneList = Get-AzDnsZone
$zoneList
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `Set-AzDnsZone`を使用して行うことができます。 このコマンドレットによって、ゾーン内の DNS レコード セットが更新されることはありません (「[DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現在、書き込み可能ゾーンのプロパティは、[ゾーンのリソースの Azure Resource Manager の "タグ"](dns-zones-records.md#tags) に限定されています。

DNS ゾーンを更新するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>ゾーン名とリソース グループを使用してゾーンを指定する

この手法では、既存のゾーン タグが、指定された値に置き換えられます。

```azurepowershell-interactive
Set-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

この手法では、既存のゾーン オブジェクトが取得された後、タグが変更されたうえで、変更がコミットされます。 この方法では既存のタグを保持できます。

```azurepowershell-interactive
# Get the zone object
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzDnsZone -Zone $zone
```

$zone オブジェクトで `Set-AzDnsZone` を使用すると、同時変更が上書きされないように [Etag チェック](dns-zones-records.md#etags)が使用されます。 オプションの `-Overwrite` スイッチを使用すると、これらのチェックを抑制できます。

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンを削除するには、`Remove-AzDnsZone` コマンドレットを使用します。

> [!NOTE]
> DNS ゾーンを削除すると、ゾーン内の DNS レコードもすべて削除されます。 この操作を元に戻すことはできません。 DNS ゾーンを使用している場合、ゾーンを削除すると、ゾーンを使用するサービスは機能しなくなります。
>
>ゾーンを誤って削除しないようにするには、「[DNS ゾーンとレコードを保護する](dns-protect-zones-recordsets.md)」をご覧ください。


DNS ゾーンを削除するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>ゾーン名とリソース グループ名を使用してゾーンを指定する

```azurepowershell-interactive
Remove-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

削除するゾーンを指定するには、`Get-AzDnsZone` によって返された `$zone` オブジェクトを使用します。

```azurepowershell-interactive
$zone = Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup
Remove-AzDnsZone -Zone $zone
```

ゾーン オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

```azurepowershell-interactive
Get-AzDnsZone -Name contoso.com -ResourceGroupName MyDNSResourceGroup | Remove-AzDnsZone

```

`Set-AzDnsZone` と同様に、`$zone` オブジェクトを使用してゾーンを指定すると、Etag チェックによって同時変更の削除を防止することができます。 これらのチェックを抑制するには、`-Overwrite` スイッチを使用します。

## <a name="confirmation-prompts"></a>確認のプロンプト

`New-AzDnsZone`、`Set-AzDnsZone`、および `Remove-AzDnsZone` コマンドレットは、いずれも確認のプロンプトをサポートしています。

`New-AzDnsZone` と `Set-AzDnsZone` の両方が、`$ConfirmPreference` PowerShell 設定変数の値が `Medium` 以下である場合に、確認のプロンプトを表示します。 DNS ゾーンを削除すると望ましくない状態が発生する可能性があるため、`Remove-AzDnsZone` コマンドレットは、`$ConfirmPreference` PowerShell 変数の値が `None` 以外の場合に確認のプロンプトを表示します。

`$ConfirmPreference` の既定値は `High` であるため、既定では `Remove-AzDnsZone` のみが確認のプロンプトを表示します。

現在の `$ConfirmPreference` 設定は `-Confirm` パラメーターを使用してオーバーライドできます。 `-Confirm` または `-Confirm:$True` を指定した場合は、コマンドレットによって実行前に確認のプロンプトが表示されます。 `-Confirm:$False` を指定した場合は、コマンドレットによって確認のプロンプトが表示されません。

`-Confirm` と `$ConfirmPreference` の詳細については、「[About Preference Variables (設定変数について)](/powershell/module/microsoft.powershell.core/about/about_preference_variables)」を参照してください。

## <a name="next-steps"></a>次のステップ

DNS ゾーンでレコード セットとレコードを管理する方法については[こちら](dns-operations-recordsets.md)をご覧ください。
<br>
Azure DNS にドメインを委任する方法については[こちら](dns-domain-delegation.md)をご覧ください。
<br>
[Azure DNS PowerShell のリファレンス ドキュメント](/powershell/module/Az.dns)を確認します。