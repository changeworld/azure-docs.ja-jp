<properties
   pageTitle="Azure CLI を使用して Azure DNS の DNS レコード セットとレコードを管理する | Microsoft Azure"
   description="Azure DNS でドメインをホストする際に Azure DNS の DNS レコード セットとレコードを管理します。レコード セットとレコードに対する操作のための CLI コマンドをすべて紹介します。"
   services="dns"
   documentationCenter="na"
   authors="jtuliani"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="jtuliani"/>

# CLI を使用した DNS レコードとレコード セットの管理


> [AZURE.SELECTOR]
- [Azure ポータル](dns-operations-recordsets-portal.md)
- [Azure CLI](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)


この記事では、クロス プラットフォームの Azure コマンド ライン インターフェイス (CLI) を使用して DNS ゾーンのレコード セットとレコードを管理する方法について説明します。

DNS レコード セットと個々の DNS レコードの違いを理解することは重要です。レコード セットとは、1 つのゾーン内にある同じ名前、同じ種類のレコードのコレクションです。詳細については、「[レコード セットとレコードについて](dns-getstarted-create-recordset-cli.md)」を参照してください。


## クロスプラットフォーム Azure CLI を構成する

Azure DNS は、Azure リソース マネージャー専用のサービスです。Azure Service Management API はありません。`azure config mode arm` コマンドを使用して、リソース マネージャー モードを使用するように Azure CLI が構成されていることを確認する必要があります。

"**エラー: 'dns' は、Azure のコマンドではありません**" と表示される場合は、多くの場合、リソース マネージャー モードではなく、Azure Service Management モードで Azure CLI を使用していることが原因です。

## 新しいレコード セットとレコードを作成する

Azure ポータルで新しいレコード セットを作成する方法については、[レコード セットとレコードの作成に関するページ](dns-getstarted-create-recordset-cli.md)を参照してください。


## レコード セットを取得する

既存のレコード セットを取得するには、`azure network dns record-set show` を使用します。リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類を指定します。次のサンプルを使用し、自身の値に置き換えます。

	azure network dns record-set show myresourcegroup contoso.com www A


## レコード セットの一覧を表示する

`azure network dns record-set list` コマンドを使用すると、DNS ゾーンの全レコードを一覧表示できます。リソース グループ名とゾーン名を指定する必要があります。

### すべてのレコード セットを一覧表示するには

この例では、名前またはレコードの種類に関係なく、すべてのレコード セットが返されます。

	azure network dns record-set list myresourcegroup contoso.com

### 指定した種類のレコード セットを一覧表示するには

この例では、指定したレコードの種類 (この場合は "A" レコード) に一致するすべてのレコード セットが返されます。

	azure network dns record-set list myresourcegroup contoso.com A


## レコード セットへのレコードの追加

`azure network dns record-set add-record` コマンド使用して、レコードをレコード セットに追加します。レコード セットにレコードを追加するためのパラメーターは、セットとなるレコードの種類によって異なります。たとえば、"A" という種類のレコード セットを使用する場合、レコードの指定に使用できるのは、`-a <IPv4 address>` パラメーターのみです。

レコード セットを作成するには、`azure network dns record-set create` コマンドを使用します。リソース グループ、ゾーン名、レコード セットの相対名、レコードの種類、有効期間 (TTL) を指定します。`--ttl` パラメーターを定義しないと、値 (秒) は既定で 4 になります。

	azure network dns record-set create myresourcegroup  contoso.com "test-a"  A --ttl 300


"A" レコード セットを作成した後、`azure network dns record-set add-record` コマンドで IPv4 アドレスを追加します。

	azure network dns record-set add-record myresourcegroup contoso.com "test-a" A -a 192.168.1.1


次の例では、各種のレコード セットを作成する方法を示します。各レコード セットには、1 つのレコードが含まれています。

[AZURE.INCLUDE [dns-add-record-cli-include](../../includes/dns-add-record-cli-include.md)]


## レコード セット内のレコードを更新する

### 別の IP アドレス (1.2.3.4) を既存の "A" レコード セット (www) に追加するには

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

### レコード セットから既存の値を削除するには
`azure network dns record-set delete-record` を使用します。

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
	data:    IPv4 address                    : 192.168.1.1
	data:
	info:    network dns record-set delete-record command OK



## レコード セットからレコードを削除する

レコードは、`azure network dns record-set delete-record` を使用してレコード セットから削除できます。削除するレコードは、すべてのパラメーターにおいて既存のレコードと正確に一致する必要があります。

レコード セットから最後のレコードを削除しても、レコード セットは削除されません。詳細については、この記事の「[レコード セットを削除する](#delete)」セクションを参照してください。

	azure network dns record-set delete-record myresourcegroup contoso.com www A -a 192.168.1.1

	azure network dns record-set delete myresourcegroup contoso.com www A

### レコード セットから AAAA レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com test-aaaa  AAAA -b "2607:f8b0:4009:1803::1005"

### レコード セットから CNAME レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com test-cname CNAME -c www.contoso.com


### レコード セットから MX レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com "@" MX -e "mail.contoso.com" -f 5

### レコード セットから NS レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-ns" NS -d "ns1.contoso.com"

### レコード セットから PTR レコードを削除する
ここで ' my-arpa-zone.com' は IP 範囲を表す ARPA ゾーンを表します。このゾーンの各 PTR レコード セットは、この IP の範囲内の IP アドレスに対応します。

	azure network dns record-set delete-record myresourcegroup my-arpa-zone.com "10" PTR -P "myservice.contoso.com"

### レコード セットから SRV レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com  "_sip._tls" SRV -p 0 -w 5 -o 8080 -u "sip.contoso.com"

### レコード セットから TXT レコードを削除する

	azure network dns record-set delete-record myresourcegroup contoso.com  "test-TXT" TXT -x "this is a TXT record"

## <a name="delete"></a>レコード セットを削除する

レコード セットは `Remove-AzureRmDnsRecordSet` コマンドレットで削除できます。ゾーンの作成時に自動的に作成されたゾーンの頂点 (名前は "@") の SOA および NS レコード セットは削除できません。これらはゾーンが削除されると自動的に削除されます。

次の例では、"A" レコード セット "test-a" を "contoso.com" DNS ゾーンから削除します。

	azure network dns record-set delete myresourcegroup contoso.com  "test-a" A

オプションの *-q* スイッチを使用すると、確認プロンプトが表示されないように設定できます。


## 次のステップ

Azure DNS の詳細については、「[Azure DNS の概要](dns-overview.md)」を参照してください。DNS 作成の自動化については、「[.NET SDK を使用した DNS ゾーンとレコード セットの作成](dns-sdk.md)」を参照してください。

逆引き DNS レコードを使用する場合、「[Azure CLI を使用してサービスの逆引き DNS レコードを管理する方法](dns-reverse-dns-record-operations-cli.md)」を参照してください。

<!---HONumber=AcomDC_0928_2016-->