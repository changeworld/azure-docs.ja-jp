<properties 
   pageTitle="Azure DNS の DNS レコード セットとレコードの管理 | Microsoft Azure" 
   description="Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。レコード セットとレコードに対する操作のための PowerShell コマンドをすべて紹介します。" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="Adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="en"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# DNS レコードの管理方法

このガイドでは、DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](../dns-getstarted-create-recordset#Understanding-record-sets-and-records)」を参照してください。

## レコード セットの作成

レコード セットは、New-AzureDnsRecordSet コマンドレットを使用して作成します。レコード セット名、ゾーン、Time-to-Live (TTL)、レコードの種類を指定する必要があります。

>[AZURE.NOTE]レコード セット名は、ゾーン名を除いた相対名にする必要があります。たとえば、"contoso.com" ゾーン内のレコード セット名 "www" によって、完全修飾名 "www.contoso.com" を持つレコード セットが作成されます。

>ゾーンの頂点のレコード セットの場合は、レコード セット名として "@" (引用符を含みます) を使用します。レコード セットの完全修飾名はゾーン名と同じになります。この場合は "contoso.com" です。

Azure DNS では、A、AAAA、CNAME、MX、NS、SOA、SRV、TXT というレコードの種類がサポートされています。SOA という種類のレコード セットは、各ゾーンと共に自動的に作成されるため、個別には作成できません。

	PS C:\> $rs = New-AzureDnsRecordSet -Name www -Zone $zone -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

レコード セットが既に存在する場合は、-Overwrite スイッチを使用しないと、コマンドは失敗します。"-Overwrite" オプションによって確認プロンプトが表示されますが、これは -Force スイッチを使用して、表示されないように設定できます。

上の例では、Get-AzureDnsZone や New-AzureDnsZone によって返されるようなゾーン オブジェクトを使用して、ゾーンを指定しています。別の方法として、ゾーン名とリソース グループ名によってゾーンを指定することもできます。

	PS C:\> $rs = New-AzureDnsRecordSet -Name www –ZoneName contoso.com –ResourceGroupName MyAzureResourceGroup -RecordType A -Ttl 300 [-Tag $tags] [-Overwrite] [-Force]

New-AzureDnsRecordSet は、Azure DNS に作成されたレコード セットを表すローカル オブジェクトを返します。

>[AZURE.NOTE]CNAME レコード セットは、同じ名前を持つ他のレコード セットとは共存できません。たとえば、相対名 "www" を持つ CNAME と相対名 "www" を持つ A レコードを同時に作成することはできません。ゾーンの頂点 (名前は '@') には、ゾーンの作成時に作成された NS および SOA レコード セットが必ず含まれるため、ゾーンの頂点で CNAME レコード セットを作成することはできません。これらの制約は、DNS 標準から生じるものであり、Azure DNS の制限事項ではありません。

### ワイルドカード レコード
Azure DNS は、[ワイルドカード レコード](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をサポートしています。ワイルドカード レコードは、一致する名前を含むクエリに対してに返されます (非ワイルドカード レコード セットに、より近い一致がない場合)。

>[AZURE.NOTE]ワイルドカード レコード セットを作成するには、レコード セット名 "*" を使用するか、最初のラベルが "*" の名前 ("*.foo" など) を使用します。

>ワイルドカード レコード セットは、NS と SOA を除くすべてのレコードの種類でサポートされています。

## レコード セットの取得
既存のレコード セットを取得するには、"Get-AzureDnsRecordSet" を使用して、レコード セットの相対名、レコードの種類、およびゾーンを指定します。

	PS C:\> $rs = Get-AzureDnsRecordSet -Name www –RecordType A -Zone $zone

New-AzureDnsRecordSet と同様に、レコード名は相対名、つまりゾーン名を除いた名前にする必要があります。ゾーンは、ゾーン オブジェクト (上記)、またはゾーン名とリソース グループ名を使用して指定できます。

	PS C:\> $rs = Get-AzureDnsRecordSet –Name www –RecordType A -Zonename contoso.com -ResourceGroupName MyAzureResourceGroup

Get-AzureDnsRecordSet は、Azure DNS に作成されたレコード セットを表すローカル オブジェクトを返します。

## レコード セットの一覧を表示する
Get-AzureDnsRecordSet は、–Name パラメーターと –RecordType パラメーターの両方またはいずれかを省略することで、レコード セットの一覧表示にも使用できます。

### 方法 1 
すべてのレコード セットの一覧を表示します。これにより、名前またはレコードの種類に関係なく、すべてのレコード セットが返されます。

	PS C:\> $list = Get-AzureDnsRecordSet -Zone $zone
### 方法 2 

指定したレコードの種類のレコード セットの一覧を表示します。これにより、指定したレコードの種類 (この場合は A レコード) に一致するすべてのレコード セットが返されます。

	PS C:\> $list = Get-AzureDnsRecordSet –RecordType A -Zone $zone 

上記のどちらの場合も、ゾーンを指定するには、例に示したゾーン オブジェクトを使用するか、–ZoneName および –ResourceGroupName パラメーターを使用することができます。

## レコード セットへのレコードの追加
レコードは、Add-AzureDnsRecordConfig コマンドレットを使用してレコード セットに追加します。これはオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、"IPv4Address" パラメーターのみです。

レコードは、Add-AzureDnsRecordConfig の呼び出しを追加することで各レコード セットにさらに追加できます。すべてのレコード セットに対して、最大 100 個のレコードを追加できます。ただし、CNAME という種類のレコード セットに格納できるレコードは 1 つまでです。また、レコード セットに同一のレコードを 2 つ格納することはできません。空のレコード セット (レコードが 0 個) を作成することはできますが、Azure DNS ネーム サーバーには表示されません。

レコード セットに必要なレコードのコレクションを格納したら、Set-AzureDnsRecordSet コマンドレットを使用してそのレコード セットをコミットする必要があります。このコマンドレットは、Azure DNS 内の既存のレコード セットを、指定されたレコード セットに置き換えます。次の例では、1 つのレコードを含む、各種のレコード セットを作成する方法を示します。

### 1 つのレコードを含む A レコード セットの作成

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコードを作成するための一連の操作を "パイプ" することもできます。次に例を示します。

	PS C:\> New-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone -Ttl 60 | Add-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet

### 1 つのレコードを含む AAAA レコード セットの作成

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む CNAME レコード セットの作成

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-cname" -RecordType CNAME -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む MX レコード セットの作成
この例では、レコード セット名 "@" を使用してゾーンの頂点 ("contoso.com" など) に MX レコードを作成します。これは、MX レコードに共通です。

	PS C:\> $rs = New-AzureDnsRecordSet -Name "@" -RecordType MX -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む NS レコード セットの作成

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs
### 1 つのレコードを含む SRV レコード セットの作成

ゾーンのルートに SRV レコードを作成する場合は、レコード名に \_サービスと \_プロトコルを指定するだけです。レコード名に "@" も含める必要はありません。

	PS C:\> $rs = New-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### 1 つのレコードを含む TXT レコード セットの作成

	PS C:\> $rs = New-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone -Ttl 60
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## 既存のレコード セットの変更
既存のレコード セットの変更は、レコードの作成に似た手順に従って行います。一連の操作は次のとおりです。

1.	Get-AzureDnsRecordSet を使用して既存のレコード セットを取得します。
2.	レコードの追加、レコードの削除、レコード パラメーターの変更、またはレコード セットの TTL の変更によってレコード セットを変更します。これらの変更はオフライン操作です。レコード セットを表すローカル オブジェクトのみが変更されます。
3.	Set-AzureDnsRecordSet コマンドレットを使用して変更をコミットします。これにより、Azure DNS 内の既存のレコード セットは、指定したレコード セットに置き換えられます。

これを次の例で説明します。

### 既存のレコード セット内のレコードの更新
この例では、既存の A レコードの IP アドレスを変更します。

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-a" -RecordType A -Zone $zone 
	PS C:\> $rs.Records[0].Ipv4Address = "134.170.185.46"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

Set-AzureDnsRecordSet コマンドレットでは、同時変更が上書きされないように "etag" チェックが使用されます。これらのチェックが実行されないようにするには、"-Overwrite" フラグを使用します。詳細については、「Etag とタグ」を参照してください。

### SOA レコードの変更

>[AZURE.NOTE]ゾーンの頂点 (名前は "@") で自動的に作成された SOA レコード セットでレコードの追加または削除を実行することはできませんが、SOA レコード内のパラメーターとレコード セットの TTL は変更できます。

次の例では、SOA レコードの "Email" プロパティを変更する方法を示します。

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType SOA -Zone $zone
	PS C:\> $rs.Records[0].Email = "admin.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### ゾーンの頂点にある NS レコードの変更

>[AZURE.NOTE]ゾーンの頂点 (名前は "@") に自動的に作成された NS レコード セットのレコードを追加、削除、または変更することはできません。許可されている変更操作は、レコード セットの TTL の変更のみです。

次の例では、NS レコード セットの TTL プロパティを変更する方法を示します。

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "@" -RecordType NS -Zone $zone
	PS C:\> $rs.Ttl = 300
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

### 既存のレコード セットへのレコードの追加
この例では、2 つの MX レコードを既存のレコード セットに追加します。

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType MX -Zone $zone
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
	PS C:\> Add-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs 

## 既存のレコード セットからのレコードの削除

レコードは、Remove-AzureDnsRecordConfig を使用してレコード セットから削除できます。削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。変更は、Set-AzureDnsRecordSet を使用してコミットする必要があります。

レコード セットから最後のレコードを削除しても、レコード セットは削除されません。詳細については、以下の「[レコード セットの削除](#delete-a-record-set)」を参照してください。


	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-a" -RecordType A –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

レコード セット オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、レコード セットからレコードを削除するための一連の操作を "パイプ" することもできます。次に例を示します。

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureDnsRecordSet
### レコード セットからの AAAA レコードの削除

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-aaaa" -RecordType AAAA –Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### レコード セットからの CNAME レコードの削除

CNAME レコード セットに格納できるレコードは最大 1 つであるため、そのレコードを削除すると、空のレコード セットが残ります。

	PS C:\> $rs =  Get-AzureDnsRecordSet -name "test-cname" -RecordType CNAME –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### レコード セットからの MX レコードの削除

	PS C:\> $rs = Get-AzureDnsRecordSet -name "test-mx" -RecordType 'MX' –Zone $zone	
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### レコード セットからの NS レコードの削除
	
	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-ns" -RecordType NS -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### レコード セットからの SRV レコードの削除

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "_sip._tls" -RecordType SRV -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

### レコード セットからの TXT レコードの削除

	PS C:\> $rs = Get-AzureDnsRecordSet -Name "test-txt" -RecordType TXT -Zone $zone
	PS C:\> Remove-AzureDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
	PS C:\> Set-AzureDnsRecordSet -RecordSet $rs

## レコード セットの削除
レコード セットは、Remove-AzureDnsRecordSet コマンドレットを使用して削除できます。

>[AZURE.NOTE]ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンの削除時に自動的に削除されます。

レコード セットを削除するには、次の 3 つの方法のいずれかを使用します。
### 方法 1
名前ですべてのパラメーターを指定します。

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zonename "contoso.com" -ResourceGroupName MyAzureResourceGroup [-Force]
オプションの "-Force" スイッチを使用すると、確認プロンプトが表示されないように設定できます。

### 方法 2
名前と種類でレコード セットを指定し、オブジェクトでゾーンを指定します。

	PS C:\> Remove-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### 方法 3
オブジェクトでレコード セットを指定します。

	PS C:\> Remove-AzureDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

オブジェクトを使用してレコード セットを指定すると、"etag" チェックによって同時変更の削除を防止することができます。オプションの "-Overwrite" フラグを使用すると、これらのチェックが行われなくなります。詳細については、「[Etag とタグ](../dns-getstarted-create-dnszone#Etags-and-tags)」を参照してください。

レコード セット オブジェクトは、パラメーターとして渡す代わりに、パイプすることもできます。

	PS C:\> Get-AzureDnsRecordSet -Name "test-a" -RecordType A -Zone $zone | Remove-AzureDnsRecordSet [-Overwrite] [-Force]

##関連項目

[レコード セットとレコードの作成の概要](../dns-getstarted-create-recordset)<BR> [DNS ゾーンに対する操作の実行](../dns-operations-dnszones)<BR> [.NET SDK を使用した操作の自動化](../dns-sdk)
 

<!---HONumber=August15_HO6-->