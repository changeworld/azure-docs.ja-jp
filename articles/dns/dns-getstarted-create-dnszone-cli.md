<properties
   pageTitle="CLI で Azure DNS の使用を開始する | Microsoft Azure"
   description="DNS ドメインのホストを開始するために CLI を使用して Azure DNS の DNS ゾーンを作成する手順について説明します。"
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
   ms.date="09/22/2015"
   ms.author="joaoma"/>

# CLI で Azure DNS の使用を開始する



> [AZURE.SELECTOR]
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)
- [Azure Powershell steps](dns-getstarted-create-dnszone.md)


ドメイン "contoso.com" には、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、多数の DNS レコードが含まれている場合があります。DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。

ドメインのホストを開始するには、まず DNS ゾーンを作成する必要があります。特定のドメイン用に作成された DNS レコードは、そのドメインの DNS ゾーン内に含まれます。

以下の手順では、Microsoft Azure CLI を使用します。Azure DNS コマンドを使用するために、必ず最新の Azure CLI に更新してください。

## Azure CLI の設定

### 手順 1.

Azure CLI をインストールします。Windows 用、Linux 用、MAC 用の Azure CLI をインストールできます。Azure CLI を使用して Azure DNS を管理するには、次の手順を完了しておく必要があります。詳しくは、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。CLI のすべてのネットワーク プロバイダー コマンドは、次のコマンドを使用して確認できます。

	Azure network


>[AZURE.IMPORTANT]DNS コマンドを使用するには、Azure CLI バージョン 0.9.8 以上が必要です。コンピューターに現在インストールされている Azure CLI のバージョンを確認するには、「`azure -v`」と入力します。
 
### 手順 2.

Azure DNS では、Azure リソース マネージャーを使用します。arm コマンドおよび DNS を使用するように CLI を切り替えます。

	Azure config mode arm

### 手順 3.

Azure アカウントにサインインします。

    Azure login -u "username"

資格情報を使用して認証を行うように求めるメッセージが表示されます。使用できるのは ORGID アカウントだけであることに留意してください。

### 手順 4.
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

タグは Etag とは異なります。タグは名前と値のペアのリストであり、Azure リソース マネージャーでは、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、[タグを使用した Azure リソースの整理](resource-group-using-tags.md)を参照してください。Azure DNS CLI では、ゾーンとレコード セットの両方でタグをサポートしており、オプションの "-Tag" パラメーターを使用して指定します。次の例では、'project = demo' と 'env = test' の 2 つのタグを持つ DNS ゾーンの作成方法を示します。

	Azure network dns zone create -n contoso.com -g myresourcegroup -t "project=demo";"env=test"

## DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。次の例では、"MyResourceGroup" というリソース グループに "contoso.com" という DNS ゾーンを作成します。

    Azure network dns zone create -n contoso.com -g myresourcegroup


>[AZURE.NOTE]Azure DNS では、ゾーン名を指定する際に末尾に "." を付けないでください。たとえば、"contoso.com." ではなく "contoso.com" とします。


これで、Azure DNS に DNS ゾーンが作成されました。DNS ゾーンを作成すると、次の DNS レコードも作成されます。

- 'Start of Authority' (SOA) レコード。このレコードは、すべての DNS ゾーンのルートに存在します。
- 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

これらのレコードを表示するには、"azure network dns-record-set show" を使用します。

	Usage: network dns record-set show <resource-group> <dns-zone-name> <name> <type>


次の例で、リソース グループ "myresourcegroup"、レコード セット名 "@" (ルート レコード)、タイプ "SOA" でコマンドを実行すると、次の出力が生成されます。
 

	azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
	info:    Executing command network dns-record-set show
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

	azure network dns record-set show myresourcegroup "contoso.com" "@" NS
	info:    Executing command network dns-record-set show
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

最初の DNS ゾーンを作成したところで、nslookup、DIG、**Resolve-DnsName** PowerShell コマンドレットなどの DNS ツールを使用してそのゾーンをテストできます。Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、上の "azure network dns-record-set show" でリスとされるように、NS レコードで与えられます。次のコマンドを実際のゾーンの正しい値に置き換えてください。

次の例では、DIG を使用し、DNS ゾーンに割り当てられたネーム サーバーを使用して、ドメイン contoso.com をクエリします。クエリでは、`@<name server for the zone>` で使用したネーム サーバーとゾーン名を、DIG を使用して指定する必要があります。

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


[レコード セットおよびレコード作成の概要](dns-getstarted-create-recordset-cli.md)<BR> [DNS ゾーンの管理方法](dns-operations-dnszones-cli.md)<BR> [DNS レコードの管理方法](dns-operations-recordsets-cli.md)<BR> [.NET SDK を使用した Azure の操作の自動化](dns-sdk.md)<BR> [Azure DNS REST API リファレンス](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Sept15_HO4-->