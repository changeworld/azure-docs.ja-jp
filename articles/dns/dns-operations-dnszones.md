---
title: "PowerShell を使用して DNS ゾーンを管理する | Microsoft Docs"
description: "Azure Powershell を使用して DNS ゾーンを管理できます。 Azure DNS の DNS ゾーンを更新、削除、および作成する方法"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: f6403718f106b5021ea5b41e0e91a11bd92d4830

---
# <a name="how-to-manage-dns-zones-using-powershell"></a>PowerShell を使用して DNS ゾーンを管理する方法

> [!div class="op_single_selector"]
> * [Azure CLI](dns-operations-dnszones-cli.md)
> * [PowerShell](dns-operations-dnszones.md)

この記事では、PowerShell を使用して DNS ゾーンを管理する方法について説明します。 その手順を使用するには、Azure Resource Manager PowerShell コマンドレット (1.0 以降) の最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md) 」を参照してください。

## <a name="create-a-new-dns-zone"></a>新しい DNS ゾーンの作成

DNS ゾーンを作成する方法については、「 [Create a DNS zone by using PowerShell (PowerShell で Azure DNS ゾーンを作成する)](dns-getstarted-create-dnszone.md)」を参照してください。

## <a name="get-a-dns-zone"></a>DNS ゾーンの取得

DNS ゾーンを取得するには、 `Get-AzureRmDnsZone` コマンドレットを使用します。 この操作により、Azure DNS 内の既存のゾーンに対応する DNS ゾーン オブジェクトが返されます。 このオブジェクトには、ゾーンに関するデータ (レコード セットの数など) が含まれますが、レコード セット自体は含まれません。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup
```

## <a name="list-dns-zones"></a>DNS ゾーンの一覧表示

`Get-AzureRmDnsZone`からゾーン名を省略することで、リソース グループ内のすべてのゾーンを列挙できます。 この操作により、ゾーン オブジェクトの配列が返されます。

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

## <a name="update-a-dns-zone"></a>DNS ゾーンの更新

DNS ゾーンのリソースへの変更は、 `Set-AzureRmDnsZone`を使用して行うことができます。 これによって、ゾーン内の DNS レコード セットが更新されることはありません (「 [DNS レコードの管理方法](dns-operations-recordsets.md)」を参照)。 この操作は、ゾーンのリソース自体のプロパティを更新するためだけに使用します。 現時点では、この操作は、ゾーンのリソースの Azure リソース マネージャーの "タグ" に限定されています。 詳細については、「 [Etag とタグ](dns-getstarted-create-dnszone.md#tagetag) 」を参照してください。

DNS ゾーンを更新するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>ゾーン名とリソース グループを使用してゾーンを指定する

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

`Get-AzureRmDnsZone`の $zone オブジェクトを使用してゾーンを指定します。 $zone オブジェクトで `Set-AzureRmDnsZone` を使用すると、同時変更が上書きされないように Etag チェックが使用されます。 オプションの *-Overwrite* スイッチを使用して、これらのチェックが実行されないように指定できます。 詳細については、「 [Etag とタグ](dns-getstarted-create-dnszone.md#tagetag) 」を参照してください。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
<..modify $zone.Tags here...>
Set-AzureRmDnsZone -Zone $zone [-Overwrite]
```

## <a name="delete-a-dns-zone"></a>DNS ゾーンの削除

DNS ゾーンは、Remove-AzureRmDnsZone コマンドレットを使用して削除できます。

Azure DNS の DNS ゾーンを削除する前に、ゾーンの作成時に自動的にゾーンのルートに作成された NS レコードと SOA レコードを除くすべてのレコード セットを削除する必要があります。

DNS ゾーンを削除するには、次の 2 つの方法のいずれかを使用します。

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>ゾーン名とリソース グループ名を使用してゾーンを指定する

この操作には、オプションの `-Force` スイッチがあり、これを使用すると、DNS ゾーンを削除するかどうかの確認メッセージは表示されなくなります。

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]
```

### <a name="specify-the-zone-using-a-zone-object"></a>$zone オブジェクトを使用してゾーンを指定する

`Get-AzureRmDnsZone`の $zone オブジェクトを使用してゾーンを指定します。 この操作には、オプションの `-Force` スイッチがあり、これを使用すると、DNS ゾーンを削除するかどうかの確認メッセージは表示されなくなります。 `Set-AzureRmDnsZone` と同様に、$zone オブジェクトを使用してゾーンを指定すると、Etag チェックによって同時変更の削除を防止することができます。

オプションの `-Overwrite` フラグを使用すると、これらのチェックが行われなくなります。 詳細については、「 [Etag とタグ](dns-getstarted-create-dnszone.md#tagetag) 」を参照してください。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]
```

ゾーン オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]
```

## <a name="next-steps"></a>次のステップ

DNS ゾーンを作成したら、 [レコード セットとレコード](dns-getstarted-create-recordset.md) を作成し、インターネット ドメインの名前解決を開始します。




<!--HONumber=Nov16_HO3-->


