---
title: "PowerShell を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法について説明します。 PowerShell を使用したレコード セットとレコードの設定"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: a8068c5a-f248-4e97-be97-8bd0d79eeffd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f4c17d03ff637659a7bc7cde378878d8a4827b80
ms.openlocfilehash: 175e8620828a2b0a0aff6de0b1a39860ea59514b

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>PowerShell を使用した DNS レコード セットとレコードの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、Azure PowerShell を使用してレコードとレコード セットを作成する手順を説明します。

## <a name="introduction"></a>はじめに

Azure DNS で DNS レコードを作成する前に、まず、Azure DNS における DNS レコード セットでの DNS レコードの編成方法を理解する必要があります。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Azure DNS における DNS レコードの詳細については、「[DNS ゾーンとレコード](dns-zones-records.md)」を参照してください。

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

このセクションでは、Azure DNS に DNS レコードを作成する方法について説明します。 これらの例では、[Azure PowerShell のインストール、サインイン、DNS ゾーンの作成](dns-getstarted-create-dnszone.md)が既に完了していることを前提としています。

このページのすべての例では、DNS レコードの種類として "A" を使用します。 その他のレコードの種類の説明および DNS レコードとレコード セットの管理方法の詳細については、[PowerShell を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets.md)に関する記事を参照してください。

新しいレコードの名前と種類が既存のレコードと同じである場合は、[そのレコードを既存のレコード セットに追加する](#add-a-record-to-an-existing-record-set)必要があります。 新しいレコードの名前と種類が既存のすべてのレコードと異なる場合は、[新しいレコード セットを作成する](#create-records-in-a-new-record-set)必要があります。 

### <a name="create-records-in-a-new-record-set"></a>新しいレコード セットのレコードの作成

レコード セットは、`New-AzureRmDnsRecordSet` コマンドレットを使用して作成します。 レコード セットを作成する際には、レコード セット名、ゾーン、Time to Live (TTL)、レコードの種類、作成するレコードを指定する必要があります。

ゾーンの頂点 (この例では "contoso.com") にレコード セットを作成するには、レコード名 "@", (引用符を含みます) を使用します。 これは、一般的な DNS の規則です。

次の例では、DNS ゾーン "contoso.com" に相対名 "www" を持つ新しいレコード セットを作成します。 レコード セットの完全修飾名は、"www.contoso.com" になります。 レコードの種類は "A" で、TTL は 3,600 秒です。 レコード セットには、"1.2.3.4" という IP アドレスの 1 つのレコードが含まれています。

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4")
```

複数のレコードを含む新しいレコード セットを作成する必要がある場合は、まず、追加するレコードを含むローカル配列を作成する必要があります。  これは次のとおり `New-AzureRmDnsRecordSet` に渡されます。

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

### <a name="add-a-record-to-an-existing-record-set"></a>既存のレコード セットへのレコードの追加

既存のレコード セットにレコードを追加するには、次の次の 3 つの手順を実行します。

1. 既存のレコード セットの取得

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. ローカル レコード セットに新しいレコードを追加します。 これはオフライン操作です。

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Azure DNS サービスへの変更のコミットバック 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

### <a name="verify-name-resolution"></a>名前解決の確認

Azure DNS ネーム サーバーに DNS レコードが存在することをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、[ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する](dns-getstarted-create-dnszone.md#test-name-servers)必要があります。 次の例を実際のレコード ゾーンの正しい値に置き換えてください。

```
nslookup
> set type=A
> server ns1-01.azure-dns.com
> www.contoso.com

Server:  ns1-01.azure-dns.com
Address:  40.90.4.1

Name:    www.contoso.com
Address:  1.2.3.4
```

## <a name="next-steps"></a>次のステップ

[ドメイン名を Azure DNS ネーム サーバーに委任する](dns-domain-delegation.md)方法を確認します。

[PowerShell を使用して DNS ゾーンを管理する](dns-operations-dnszones.md)方法を確認します。

[PowerShell を使用して DNS レコードとレコード セットを管理する](dns-operations-recordsets.md)方法を確認します。





<!--HONumber=Dec16_HO3-->


