<properties
   pageTitle="Azure DNS の概要 | Microsoft Azure"
   description="Azure DNS の DNS ゾーンを作成する方法について説明します。これは、PowerShell を使用して DNS ドメインのホストを開始するための最初の DNS ゾーンを作成する手順です。"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# PowerShell を使用した DNS ゾーンの作成

> [AZURE.SELECTOR]
- [Azure ポータル](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)

この記事では、CLI を使用して DNS ゾーンを作成する手順を説明します。DNS ゾーンは、PowerShell または Azure ポータルを使用して作成することもできます。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>Etag とタグについて

### <a name="etags"></a>Etag

たとえば、2 人のユーザーまたは 2 つのプロセスが同時に DNS レコードを変更しようとするとします。どちらの変更が優先されるでしょうか。 また、優先された側は、他のユーザーまたはプロセスによって行われた変更を上書きしたことに気付くのでしょうか。

Azure DNS は、Etag を使用して同じリソースへの同時変更を安全に処理します。各 DNS リソース (ゾーンまたはレコード セット) には、関連付けられている Etag があります。リソースが取得されるときは、常に Etag も取得されます。リソースを更新する場合は、Azure DNS がサーバー上の Etag の一致を確認できるように Etag を返すこともできます。リソースを更新するたびに Etag が再生成されるため、Etag の不一致は同時変更が発生していることを示します。Etag は、既存のリソースがないことを確認するために、新しいリソースの作成時にも使用されます。

Azure DNS PowerShell は、既定で、ゾーンおよびレコード セットへの同時変更のブロックに Etag を使用します。オプションの *-Overwrite* スイッチを使用すると、Etag チェックを実行しないように設定できます。この場合、発生したすべての同時変更が上書きされます。

Azure DNS REST API のレベルでは、HTTP ヘッダーを使用して Etag を指定します。次の表に各ヘッダーの動作を示します。

|ヘッダー|動作|
|------|--------|
|なし|PUT は常に成功します (Etag チェックなし)。|
|If-match <etag>|PUT は、リソースが存在し、Etag が一致する場合にのみ成功します|
|If-match * | PUT はリソースが存在する場合にのみ成功します|
|If-none-match * |	PUT はリソースが存在しない場合にのみ成功します|

### <a name="tags"></a>タグ

タグは Etag とは異なります。タグは名前と値のペアのリストであり、Azure Resource Manager では、課金またはグループ化のためのリソースのラベル付けに使用されます。タグの詳細については、[タグを使用した Azure リソースの整理](../resource-group-using-tags.md)を参照してください。

Azure DNS PowerShell では、ゾーンとレコード セットの両方でタグをサポートしており、オプションの `-Tag` パラメーターを使用して指定します。


## 開始する前に

構成を開始する前に、以下がそろっていることを確認します。
	
- Azure サブスクリプション。Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
	
- Azure Resource Manager PowerShell コマンドレット (1.0 以降) の最新版をインストールする必要があります。PowerShell コマンドレットのインストールの詳細については、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」を参照してください。

## 手順 1 - サインインする

PowerShell コンソールを開き、アカウントに接続します。詳細については、「[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

接続するには、次のサンプルを参照してください。

	Login-AzureRmAccount

アカウントのサブスクリプションを確認します。

	Get-AzureRmSubscription 

使用するサブスクリプションを指定します。

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 手順 2 - リソース グループを作成する

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

	New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## 手順 3 - 登録する

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。この操作は、サブスクリプションごとに 1 回だけ実行します。

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## 手順 4 - DNS ゾーンを作成する

DNS ゾーンは、`New-AzureRmDnsZone` コマンドレットを使用して作成します。タグありまたはタグなしで DNS ゾーンを作成するための例が下にあります。タグの詳細については、この記事の[タグ](#tags)に関するセクションを参照してください。

>[AZURE.NOTE] Azure DNS では、ゾーン名を指定する際に末尾に **‘.’** を付けないでください。たとえば、'**contoso.com.**' ではなく '**contoso.com**' にします。

### DNS ゾーンを作成するには

次の例では、*MyResourceGroup* というリソース グループに *contoso.com* という DNS ゾーンを作成します。この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### タグのある DNS ゾーンを作成するには

次の例では、*project = demo* と *env = test* の 2 つのタグを含む DNS ゾーンの作成方法を示します。この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

	New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## レコードの表示

DNS ゾーンを作成すると、次の DNS レコードも作成されます。

- *Start of Authority* (SOA) レコード。このレコードは、すべての DNS ゾーンのルートに存在します。

- 権威ネーム サーバー (NS) レコード。このレコードは、どのネーム サーバーがゾーンをホストしているのかを表します。Azure DNS は、ネーム サーバーのプールを使用しているため、Azure DNS 内のゾーンによって、割り当てられるネーム サーバーは異なる場合があります。詳細については、「[Azure DNS へのドメインの委任](dns-domain-delegation.md)」を参照してください。

このレコードを表示するには、`Get-AzureRmDnsRecordSet` を使用します。

	Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}


DNS ゾーンのルート (または "*頂点*") にあるレコード セットは、レコード セット名として **@** を使用します。


## テスト

DNS ゾーンをテストするには、nslookup、dig、[Resolve-DnsName PowerShell コマンドレット](https://technet.microsoft.com/library/jj590781.aspx)などの DNS ツールを使用します。

Azure DNS の新しいゾーンを使用するためのドメインの委任をまだ行っていない場合は、ゾーンのネーム サーバーの 1 つに DNS クエリを直接送信する必要があります。ゾーンのネーム サーバーは、上の `Get-AzureRmDnsRecordSet` で一覧表示されているように、NS レコードで与えられます。次のコマンドを実際のゾーンの正しい値に置き換えてください。

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)


## 次のステップ

DNS ゾーンを作成したら、[レコード セットとレコード](dns-getstarted-create-recordset.md)を作成し、インターネット ドメインの名前解決を開始します。

<!---HONumber=AcomDC_0817_2016-->