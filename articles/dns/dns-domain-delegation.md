<properties
   pageTitle="Azure DNS へのドメインの委任 |Microsoft Azure"
   description="ドメインの委任を変更し、ドメインのホストに Azure DNS ネーム サーバーを使用する方法を説明します。"
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="joaoma"/>


# Azure DNS へのドメインの委任

Azure DNS は DNS ドメインのホスティング サービスです。ドメインに対する DNS クエリを Azure DNS に到達させるには、ドメインを親ドメインから Azure DNS に委任する必要があります。この記事では、ドメインの委任のしくみとドメインを Azure DNS に委任する方法について説明します。


## DNS 委任のしくみ

### ドメインとゾーン

ドメインとは、"contoso.com" など、ドメイン ネーム システム内で一意の名前です。ドメイン レジストラーは、インターネット ドメイン名を提供できる企業です。ドメイン レジストラーは、ユーザーが希望するインターネット ドメインが使用可能かどうかを確認し、購入を許可します。ドメイン名が登録されると、そのユーザーはドメイン名の法律上の所有者になります。既にインターネット ドメインを所有している場合は、現在のドメイン レジストラーを使用して、Azure DNS に委任します。

>[AZURE.NOTE]特定のドメイン名の所有者の詳細や、ドメインの購入方法の詳細については、「[Internet domain management in Azure AD (Azure AD でのインターネット ドメイン管理)](https://msdn.microsoft.com/library/azure/hh969248.aspx)」を参照してください。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。たとえば、ドメイン "contoso.com" には、"mail.contoso.com" (メール サーバー用) や "www.contoso.com" (Web サイト用) など、多数の DNS レコードが含まれている場合があります。

Azure DNS を使用すると、DNS ゾーンをホストできるため、Azure でドメインの DNS レコードを管理できます。Azure DNS はドメイン レジストラーではないことに注意してください。

ドメイン ネーム システムはドメインの階層構造です。階層は、"root" ドメインから始まり、その名前は単純に "." です。その下には "com"、"net"、"org"、"uk"、"jp" などのトップ レベル ドメインがあります。さらに、これらの下には "org.uk" や "co.jp" などの第 2 レベル ドメインがあります。同様に続きます。

DNS 階層内のドメインのホストには、個別の DNS ゾーンを使用します。これらのゾーンはグローバルに分散していて、世界中の DNS ネーム サーバーでホストされています。

### 解決と委任

DNS サーバーには次の 2 種類があります。

- _権限のある_ DNS サーバーは、DNS ゾーンをホストします。このサーバーは、これらのゾーン内のレコードに対する DNS クエリのみに応答します。
- _再帰_ DNS サーバーは、DNS ゾーンをホストしません。このサーバーは、権限のある DNS サーバーを呼び出して必要なデータを収集することで、すべての DNS クエリに応答します。

PC やモバイル デバイスの DNS クライアントは、通常、クライアント アプリケーションが必要とする DNS クエリを実行するために、再帰 DNS サーバーを呼び出します。

再帰 DNS サーバーは、"www.contoso.com" などの DNS レコードに対するクエリを受け取ると、まず、"contoso.com" ドメインのゾーンをホストするネーム サーバーを検索する必要があります。そのためには、ルート名サーバーから開始し、そこから、"com" ゾーンをホストするネーム サーバーを見つけます。次に、"com" ネーム サーバーを照会し、"contoso.com" ゾーンをホストするネーム サーバーを見つけます。最後に、"www.contoso.com" についてこれらのネーム サーバーを照会できます。

これは DNS 名の解決と呼ばれます (厳密に言えば、DNS 解決には、次の CNAME などのその他の手順が含まれますが、DNS の委任のしくみを理解するには重要ではありません)。

親ゾーンが子ゾーンのネーム サーバーを "指す" には、 NS レコード (NS は "ネーム サーバー" の略) と呼ばれる特殊な種類の DNS レコードを使用します。たとえば、ルート ゾーンには "com" の NS レコードが格納されます。このレコードは "com" ゾーンのネーム サーバーを示します。同様に、"com" ゾーンには "contoso.com" の NS レコードが格納されます。このレコードは "contoso.com" ゾーンのネーム サーバーを示します。親ゾーンで子ゾーンの NS レコードを設定することを、ドメインの委任と呼びます。


![Dns-nameserver](./media/dns-domain-delegation/image1.png)

実際に、各委任には、NS レコードの 2 つのコピーがあります。1 つは親ゾーン内で子ゾーンを指すレコード、もう 1 つは子ゾーン自体にあるレコードです。"contoso.com" ゾーンには、("com" 内の NS レコードだけでなく) "contoso.com" の NS レコードも格納されています。これらは権限のある NS レコードと呼ばれ、子ゾーンの頂点に配置されます。


## Azure DNS へのドメインの委任

Azure DNS 内に DNS ゾーンを作成した後は、Azure DNS がゾーンの名前解決のための権限のあるソースになるように、親ゾーン内の NS レコードを設定する必要があります。レジストラーから購入したドメインの場合は、レジストラーから、これらの NS レコードを設定するオプションが提供されます。

>[AZURE.NOTE]Azure DNS 内にそのドメイン名で DNS ゾーンを作成するには、ドメインを所有する必要はありません。ただし、レジストラーを使用して Azure DNS への委任を設定するには、ドメインを所有する必要があります。

たとえば、ドメイン "contoso.com" を購入し、Azure DNS に "contoso.com" という名前のゾーンを作成するとします。レジストラーは、ドメインの所有者として、ドメインのネーム サーバー アドレス (つまり、NS レコード) を構成するオプションを提供します。レジストラーはこれらの NS レコードを親ドメイン (この場合は ".com") に格納します。その後、世界中のクライアントは、"contoso.com" 内の DNS レコードを解決しようとすると、Azure DNS ゾーン内のドメインに転送されます。

委任を設定するには、ゾーンのネーム サーバー名を把握する必要があります。Azure DNS は、ゾーンが作成されるたびに、プールからネーム サーバーを割り当て、ゾーン内で自動的に作成される権限のある NS レコードにこれらを格納します。したがって、ネーム サーバー名を確認するには、これらのレコードを取得するだけです。

Azure PowerShell を使用すると、権限のある NS レコードを次の方法で取得できます (レコード名 "@" は、ゾーンの頂点にあるレコードを指すために使用されています)。

	PS C:\> $zone = Get-AzureDnsZone –Name contoso.com –ResourceGroupName MyAzureResourceGroup
	PS C:\> Get-AzureDnsRecordSet –Name “@” –RecordType NS –Zone $zone

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-04.azure-dns.com, ns2-04.azure-dns.net, ns3-04.azure-dns.org,
                     ns4-04.azure-dns.info}
	Tags              : {}

この例では、ゾーン "contoso.com" に、ネーム サーバー "ns1-04.azure-dns.com"、"ns2-04.azure-dns.net"、"ns3-04.azure-dns.org"、"ns4-04.azure-dns.info" が割り当てられています。

各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。レジストラーの DNS 管理ページで、NS レコードを編集し、その NS レコードを、Azure DNS で作成されたレコードに置き換えます。

委任が完了したら、"nslookup" などのツールを使用してゾーンの SOA レコードを照会することで、名前解決が動作していることを確認できます (SOA レコードは、ゾーンの作成時にも自動的に作成されます)。

委任が正しく設定されている場合は、通常の DNS 解決プロセスでネーム サーバーが自動的に検出されるため、Azure DNS ネーム サーバーを指定する必要はありません。

	PS C:\> nslookup –type=SOA contoso.com

	Server: ns1-04.azure-dns.com
	Address: 208.76.47.4

	contoso.com
	primary name server = ns1-04.azure-dns.com
	responsible mail addr = msnhst.microsoft.com
	serial = 1
	refresh = 900 (15 mins)
	retry = 300 (5 mins)
	expire = 604800 (7 days)
	default TTL = 300 (5 mins)

## Azure DNS サブドメインの委任

Azure DNS で "contoso.com" を設定して委任した後、別の子ゾーン "partners.contoso.com" を設定する場合を考えてみます。これは、通常の委任と同様のプロセスに従います。

1. Azure DNS で子ゾーン "partners.contoso.com" を作成します。
2. 子ゾーンで権限のある NS レコードを検索し、Azure DNS で子ゾーンをホストするネーム サーバーを取得します。
3. 子ゾーンを指す親ゾーンで NS レコードを構成することで、子ゾーンを委任します。

唯一異なる点は、手順 3. で、ドメイン レジストラーを使用して NS レコードを設定するのではなく、Azure DNS の親ゾーン "contoso.com" に NS レコードを作成する必要があることです。

PowerShell の例を次に示します。最初に、親ゾーンと子ゾーンを作成します。リソース グループは同じでも異なっていてもかまいません。

	PS C:\> $parent = New-AzureDnsZone -Name contoso.com -ResourceGroupName RG1
	PS C:\> $child = New-AzureDnsZone -Name partners.contoso.com -ResourceGroupName RG1

次に、次の例で示すように、子ゾーンから権限のある NS レコードを取得します。

	PS C:\> $child_ns_recordset = Get-AzureDnsRecordSet -Zone $child -Name "@" -RecordType NS

最後に、対応する NS レコード セットを親ゾーンに作成し、委任を完了します (親ゾーンのレコード セット名は子ゾーンの名前と一致します。ここでは "partners" です)。

	PS C:\> $parent_ns_recordset = New-AzureDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
	PS C:\> $parent_ns_recordset.Records = $child_ns_recordset.Records
	PS C:\> Set-AzureDnsRecordSet -RecordSet $parent_ns_recordset

レジストラーを使用して委任したときと同様に、子ゾーンの SOA レコードを検索することで、すべてが正しく設定されていることを確認できます。

	PS C:\> nslookup –type=SOA partners.contoso.com

	Server: ns1-08.azure-dns.com
	Address: 208.76.47.8

	partners.contoso.com
		primary name server = ns1-08.azure-dns.com
		responsible mail addr = msnhst.microsoft.com
		serial = 1
		refresh = 900 (15 mins)
		retry = 300 (5 mins)
		expire = 604800 (7 days)
		default TTL = 300 (5 mins)

## 次のステップ

[DNS ゾーンの管理](../dns-operations-dnszones)

[DNS レコードの管理](../dns-operations-recordsets)

[Traffic Manager の概要](../traffic-manager-overview)

[.NET SDK を使用した Azure の操作の自動化](../dns-sdk)

[Azure DNS REST API リファレンス](https://msdn.microsoft.com/library/azure/mt163862.aspx)

<!---HONumber=Nov15_HO4-->