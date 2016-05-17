<properties
   pageTitle="CLI を使用した DNS ゾーンの作成 | Microsoft Azure"
   description="DNS ドメインのホストを開始するために CLI を使用して Azure DNS の DNS ゾーンを作成する手順について説明します。"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# CLI を使用した Azure DNS ゾーンの作成


> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


この記事では、CLI を使用して DNS ゾーンを作成する手順を説明します。DNS ゾーンは、PowerShell または Azure ポータルを使用して作成することもできます。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


## 開始する前に

以下の手順では、Microsoft Azure CLI を使用します。Azure DNS コマンドを使用するために、必ず最新の Azure CLI (0.9.8 以降) に更新してください。コンピューターに現在インストールされている Azure CLI のバージョンを確認するには、「`azure -v`」と入力します。

## 手順 1. Azure CLI の設定

### 1\.Azure CLI のインストール

Windows 用、Linux 用、MAC 用の Azure CLI をインストールできます。Azure CLI を使用して Azure DNS を管理するには、次の手順を完了しておく必要があります。詳しくは、「[Azure CLI のインストール](../xplat-cli-install.md)」をご覧ください。DNS コマンドを使用するには、Azure CLI バージョン 0.9.8 以上が必要です。

CLI のすべてのネットワーク プロバイダー コマンドは、次のコマンドを使用して確認できます。

	Azure network

### 2\.CLI モードの切り替え

Azure DNS では、Azure リソース マネージャーを使用します。ARM コマンドを使用するように CLI モードを切り替えます。

	Azure config mode arm

### 3\.Azure アカウントへのサインイン

資格情報を使用して認証を行うように求めるメッセージが表示されます。使用できるのは ORGID アカウントだけであることに留意してください。

    Azure login -u "username"

### 4\.サブスクリプションの選択

使用する Azure サブスクリプションを選択します。

    Azure account set "subscription name"

### 5\.リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

    Azure group create -n myresourcegroup --location "West US"


### 6\.Register

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。この操作は、サブスクリプションごとに 1 回だけ実行します。

	Azure provider register --namespace Microsoft.Network


## 手順 2. DNS ゾーンの作成

DNS ゾーンは、`azure network dns zone create` コマンドを使用して作成します。必要に応じて、DNS ゾーンと同時にタグを作成できます。タグは名前と値のペアのリストであり、Azure Resource Manager では、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)を参照してください。

Azure DNS では、ゾーン名を指定する際に末尾に '**.**' を付けないでください。たとえば、'**contoso.com.**' ではなく '**contoso.com**' にします。


### DNS ゾーンを作成するには

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。

この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

    Azure network dns zone create myresourcegroup contoso.com

### DNS ゾーンとタグを作成するには

Azure DNS CLI では、オプションの *-Tag* パラメーターを使用して DNS ゾーンのタグを指定できます。次の例では、project = demo と env = test の 2 つのタグを含む DNS ゾーンの作成方法を示します。

次の例の値を実際の値と置き換えて、DNS ゾーンとタグを作成できます。

	Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## レコードの表示

DNS ゾーンを作成すると、次の DNS レコードも作成されます。

- 'Start of Authority' (SOA) レコード。このレコードは、すべての DNS ゾーンのルートに存在します。

- 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

このレコードを表示するには、`azure network dns-record-set show` を使用します。<BR> *使用法: network dns record-set show <resource-group> <dns-zone-name> <name> <type>*


次の例で、リソース グループ *myresourcegroup*、レコード セット名 *"@"* (ルート レコード)、タイプ *SOA* でコマンドを実行すると、次の出力が生成されます。


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
<BR>ゾーンで作成された NS レコードを表示するには、次のコマンドを使用します。

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

>[AZURE.NOTE] DNS ゾーンのルート (または*頂点*) にあるレコード セットは、レコード セット名として **@** を使用します。

## テスト

DNS ゾーンをテストするには、nslookup、DIG、`Resolve-DnsName` PowerShell コマンドレットなどの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、上の "azure network dns-record-set show" で一覧表示されているように、NS レコードで与えられます。次のコマンドを実際のゾーンの正しい値に置き換えてください。

次の例では、DIG を使用し、DNS ゾーンに割り当てられたネーム サーバーを使用して、ドメイン contoso.com をクエリします。クエリでは、*@<name server for the zone>* を使用したネーム サーバーとゾーン名を、DIG を使用して指定する必要があります。

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

DNS ゾーンを作成したら、[レコード セットとレコード](dns-getstarted-create-recordset-cli.md)を作成し、インターネット ドメインの名前解決を開始します。

<!---HONumber=AcomDC_0511_2016-->