<properties 
   pageTitle="CLI を使用して Azure DNS の DNS レコード セットとレコードを管理する | Microsoft Azure" 
   description="Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。レコード セットとレコードに対する操作のための CLI コマンドをすべて紹介します。" 
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# CLI を使用して DNS レコードを管理する方法

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-recordsets-cli.md)
- [Azure Powershell](dns-operations-recordsets.md)

このガイドでは、DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](dns-getstarted-create-recordset.md#Understanding-record-sets-and-records)」を参照してください。

## レコード セットの作成

レコード セットは、`azure network dns record-set create` コマンドを使用して作成します。レコード セット名、ゾーン、Time-to-Live (TTL)、レコードの種類を指定する必要があります。

>[AZURE.NOTE]レコード セット名は、ゾーン名を除いた相対名にする必要があります。たとえば、"contoso.com" ゾーン内のレコード セット名 "www" によって、完全修飾名 "www.contoso.com" を持つレコード セットが作成されます。

>ゾーンの頂点のレコード セットの場合は、レコード セット名として "@" (引用符を含みます) を使用します。レコード セットの完全修飾名はゾーン名と同じになります。この場合は "contoso.com" です。

Azure DNS では、A、AAAA、CNAME、MX、NS、SOA、SRV、TXT というレコードの種類がサポートされています。SOA という種類のレコード セットは、各ゾーンと共に自動的に作成されるため、個別には作成できません。

	azure network dns record-set create myresourcegroup contoso.com  www  A --ttl 300


>[AZURE.IMPORTANT]CNAME レコード セットは、同じ名前を持つ他のレコード セットとは共存できません。たとえば、相対名 "www" を持つ CNAME と相対名 "www" を持つ A レコードを同時に作成することはできません。ゾーンの頂点 (名前は '@') には、ゾーンの作成時に作成された NS および SOA レコード セットが必ず含まれるため、ゾーンの頂点で CNAME レコード セットを作成することはできません。これらの制約は、DNS 標準から生じるものであり、Azure DNS の制限事項ではありません。

### ワイルドカード レコード

Azure DNS は、[ワイルドカード レコード](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をサポートしています。ワイルドカード レコードは、一致する名前を含むクエリに対してに返されます (非ワイルドカード レコード セットに、より近い一致がない場合)。

>[AZURE.NOTE]ワイルドカード レコード セットを作成するには、レコード セット名 "*" を使用するか、最初のラベルが "*" の名前 ("*.foo" など) を使用します。

>ワイルドカード レコード セットは、NS と SOA を除くすべてのレコードの種類でサポートされています。

## レコード セットの取得
既存のレコード セットを取得するには、`azure network dns record-set show` を使用し、リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類を指定します。

	azure network dns record-set show myresourcegroup contoso.com www A


## レコード セットの一覧を表示する

`azure network dns record-set list` コマンドを使用すると、DNS ゾーンの全レコードを一覧表示できます。

### 方法 1 
すべてのレコード セットの一覧を表示します。これにより、名前またはレコードの種類に関係なく、すべてのレコード セットが返されます。

	azure network dns record-set list myresourcegroup contoso.com

### 方法 2 

指定したレコードの種類のレコード セットの一覧を表示します。これにより、指定したレコードの種類 (この場合は A レコード) に一致するすべてのレコード セットが返されます。


	azure network dns record-set list myresourcegroup contoso.com A 

どちらの場合も、リソース グループ名とゾーン名を指定します。

## レコード セットへのレコードの追加

レコード セットにレコードを追加するには、`azure network dns record-set add-record` を使用します。

レコード セットにレコードを追加するためのパラメーターは、レコード セットの種類によって異なります。たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、"-a `<IPv4 address>`" パラメーターのみです。

次の例では、1 つのレコードを含む、各種のレコード セットを作成する方法を示します。

### 1 つのレコードを含む A レコード セットの作成

レコード セットを作成するには、`azure network dns record-set create` を使用し、リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類、有効期間 (TTL) を指定します。
	
	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300

>[AZURE.NOTE]--ttl パラメーターを定義しないと、値は既定で 4 (秒) になります。


A レコード セットを作成した後、`azure network dns record-set add-record` でレコード セットに IPv4 アドレスを追加します。

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1 

### 1 つのレコードを含む AAAA レコード セットの作成

	azure network dns record-set create myresourcegroup contoso.com "test-aaaa" AAAA --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-aaaa" AAAA -b "2607:f8b0:4009:1803::1005"

### 1 つのレコードを含む CNAME レコード セットの作成

	azure network dns record-set create -g myresourcegroup contoso.com  "test-cname" CNAME --ttl 300
	
	azure network dns record-set add-record  myresourcegroup contoso.com  test-cname CNAME -c "www.contoso.com"

>[AZURE.NOTE]CNAME レコードでは、単一の文字列値だけを使用できます。

### 1 つのレコードを含む MX レコード セットの作成

この例では、レコード セット名 "@" を使用してゾーンの頂点 ("contoso.com" など) に MX レコードを作成します。これは、MX レコードに共通です。

	azure network dns record-set create myresourcegroup contoso.com  "@"  MX --ttl 300

	azure network dns record-set add-record -g myresourcegroup contoso.com  "@" MX -e "mail.contoso.com" -f 5


### 1 つのレコードを含む NS レコード セットの作成

	azure network dns record-set create myresourcegroup contoso.com test-ns  NS --ttl 300
	
	azure network dns record-set add-record myresourcegroup  contoso.com  "test-ns" NS -d "ns1.contoso.com" 
	
### 1 つのレコードを含む SRV レコード セットの作成

ゾーンのルートに SRV レコードを作成する場合は、レコード名に \_サービスと \_プロトコルを指定するだけです。レコード名に "@" も含める必要はありません。

	
	azure network dns record-set create myresourcegroup contoso.com "_sip._tls" SRV --ttl 300 

	azure network dns record-set add-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 - w 5 -o 8080 -u "sip.contoso.com" 

### 1 つのレコードを含む TXT レコード セットの作成

	azure network dns record-set create myresourcegroup contoso.com "test-TXT" TXT --ttl 300

	azure network dns record-set add-record myresourcegroup contoso.com "test-txt" TXT -x "this is a TXT record" 


## 既存のレコード セットの変更


これを次の例で説明します。

### 既存のレコード セット内のレコードの更新

この例では、別の IP アドレス (1.2.3.4) を既存の A レコード セット (www) に追加します。

	azure network dns record-set add-record  myresourcegroup contoso.com  A
	-a 1.2.3.4
	info:    Executing command network dns record-set add-record
	Record set name: www
	+ Looking up the dns zone "contoso.com"
	+ Looking up the DNS record set "www"
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/a/www
	data:    Name                            : www	
	data:    Type                            : Microsoft.Network/dnszones/a
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:        IPv4 address                : 1.2.3.4
	data:
	info:    network dns record-set add-record command OK


レコード セットから既存の値を削除するには、`azure network dns record-set delete-record` を使用します。
 
	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 1.2.3.4
	info:    Executing command network dns record-set delete-record
	+ Looking up the DNS record set "www"
	Delete DNS record? [y/n] y
	+ Updating DNS record set "www"
	data:    Id                              : /subscriptions/################################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/A/www
	data:    Name                            : www
	data:    Type                            : Microsoft.Network/dnszones/A
	data:    Location                        : global
	data:    TTL                             : 4
	data:    A records:
	data:        IPv4 address                : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## 既存のレコード セットからのレコードの削除

レコード セットからレコードを削除するには、`azure network dns record-set delete-record` を使用します。削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。

レコード セットから最後のレコードを削除しても、レコード セットは削除されません。詳細については、以下の「[レコード セットの削除](#delete-a-record-set)」を参照してください。


	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### レコード セットからの AAAA レコードの削除

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### レコード セットからの CNAME レコードの削除

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com
	

### レコード セットからの MX レコードの削除

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### レコード セットからの NS レコードの削除
	
	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### レコード セットからの SRV レコードの削除

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com" 

### レコード セットからの TXT レコードの削除

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## レコード セットの削除
レコード セットは、Remove-AzureDnsRecordSet コマンドレットを使用して削除できます。

>[AZURE.NOTE]ゾーンの作成時に自動的に作成される、ゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンの削除時に自動的に削除されます。

次の例では、A レコード セット "test-a" を contoso.com DNS ゾーンから削除します。

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A 

オプションの "-q" スイッチを使用すると、確認プロンプトが表示されないように設定できます。


##関連項目

[レコード セットとレコードの作成の概要](dns-getstarted-create-recordset-cli.md)<BR> [DNS ゾーンに対する操作の実行](dns-operations-dnszones-cli.md)<BR> [.NET SDK を使用した操作の自動化](dns-sdk.md)
 

<!---HONumber=Sept15_HO4-->