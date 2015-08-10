<properties
   pageTitle="Azure DNS の概要 | Microsoft Azure"
   description="Azure DNS の DNS ゾーンを作成する方法について説明します。これは、PowerShell または CLI を使用して DNS ドメインのホストを開始するための最初の DNS ゾーンを作成する手順です。"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="joaoma"/>

# Azure DNS の概要

ドメイン "contoso.com" には、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、多数の DNS レコードが含まれている場合があります。DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。

ドメインのホストを開始するには、まず DNS ゾーンを作成する必要があります。特定のドメイン用に作成された DNS レコードは、そのドメインの DNS ゾーン内に含まれます。

以下の手順では、Microsoft Azure CLI を使用します。Azure DNS コマンドを使用するために、必ず最新の Azure CLI に更新してください。

## Azure CLI の設定

### 手順 1.

Azure CLI をインストールします。Windows 用、Linux 用、MAC 用の Azure CLI をインストールできます。Azure CLI を使用して Azure DNS を管理するには、次の手順を完了しておく必要があります。詳細については、「[Azure CLI のインストール](xplat-cli-install.md)」を参照してください。CLI のすべてのネットワーク プロバイダー コマンドは、次のコマンドを使用して確認できます。

	Azure network 

### 手順 2.

Azure DNS では、Azure リソース マネージャー (ARM) を使用します。ARM コマンドおよび DNS を使用するように CLI を切り替えます。

	Azure config mode arm

### 手順 3

Azure アカウントにログインします。

    Azure login -u "username"

資格情報を使用して認証を行うよう求められます。使用できるのは ORGID アカウントだけであることに留意してください。

### 手順 4
使用する Azure サブスクリプションを選択します。

    Azure account set "subscription name"

使用できるサブスクリプションのリストを表示するには、"azure account list" コマンドを使用します。

### 手順 5

新しいリソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

    Azure group create -n myresourcegroup --location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

### 手順 6

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。この操作は、サブスクリプションごとに 1 回だけ実行します。

	Azure provider register --namespace Microsoft.Network

## タグ

タグは Etag とは異なります。タグは名前と値のペアのリストであり、Azure リソース マネージャーでは、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、タグを使用した Azure リソースの整理に関するページを参照してください。Azure DNS CLI では、ゾーンとレコード セットの両方でタグをサポートしており、オプションの "-Tag" パラメーターを使用して指定します。次の例では、'project = demo' と 'env = test' の 2 つのタグを持つ DNS ゾーンの作成方法を示します。

	Azure network dns-zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## DNS ゾーンの作成

DNS ゾーンを作成するには、"azure network dns-zone create" コマンドを使用します。次の例では、"MyResourceGroup" というリソース グループに "contoso.com" という DNS ゾーンを作成します。

    Azure network dns-zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]Azure DNS では、ゾーン名を指定する際に末尾に '.' を付けないでください。たとえば、"contoso.com." ではなく "contoso.com" にします。


これで、Azure DNS に DNS ゾーンが作成されました。DNS ゾーンを作成すると、次の DNS レコードも作成されます。

\- "Start of Authority" (SOA) レコード。このレコードは、すべての DNS ゾーンのルートに存在します。 - 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、Azure DNS へのドメインの委任に関するページを参照してください。これらのレコードを表示するには、"azure network dns-record-set show" を使用します。
   
	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type SOA
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/SOA
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    SOA record:
	data:      Email                         : msnhst.microsoft.com
	data:      Expire time                   : 604800
	data:      Host                          : edge1.azuredns-cloud.net
	data:      Minimum TTL                   : 300
	data:      Refresh time                  : 900
	data:      Retry time                    : 300
	data:                                    :
<BR>作成される NS レコードを表示するには、次のコマンドを使用します。

	azure network dns-record-set show -g myresourcegroup --dns-zone "contoso.com" -n "@" --type NS
	info:    Executing command network dns-record-set show
	DNS zone name: contoso.com
	+ Looking up the DNS record set "@"
	data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
	data:    Name                            : @
	data:    Type                            : Microsoft.Network/dnszones/NS
	data:    Location                        : global
	data:    TTL                             : 3600
	data:    NS records
	data:        Name server domain name     : ns1-05.azure-dns.com
	data:        Name server domain name     : ns2-05.azure-dns.net
	data:        Name server domain name     : ns3-05.azure-dns.org
	data:        Name server domain name     : ns4-05.azure-dns.info
	data:
	info:    network dns-record-set show command OK

>[AZURE.NOTE]DNS ゾーンのルート (または '頂点') にあるレコード セットは、レコード セット名として "@" を使用します。

最初の DNS ゾーンを作成したところで、nslookup、dig、Resolve-DnsName PowerShell コマンドレットなどの DNS ツールを使用してそのゾーンをテストできます。Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、上の "azure network dns-record-set show" でリスとされるように、NS レコードで与えられます。次のコマンドを実際のゾーンの正しい値に置き換えてください。

次の例では、dig を使用し、DNS ゾーンに割り当てられたネーム サーバーを使用して、ドメイン contoso.com をクエリします。クエリでは、`@<name server for the zone>` を使用したネーム サーバーおよびゾーン名を、DIG を使用して指定する必要があります。

	 <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
	(1 server found)
	global options: +cmd
 	Got answer:
	->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
 	flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
 	WARNING: recursion requested but not available

 	OPT PSEUDOSECTION:
 	EDNS: version: 0, flags:; udp: 4000
  	QUESTION SECTION:
	contoso.com.                        IN      A

 	AUTHORITY SECTION:
	contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net. 
	msnhst.microsoft.com. 6 900 300 604800 300

	Query time: 93 msec
	SERVER: 208.76.47.5#53(208.76.47.5)
	WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
	MSG SIZE  rcvd: 120
	
## 次のステップ

<!---HONumber=July15_HO5-->