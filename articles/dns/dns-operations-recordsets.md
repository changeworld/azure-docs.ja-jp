<properties 
   pageTitle="Azure DNS の DNS レコード セットとレコードの管理 | Microsoft Azure" 
   description="Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。レコード セットとレコードに対する操作のための PowerShell コマンドをすべて紹介します。" 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2016"
   ms.author="cherylmc"/>

# PowerShell を使用した DNS レコードとレコード セットの管理



> [AZURE.SELECTOR]
- [Azure ポータル](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



この記事では、Azure PowerShell を使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](dns-getstarted-create-recordset-portal.md)」を参照してください。

レコード セットとレコードを管理するには、Azure Resource Manager PowerShell コマンドレットの最新版をインストールする必要があります。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。PowerShell の使用方法の詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。



## 新しいレコード セットとレコードを作成する

Azure ポータルでレコード セットを作成する方法については、[Azure ポータルを使用した DNS レコードの作成に関するページ](dns-getstarted-create-recordset.md)を参照してください。

## レコード セットの取得

既存のレコード セットを取得するには、`Get-AzureRmDnsRecordSet` を使用します。レコード セットの相対名、レコードの種類、ゾーンを指定します。

	$rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

`New-AzureRmDnsRecordSet` と同様に、レコード セット名は、ゾーン名を除いた相対名にする必要があります。

ゾーンは、ゾーン名とリソース グループ名を使用して、またはゾーン オブジェクトを使用して、指定できます。

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
`Get-AzureRmDnsRecordSet` は、Azure DNS で作成されたレコード セットを表すローカル オブジェクトを返します。

## レコード セットの一覧を表示する

`Get-AzureRmDnsRecordSet` は、*–Name* パラメーターと *–RecordType* パラメーターの両方または一方を省略することで、レコード セットを一覧表示することもできます。

### すべてのレコード セットを一覧表示するには 

この例では、名前またはレコードの種類に関係なく、すべてのレコード セットが返されます。

	$list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### 指定したレコードの種類のレコード セットを一覧表示するには

この例では、レコードの種類に一致するすべてのレコード セットが返されます。この場合、種類が A のレコードです。

	$list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

ゾーンを指定するには、例に示した *–ZoneName* パラメーターと *–ResourceGroupName* パラメーターのいずれかを使用するか、ゾーン オブジェクトを使用できます。

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	$list = Get-AzureRmDnsRecordSet -Zone $zone

## レコード セットへのレコードの追加

レコード セットにレコードを追加するには、`Add-AzureRmDnsRecordConfig` コマンドレットを使用します。これはオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。たとえば、*A* という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、*IPv4Address* パラメーターのみです。

レコードは、`Add-AzureRmDnsRecordConfig` の呼び出しを追加することで各レコード セットにさらに追加できます。すべてのレコード セットに対して、最大 20 個のレコードを追加できます。ただし、*CNAME* という種類のレコード セットに格納できるレコードは 1 つまでです。また、レコード セットに同一のレコードを 2 つ格納することはできません。空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。

レコード セットに必要なレコードのコレクションが追加されたら、`Set-AzureRmDnsRecordSet` コマンドレットでコミットする必要があります。レコード セットがコミットされたら、Azure DNS 内の既存のレコード セットは、指定したレコード セットに置き換えられます。

### 1 つのレコードを含む A レコード セットを作成するには

	$rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコードを作成するための一連の操作を "パイプ" することもできます。次に例を示します。

	New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### その他のレコードの種類の例

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## 既存のレコード セットを変更する

既存のレコード セットの変更は、レコードの作成に似た手順に従って行います。一連の操作は次のとおりです。

1.	`Get-AzureRmDnsRecordSet` を使用して既存のレコードを取得します。

2.	レコードの追加、レコードの削除、レコード パラメーターの変更、またはレコード セットの TTL の変更によってレコード セットを変更します。これらの変更はオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。

3.	`Set-AzureRmDnsRecordSet` コマンドレットを使用して変更をコミットします。これにより、Azure DNS 内の既存のレコード セットは、指定したレコード セットに置き換えられます。


### 既存のレコード セット内のレコードを更新するには

この例では、既存の A レコードの IP アドレスを変更します。

	$rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Ipv4Address = "134.170.185.46"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

`Set-AzureRmDnsRecordSet` コマンドレットでは、同時変更が上書きされないように *etag* チェックが使用されます。これらのチェックが実行されないようにするには、*-Overwrite* フラグを使用します。詳細については、「[Etag とタグについて](dns-getstarted-creatednszone.md#tagetag)」を参照してください。

### SOA レコードを変更するには

ゾーンの頂点 (名前は "@") で自動的に作成された SOA レコード セットでレコードの追加または削除を実行することはできませんが、SOA レコード内のすべてのパラメーター ('Host' を除きます) とレコード セットの TTL は変更できます。

次の例では、SOA レコードの *Email* プロパティを変更する方法を示します。

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Records[0].Email = "admin.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### ゾーンの頂点にある NS レコードを変更するには

ゾーンの頂点 (名前は "@") に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。許可されている変更操作は、レコード セットの TTL の変更のみです。

次の例では、NS レコード セットの TTL プロパティを変更する方法を示します。

	$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	$rs.Ttl = 300
	Set-AzureRmDnsRecordSet -RecordSet $rs 

### 既存のレコード セットにレコードを追加するには

この例では、2 つの MX レコードを既存のレコード セットに追加します。

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	Set-AzureRmDnsRecordSet -RecordSet $rs 

## 既存のレコード セットからのレコードの削除

レコードは、`Remove-AzureRmDnsRecordConfig` を使用してレコード セットから削除できます。削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。変更は `Set-AzureRmDnsRecordSet` を使用してコミットする必要があります。

レコード セットから最後のレコードを削除しても、レコード セットは削除されません。詳細については、以下の「[レコード セットの削除](#delete-a-record-set)」を参照してください。


	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	Set-AzureRmDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコード セットからレコードを削除するための一連の操作を "パイプ" することもできます。次に例を示します。

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### レコード セットからの AAAA レコードの削除

	$rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの CNAME レコードの削除

CNAME レコード セットに格納できるレコードは最大 1 つであるため、そのレコードを削除すると、空のレコード セットが残ります。

	$rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの MX レコードの削除

	$rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの NS レコードの削除
	
	$rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの SRV レコードの削除

	$rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの TXT レコードの削除

	$rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	Set-AzureRmDnsRecordSet -RecordSet $rs

## レコード セットの削除

レコード セットは `Remove-AzureRmDnsRecordSet` コマンドレットで削除できます。ゾーンの作成時に自動的に作成されたゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンが削除されると自動的に削除されます。

レコード セットを削除するには、次の 3 つの方法のいずれかを使用します。

### 名前ですべてのパラメーターを指定します。

オプションの *-Force* スイッチを使用すると、確認プロンプトが表示されないように設定できます。

	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### 名前と種類でレコード セットを指定し、オブジェクトでゾーンを指定します。

	$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### オブジェクトでレコード セットを指定します。

	$rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

オブジェクトを使用してレコード セットを指定すると、etag チェックによって同時変更の削除を防止することができます。オプションの *-Overwrite* フラグを使用すると、これらのチェックが行われなくなります。詳細については、「[Etag とタグ](dns-getstarted-create-dnszone.md#tagetag)」を参照してください。

レコード セット オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

	Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## 次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。DNS 作成の自動化については、「[.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。

逆引き DNS レコードを使用する場合、「[逆引き DNS レコードを管理する方法](dns-reverse-dns-record-operations-ps.md)」を参照してください。
 

<!---HONumber=AcomDC_0518_2016-->