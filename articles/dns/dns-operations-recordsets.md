<properties 
   pageTitle="Azure DNS の DNS レコード セットとレコードの管理 | Microsoft Azure" 
   description="Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。レコード セットとレコードに対する操作のための PowerShell コマンドをすべて紹介します。" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmon" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="03/04/2016"
   ms.author="joaoma"/>

# PowerShell を使用して DNS レコードを管理する方法



> [AZURE.SELECTOR]
- [Azure ポータル](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



このガイドでは、Azure PowerShell を使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](../dns-getstarted-create-recordset#Understanding-record-sets-and-records)」を参照してください。

## レコード セットの作成

レコード セットは、New-AzureRmDnsRecordSet コマンドレットを使用して作成します。レコード セット名、ゾーン、Time-to-Live (TTL)、レコードの種類を指定する必要があります。

レコード セット名は、ゾーン名を除いた相対名にする必要があります。たとえば、"contoso.com" ゾーン内のレコード セット名 "www" によって、完全修飾名 "www.contoso.com" を持つレコード セットが作成されます。

ゾーンの頂点のレコード セットの場合は、レコード セット名として "@" (引用符を含みます) を使用します。レコード セットの完全修飾名はゾーン名と同じになります。この場合は "contoso.com" です。

Azure DNS では、A、AAAA、CNAME、MX、NS、SOA、SRV、TXT というレコードの種類がサポートされています。SOA という種類のレコード セットは、各ゾーンと共に自動的に作成されるため、個別には作成できません。

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 -ZoneName contoso.com -ResouceGroupName MyAzureResouceGroup [-Tag $tags] [-Overwrite] [-Force]

レコード セットが既に存在する場合は、-Overwrite スイッチを使用しないと、コマンドは失敗します。"-Overwrite" オプションによって確認プロンプトが表示されますが、これは -Force スイッチを使用して、表示されないように設定できます。

上の例では、ゾーンはゾーン名とリソース グループ名を使用して指定されています。または、Get-AzureRmDnsZone や New-AzureRmDnsZone によって返されるように、ゾーン オブジェクトを指定することもできます。

	PS C:\> $zone = Get-AzureRmDnsZone -ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs = New-AzureRmDnsRecordSet -Name www -RecordType A -Ttl 300 –Zone $zone [-Tag $tags] [-Overwrite] [-Force]

New-AzureRmDnsRecordSet は、Azure DNS に作成されたレコード セットを表すローカル オブジェクトを返します。

>[AZURE.IMPORTANT] CNAME レコード セットは、同じ名前を持つ他のレコード セットとは共存できません。たとえば、相対名 "www" を持つ CNAME と相対名 "www" を持つ A レコードを同時に作成することはできません。ゾーンの頂点 (名前は '@') には、ゾーンの作成時に作成された NS および SOA レコード セットが必ず含まれるため、ゾーンの頂点で CNAME レコード セットを作成することはできません。これらの制約は、DNS 標準から生じるものであり、Azure DNS の制限事項ではありません。

### ワイルドカード レコード

Azure DNS は、[ワイルドカード レコード](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をサポートしています。ワイルドカード レコードは、一致する名前を含むクエリに対してに返されます (非ワイルドカード レコード セットに、より近い一致がない場合)。

ワイルドカード レコード セットを作成するには、レコード セット名 "*" を使用するか、最初のラベルが "*" の名前 ("*.foo" など) を使用します。

ワイルドカード レコード セットは、NS と SOA を除くすべてのレコードの種類でサポートされています。

## レコード セットの取得

既存のレコード セットを取得するには、"Get-AzureRmDnsRecordSet" を使用して、レコード セットの相対名、レコードの種類、およびゾーンを指定します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

New-AzureRmDnsRecordSet と同様に、レコード名は相対名、つまりゾーン名を除いた名前にする必要があります。ゾーンは、ゾーン名とリソース グループ名を使用して、またはゾーン オブジェクトを使用して、指定できます。

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone
	
Get-AzureRmDnsRecordSet は、Azure DNS に作成されたレコード セットを表すローカル オブジェクトを返します。

## レコード セットの一覧を表示する
Get-AzureRmDnsRecordSet は、–Name パラメーターと –RecordType パラメーターの両方またはいずれかを省略することで、レコード セットの一覧表示にも使用できます。

### 方法 1 

すべてのレコード セットの一覧を表示します。これにより、名前またはレコードの種類に関係なく、すべてのレコード セットが返されます。

	PS C:\> $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### 方法 2 

指定したレコードの種類のレコード セットの一覧を表示します。これにより、指定したレコードの種類 (この場合は A レコード) に一致するすべてのレコード セットが返されます。

	PS C:\> $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 

上記のどちらのオプションでも、ゾーンを指定するには、例に示した –ZoneName および –ResourceGroupName パラメーターを使用するか、またはゾーン オブジェクトを使用できます。

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
	PS C:\> $list = Get-AzureRmDnsRecordSet -Zone $zone

## レコード セットへのレコードの追加

レコードは、Add-AzureRmDnsRecordConfig コマンドレットを使用してレコード セットに追加します。これはオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、"IPv4Address" パラメーターのみです。

レコードは、Add-AzureRmDnsRecordConfig の呼び出しを追加することで各レコード セットにさらに追加できます。すべてのレコード セットに対して、最大 20 個のレコードを追加できます。ただし、CNAME という種類のレコード セットに格納できるレコードは 1 つまでです。また、レコード セットに同一のレコードを 2 つ格納することはできません。空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。

レコード セットに必要なレコードのコレクションを格納したら、Set-AzureRmDnsRecordSet コマンドレットを使用してそのレコード セットをコミットする必要があります。このコマンドレットは、Azure DNS 内の既存のレコード セットを、指定されたレコード セットに置き換えます。次の例では、1 つのレコードを含む、各種のレコード セットを作成する方法を示します。

### 1 つのレコードを含む A レコード セットの作成

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup 
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコードを作成するための一連の操作を "パイプ" することもできます。次に例を示します。

	PS C:\> New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### 1 つのレコードを含む AAAA レコード セットの作成

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む CNAME レコード セットの作成

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む MX レコード セットの作成

この例では、レコード セット名 "@" を使用してゾーンの頂点 ("contoso.com" など) に MX レコードを作成します。これは、MX レコードに共通です。

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "@" -RecordType MX -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む NS レコード セットの作成

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む SRV レコード セットの作成

ゾーンのルートに SRV レコードを作成する場合は、レコード名に \_サービスと \_プロトコルを指定するだけです。レコード名に "@" も含める必要はありません。

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む TXT レコード セットの作成

	PS C:\> $rs = New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## 既存のレコード セットの変更

既存のレコード セットの変更は、レコードの作成に似た手順に従って行います。一連の操作は次のとおりです。

1.	Get-AzureRmDnsRecordSet を使用して既存のレコード セットを取得します。
2.	レコードの追加、レコードの削除、レコード パラメーターの変更、またはレコード セットの TTL の変更によってレコード セットを変更します。これらの変更はオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。
3.	Set-AzureRmDnsRecordSet コマンドレットを使用して変更をコミットします。これにより、Azure DNS 内の既存のレコード セットは、指定したレコード セットに置き換えられます。

これを次の例で説明します。

### 既存のレコード セット内のレコードの更新

この例では、既存の A レコードの IP アドレスを変更します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

Set-AzureRmDnsRecordSet コマンドレットでは、同時変更が上書きされないように "etag" チェックが使用されます。これらのチェックが実行されないようにするには、"-Overwrite" フラグを使用します。詳細については、「Etag とタグ」を参照してください。

### SOA レコードの変更

>[AZURE.NOTE] ゾーンの頂点 (名前は "@") で自動的に作成された SOA レコード セットでレコードの追加または削除を実行することはできませんが、SOA レコード内のすべてのパラメーター ('Host' を除きます) とレコード セットの TTL は変更できます。

次の例では、SOA レコードの "Email" プロパティを変更する方法を示します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### ゾーンの頂点にある NS レコードの変更

>[AZURE.NOTE] ゾーンの頂点 (名前は "@") に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。許可されている変更操作は、レコード セットの TTL の変更のみです。

次の例では、NS レコード セットの TTL プロパティを変更する方法を示します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

### 既存のレコード セットへのレコードの追加

この例では、2 つの MX レコードを既存のレコード セットに追加します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs 

## 既存のレコード セットからのレコードの削除

レコードは、Remove-AzureRmDnsRecordConfig を使用してレコード セットから削除できます。削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。変更は、Set-AzureRmDnsRecordSet を使用してコミットする必要があります。

レコード セットから最後のレコードを削除しても、レコード セットは削除されません。詳細については、以下の「[レコード セットの削除](#delete-a-record-set)」を参照してください。


	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコード セットからレコードを削除するための一連の操作を "パイプ" することもできます。次に例を示します。

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### レコード セットからの AAAA レコードの削除

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの CNAME レコードの削除

CNAME レコード セットに格納できるレコードは最大 1 つであるため、そのレコードを削除すると、空のレコード セットが残ります。

	PS C:\> $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの MX レコードの削除

	PS C:\> $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup	
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの NS レコードの削除
	
	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの SRV レコードの削除

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

### レコード セットからの TXT レコードの削除

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureRmDnsRecordSet -RecordSet $rs

## レコード セットの削除
レコード セットは、Remove-AzureRmDnsRecordSet コマンドレットを使用して削除できます。

>[AZURE.NOTE] ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンの削除時に自動的に削除されます。

レコード セットを削除するには、次の 3 つの方法のいずれかを使用します。

### 方法 1

名前ですべてのパラメーターを指定します。

	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

オプションの "-Force" スイッチを使用すると、確認プロンプトが表示されないように設定できます。

### 方法 2

名前と種類でレコード セットを指定し、オブジェクトでゾーンを指定します。

	PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 方法 3

オブジェクトでレコード セットを指定します。

	PS C:\> $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

オブジェクトを使用してレコード セットを指定すると、"etag" チェックによって同時変更の削除を防止することができます。オプションの "-Overwrite" フラグを使用すると、これらのチェックが行われなくなります。詳細については、「[Etag とタグ](../dns-getstarted-create-dnszone#Etags-and-tags)」を参照してください。

レコード セット オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

	PS C:\> Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

##関連項目

[レコード セットとレコードの作成の概要](dns-getstarted-create-recordset.md)<BR> [DNS ゾーンの管理](dns-operations-dnszones.md)<BR> [.NET SDK を使用した操作の自動化](dns-sdk.md)
 

<!---HONumber=AcomDC_0406_2016-->