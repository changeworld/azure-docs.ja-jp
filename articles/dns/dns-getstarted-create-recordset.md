---
title: "PowerShell を使用して DNS ゾーンのレコード セットとレコードを作成する | Microsoft Docs"
description: "Azure DNS のホスト レコードを作成する方法。PowerShell を使用したレコード セットとレコードの設定"
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
ms.date: 08/16/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 02d720a04fdc0fa302c2cb29b0af35ee92c14b3b
ms.openlocfilehash: a6b72b96c241e3d459abad0d3986f2d757cf1752

---

# <a name="create-dns-record-sets-and-records-by-using-powershell"></a>PowerShell を使用した DNS レコード セットとレコードの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](dns-getstarted-create-recordset-portal.md)
> * [PowerShell](dns-getstarted-create-recordset.md)
> * [Azure CLI](dns-getstarted-create-recordset-cli.md)

この記事では、Windows PowerShell を使用してレコードとレコード セットを作成する手順を説明します。 DNS ゾーンを作成した後は、ドメインの DNS レコードを追加します。 そのために、まずは DNS レコードとレコード セットについて理解することが必要です。

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

## <a name="verify-that-you-have-the-latest-version-of-powershell"></a>最新バージョンの PowerShell を使用していることを確認する

Azure Resource Manager PowerShell コマンドレットの最新版がインストールされていることを確認します。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md) 」を参照してください。

## <a name="create-a-record-set-and-record"></a>レコード セットとレコードの作成

このセクションでは、レコード セットとレコードの作成方法を説明します。

### <a name="1-connect-to-your-subscription"></a>1.サブスクリプションへの接続

PowerShell コンソールを開き、アカウントに接続します。 接続するには、次のサンプルを参照してください。

```powershell
Login-AzureRmAccount
```

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

PowerShell の使用方法の詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

### <a name="2-create-a-record-set"></a>2.レコード セットの作成

レコード セットは、`New-AzureRmDnsRecordSet` コマンドレットを使用して作成します。 レコード セットを作成するとき、レコード セット名、ゾーン、time to live (TTL)、レコードの種類を指定する必要があります。

ゾーンの頂点 (この例では "contoso.com") にレコード セットを作成するには、レコード名 "@", (引用符を含みます) を使用します。 これは、一般的な DNS の規則です。

下の例では、DNS ゾーン "contoso.com" に相対名 "www" を持つレコード セットを作成します。 レコード セットの完全修飾名は、"www.contoso.com" になります。 レコードの種類は"A" で、TTL は 60 秒です。 この手順を完了すると、空の "www" レコード セットが作成され、変数 *$rs*に割り当てられます。

```powershell
$rs = New-AzureRmDnsRecordSet -Name "www" -RecordType "A" -ZoneName "contoso.com" -ResourceGroupName "MyAzureResourceGroup" -Ttl 60
```

#### <a name="if-a-record-set-already-exists"></a>レコード セットが既に存在する場合

レコード セットが既に存在する場合、 *-Overwrite* スイッチを使用しないと、コマンドは失敗します。 *-Overwrite* オプションによって確認プロンプトが表示されますが、これは *-Force* スイッチを使用して、表示されないように設定できます。

```powershell
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]
```

この例では、ゾーン名とリソース グループ名を使用してゾーンを指定します。 あるいは、`Get-AzureRmDnsZone` または `New-AzureRmDnsZone` で返されるゾーン オブジェクトを指定できます。

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
$rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -Zone $zone [-Tag $tags] [-Overwrite] [-Force]
```

`New-AzureRmDnsRecordSet` は、Azure DNS で作成されたレコード セットを表すローカル オブジェクトを返します。

### <a name="3-add-a-record"></a>3.レコードの追加

新しく作成した "www" レコード セットを使用するには、レコードを追加する必要があります。 次の例を使用して、IPv4 *A* レコードを "www" レコード セットに追加できます。 この例は、前の手順で設定した変数の *$rs* に依存しています。

`Add-AzureRmDnsRecordConfig` を使用してレコード セットにレコードを追加する操作はオフライン操作です。 ローカル変数 *$rs* のみが更新されます。

```powershell
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.185.46
Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address 134.170.188.221
```

### <a name="4-commit-the-changes"></a>4.変更をコミットする

レコード セットへの変更をコミットします。 レコード セットへの変更を Azure DNS にアップロードするには、`Set-AzureRmDnsRecordSet` を使用します。

```powershell
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="5-retrieve-the-record-set"></a>5.レコード セットを取得する

次の例のように `Get-AzureRmDnsRecordSet` を使用し、Azure DNS からレコード セットを取得できます。

    Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup


    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : MyAzureResourceGroup
    Ttl               : 3600
    Etag              : 68e78da2-4d74-413e-8c3d-331ca48246d9
    RecordType        : A
    Records           : {134.170.185.46, 134.170.188.221}
    Tags              : {}


nslookup ツールや他の DNS ツールを使用して、新しいレコード セットを照会することもできます。

まだドメインを Azure DNS ネーム サーバーに委任していない場合、ゾーンの名前、サーバー、およびアドレスを明示的に指定する必要があります。

    nslookup www.contoso.com ns1-01.azure-dns.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    Name:    www.contoso.com
    Addresses:  134.170.185.46
                134.170.188.221

## <a name="create-a-record-set-of-each-type-with-a-single-record"></a>1 つのレコードを含む各種のレコード セットを作成する

次の例では、各種のレコード セットを作成する方法を示します。 各レコード セットには、1 つのレコードが含まれています。

[!INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="next-steps"></a>次のステップ

[PowerShell を使用して DNS ゾーンを管理する方法](dns-operations-dnszones.md)

[PowerShell を使用した DNS レコードとレコード セットの管理](dns-operations-recordsets.md)

[.NET SDK を使用した Azure の操作の自動化](dns-sdk.md)



<!--HONumber=Nov16_HO3-->


